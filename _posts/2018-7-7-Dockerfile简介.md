---
layout: post
title: Dockerfile简介
categories:
description: Dockerfile简介
keywords:
---
Dockerfile简介

官网链接：
https://docs.docker.com/engine/reference/builder/#shell

Dockerfile 是一个由一堆命令+参数构成的脚本，使用 docker build 即可执行脚本构建镜像，自动的去做一些事，主要用于进行持续集成。

Dockerfile 的格式统统为：


```
# Comment
INSTRUCTION arguments12
```
必须以 FROM BASE_IMAGE 开头指定基础镜像。

更详细的规范与说明请参考 Dockerfile reference。这里我们以基于上面的 joe/centos:v1 作为基础镜像，然后在根目录创建 a 目录为例。

创建Dockerfile文件名为a. 如下：

```

FROM joe/centos:v1
RUN mkdir a12
```
然后使用当前目录的Dockerfile创建镜像，不需要指定文件名
命令如下：


```
docker build -t runoob/ubuntu:v1 . 1
```
执行：


```
> docker build -t newfiledocker:v1 .
Sending build context to Docker daemon  3.584kB
Step 1/2 : FROM joe/centos:v1
---> 68e83119eefa
Step 2/2 : RUN mkdir a
---> Running in 1127aff5fbd3
Removing intermediate container 1127aff5fbd3
---> 25a8a5418af0
Successfully built 25a8a5418af0
Successfully tagged newfiledocker:v112345678910
```
新建基于 newfiledocker 的容器并在终端中打开，发现里面已经有 a 文件夹了。
```


> docker docker run -it newfiledocker:v1 /bin/bash
root@e3bd8ca19ffc:/# ls
a  bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var123

```

其他使用dockfile的方式

指定使用的dockerfile
使用命令

```

vim -f /soft/docker/Dockerfile
#输入dockerfile的内容，保存退出
docker build -t newfiledocker:v1 -f /soft/docker/Dockerfile123
```
使用url创建 Dockerfile 镜像
使用URL github.com/creack/docker-firefox 的 Dockerfile 创建镜像，命令如下：


```
docker build github.com/creack/docker-firefox1
```
也就是说 我们找到很多资源里公布的dockerfile就可以直接远程创建。

在 Docker Store 上有非常多的高质量的官方镜像，有可以直接拿来使用的服务类的镜像，如 nginx、redis、mongo、mysql、httpd、php、tomcat 等；也有一些方便开发、构建、运行各种语言应用的镜像，如 node、openjdk、python、ruby、golang 等。可以在其中寻找一个最符合我们最终目标的镜像为基础镜像进行定制。

如果没有找到对应服务的镜像，官方镜像中还提供了一些更为基础的操作系统镜像，如 ubuntu、debian、centos、fedora、alpine 等，这些操作系统的软件库为我们提供了更广阔的扩展空间。

除了选择现有镜像为基础镜像外，Docker 还存在一个特殊的镜像，名为 scratch。这个镜像是虚拟的概念，并不实际存在，它表示一个空白的镜像。

FROM scratch
…
如果你以 scratch 为基础镜像的话，意味着你不以任何镜像为基础，接下来所写的指令将作为镜像第一层开始存在。

不以任何系统为基础，直接将可执行文件复制进镜像的做法并不罕见，比如 swarm、coreos/etcd。对于 Linux 下静态编译的程序来说，并不需要有操作系统提供运行时支持，所需的一切库都已经在可执行文件里了，因此直接 FROM scratch 会让镜像体积更加小巧。使用 Go 语言 开发的应用很多会使用这种方式来制作镜像，这也是为什么有人认为 Go 是特别适合容器微服务架构的语言的原因之一。
借助 Dockerfile 的能力，Docker 留下了无限的可能。



dockfile和docker commit的区别

我们知道使用docker commit 可以保留对镜像的修改，将其保存下来形成镜像。

格式



docker commit $container_id $image_name1

例如：


```
$ sudo docker ps
ID                  IMAGE               COMMAND             CREATED             STATUS              PORTS
c3f279d17e0a        ubuntu:12.04/bin/bash           7 days ago          Up25 hours
197387f1b436        ubuntu:12.04/bin/bash           7 days ago          Up25 hours
$ docker commit c3f279d17e0a  SvenDowideit/testimage:version3
f5283438590d
$ docker images | head
REPOSITORY                        TAG                 ID                  CREATED             VIRTUAL SIZE
SvenDowideit/testimage            version3            f5283438590d        16 seconds 123456789
```
docker commit 是在原有镜像的基础上，再叠加上容器的存储层，并构成新的镜像。以后我们运行这个新镜像的时候，就会拥有原有容器最后的文件变化。

这种方式做镜像比较方便，但是项目的其它成员就不太清楚这个镜像的具体生成细节，不易维护。

慎用 docker commit
使用 docker commit 命令虽然可以比较直观的帮助理解镜像分层存储的概念，但是实际环境中并不会这样使用。

首先，如果使用docker diff 仔细观察之前的结果，你会发现除了真正想要修改的文件外，由于命令的执行，还有很多文件被改动或添加了。这还仅仅是最简单的操作，如果是安装软件包、编译构建，那会有大量的无关内容被添加进来，如果不小心清理，将会导致镜像极为臃肿。

此外，使用 docker commit 意味着所有对镜像的操作都是黑箱操作，生成的镜像也被称为黑箱镜像，换句话说，就是除了制作镜像的人知道执行过什么命令、怎么生成的镜像，别人根本无从得知。而且，即使是这个制作镜像的人，过一段时间后也无法记清具体在操作的。虽然 docker diff 或许可以告诉得到一些线索，但是远远不到可以确保生成一致镜像的地步。这种黑箱镜像的维护工作是非常痛苦的。

而且，回顾之前提及的镜像所使用的分层存储的概念，除当前层外，之前的每一层都是不会发生改变的，换句话说，任何修改的结果仅仅是在当前层进行标记、添加、修改，而不会改动上一层。如果使用 docker commit 制作镜像，以及后期修改的话，每一次修改都会让镜像更加臃肿一次，所删除的上一层的东西并不会丢失，会一直如影随形的跟着这个镜像，即使根本无法访问到。这会让镜像更加臃肿。



更多dockfile命令

FROM：指定待扩展的父级镜像(基础镜像)。除了注释以外，在文件开头必须是一个FROM指令，接下来的指令便在这个父级镜像的环境中运行，直到遇到下一个FROM指令。通过添加多个FROM命令，可以在同一个Dockerefile文件中创建多个镜像。

MAINTAINER：声明创建的镜像的作者信息。用户名、邮箱。非必须。

RUN：用来修改镜像的命令，常用来安装库、程序以及配置程序。一条RUN指令执行完毕后，会在当前镜像上创建一个新的镜像层，接下来对的指令会在新的镜像上继续执行。RUN 语句有两种形式：
```
RUN yum update：是在/bin/sh环境中执行的指令的命令

RUN [“yum”, “update”]：直接使用系统调用exec来执行行。

RUN yum update && yum install nginx：使用&&符号将多条命令连接在同一条RUN语句中。
```
EXPOSE：用来指明容器内进程对外开放的端口，多个端口之间使用空格隔开。运行容器时，通过参数-P(大写)即可将EXPOSE里所指定的端口映射到主机上另外的随机端口，其他容器或主机就可以通过映射后的端口与此容器通信。同时，我们也可以通过-p(小写)参数将Dockerfile中EXPOSE中没有列出的端口设置成公开的。

ADD：向新镜像中添加文件，这个文件可以是一个主机文件，也可以是一个网络文件，也可以使一个文件夹。

第一个参数：源文件（夹）。如果是相对路径，它必须是相对于Dockerfile所在目录的相对路径。如果是URL，会先下载下来，再添加到镜像里去。

第二个参数：目标路径。如果源文件是主机上zip或者tar形式的压缩文件，Docker会先解压缩，然后将文件添加到镜像的指定位置。如果源文件是一个通过URL指定的网络压缩文件，则不会解压。

VOLUME：在镜像里创建一个指定路径(文件或文件夹)的挂载点，这个容器可以来自主机或者其它容器。多个容器可以通过同一个挂载点共享数据，即便其中一个容器已经停止，挂载点也仍热可以访问。

WORKDIR：为接下来执行的指令指定一个新的工作目录，这个目录可以使绝对目录，也可以是相对目录。根据需要，WORKDIR可以被多次指定。当启动一个容器时，最后一条WORKDIR指令所指的目录将作为容器运行的当前工作目录。

ENV：设置容器运行的环境变量。在运行容器的时候，通过-e参数可以修改这个环境变量值，也可以添加新的环境变量：

```
docker run -e WEBAPP_PORT=8000 -e WEBAPP_HOST=www.example.com …
```
CMD：用来设置启动容器时默认运行的命令。

ENTRYPOINT：与CMD类似，也是用来指定容器启动时的默认运行的命令。区别在于：运行容器时添加在镜像之后的参数，对ENTRYPOINT是拼接，CMD是覆盖。
```
ENTRYPOINT [ “ls”, “-l”]

docker run centos ==> docker run centos ls -l

docker run centos -a ==> docker run centos ls -l -a
```
我们在运行容器的时候可以通过–entrypoint来覆盖Dockerfile中的指定：docker run gutianlangyu/test –entrypoint echo “hello world”
USER：为容器的运行及接下来RUN、CMD、ENTRYPOINT等指令的运行指定用户或UID。

ONBUILD：触发器指令。构建镜像时，Docker的镜像构建器会将所有的ONBUILD指令指定的命令保存到镜像的元数据中，这些命令在当前镜像的构建过程中并不会执行。只有心的镜像使用FROM指令指定父镜像为这个镜像时，便会触发执行。

使用FROM以这个Dockerfile构建出的镜像为父镜像，构建子镜像时：

```ONBUILD ADD . /app/src：自动执行ADD . /app/src

```

dockfile格式实例


```
#Version 1.0.1
FROM centos:latest

MAINTAINER ***u "***u@163.com"

#设置root用户为后续命令的执行者
USER root

#执行操作
RUN yum update -y
RUN yum install -y java

#使用&&拼接命令
RUN touch test.txt && echo "abc" >>abc.txt

#对外暴露端口
EXPOSE 80 8080 1038

#添加文件
ADD abc.txt /opt/

#添加文件夹
ADD /webapp /opt/webapp

#添加网络文件
ADD https://www.baidu.com/img/bd_logo1.png /opt/

#设置环境变量
ENV WEBAPP_PORT=9090

#设置工作目录
WORKDIR /opt/

#设置启动命令
ENTRYPOINT ["ls"]

#设置启动参数
CMD ["-a", "-l"]

#设置卷
VOLUME ["/data", "/var/www"]

#设置子镜像的触发操作
ONBUILD ADD . /app/src
ONBUILD RUN echo "on build excuted" >> onbuild.txt
---------------------
```
