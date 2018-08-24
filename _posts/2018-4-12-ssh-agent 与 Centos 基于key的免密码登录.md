---
layout: post
title: ssh-agent 与 Centos 基于key的免密码登录
categories: ssh-agent 与 Centos 基于key的免密码登录
description: ssh-agent 与 Centos 基于key的免密码登录
keywords: ssh-agent Centos 基于key的免密码登录
---

SSH 为 Secure Shell 的缩写，由 IETF 的网络小组（Network Working Group）所制定；SSH 为建立在应用层基础上的安全协议。SSH是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用 SSH 协议可以有效防止远程管理过程中的信息泄露问题。

传统的网络服务程序，如：ftp、pop和telnet在本质上都是不安全的，因为它们在网络上用明文传送口令和数据，别有用心的人非常容易就可以截获这些口令和数据。而且，这些服务程序的安全验证方式也是有其弱点的， 就是很容易受到“中间人”（man-in-the-middle）这种方式的\*\*\*。所谓“中间人”的\*\*\*方式， 就是“中间人”冒充真正的服务器接收你传给服务器的数据，然后再冒充你把数据传给真正的服务器。服务器和你之间的数据传送被“中间人”一转手做了手脚之后，就会出现很严重的问题。通过使用SSH，你可以把所有传输的数据进行加密，这样"中间人"这种***方式就不可能实现了，而且也能够防止DNS欺骗和IP欺骗。使用SSH，还有一个额外的好处就是传输的数据是经过压缩的，所以可以加快传输的速度。SSH有很多功能，它既可以代替Telnet，又可以为FTP、PoP、甚至为PPP提供一个安全的"通道"。

OpenSSH 是 SSH （Secure SHell） 协议的免费开源实现。OpenSSH提供了服务端后台程序和客户端工具，用来加密远程控制和文件传输过程中的数据，并由此来代替原来的类似服务。

**ssh连接服务器慢的解决方法**
```
编辑以下文件
ssh服务端
配置文件/etc/ssh/sshd_config
把以下2项修改为,并重启ssh服务
UseDNS no
GSSAPIAuthentication no
```
**ssh客户端**
配置文件：/etc/ssh/ssh_config
语法：
```
ssh [user@]host [命令]  远程执行命令后返回信息并退出
    -p port：远程服务器监听的端口
    -b:指定连接的源IP
    -v:调试模式
    -C：压缩方式
    -X: 支持x11(图形)转发
    -Y：支持信任x11转发
    ForwardX11Trusted yes
    -t: 强制伪tty分配
ssh -t 跳板机IP ssh 最终访问IP
#ssh -t 192.168.4.101 ssh 192.168.4.113
```

## ssh服务登录验证方式
基于用户和口令登录验证

![](/images/posts/ssh免密登陆/1.png)

**基于密钥的登录方式**
```
1 首先在客户端生成一对密钥（ssh-keygen）
2 并将客户端的公钥ssh-copy-id 拷贝到服务端
3 当客户端再次发送一个连接请求，包括ip、用户名
4 服务端得到客户端的请求后，会到authorized_keys中查找，如果有响应的IP和用户，就会随机生成一个字符串，例如：acdf
5 服务端将使用客户端拷贝过来的公钥进行加密，然后发送给客户端
6 得到服务端发来的消息后，客户端会使用私钥进行解密，然后将解密后的字符串发送给服务端
7 服务端接受到客户端发来的字符串后，跟之前的字符串进行对比，如果一致，就允许免密码登录
```
## 基于密钥的认证实现步骤
```
命令语法

生成密钥对
ssh-keygen -t rsa [-P ''] [-f "~/.ssh/id_rsa"]
ssh-keygen -t 加密算法 -P '加密口令' -f "指定生成密钥位置"   这个是大写的P

公钥文件传输至远程服务器
ssh-copy-id [-i [identity_file]] [user@]host
ssh-copy-id -i 指定密钥 远程用户@远程主机

根据私钥生成公钥
#ssh-keygen -f id_rsa -y
-y选项表示根据私钥生成对应的公钥，生成的公钥会打印在屏幕中，可以使用重定向生成公钥文件

修改现有私钥的密码
小写的p是修改密码
#ssh-keygen -f id_rsa -p
```

步骤：
```
1.在客户端生成密钥对
#ssh-keygen
#cat id_rsa
id_rsa      id_rsa.pub

2.把公钥文件传输至远程服务器对应用户的家目录
#ssh-copy-id -i id_rsa.pub root@192.168.4.100
如果在COPY的时候不小心把公钥写成了私钥，放心，实际执行时只会复制公钥。

会在用户的家目录的.ssh/目录生成authorized_keys文件

3. 测试
#ssh root@192.168.4.100

4.实现了免密登录了
```
```
注意：如果不想再基于key认证了，可以把~/.ssh/authorized_keys文件删除
```

**基于key的免密码登录安全防范**  
私钥很重要，等于身份的确认，一但私钥丢失，风险是极高的。可是，好不容易刚整完了免密码登录，如果对私钥进行加密码，每次使用都需要输入密码，岂不是给便利性带来了麻烦？

**ssh-agent**

它可以帮助我们管理私钥：  
1.在使用不同的密钥连接到不同的主机时，ssh代理可以帮助我们选择对应的密钥进行认证，不用手动指定密钥即可进行连接。  
2.当私钥设置了密码，ssh代理可以帮助我们免去重复的输入密码的操作。
```
基于Linux的步骤如下：
1.先完成以上的基于key验证的步骤
2.启动ssh-agent
    # eval `ssh-agent`
3.将私钥添加到ssh代理
    # ssh-add 私钥名
4.测试
```
基于Windows的 xshell 步骤如下：

![](/images/posts/ssh免密登陆/2.jpg)
![](/images/posts/ssh免密登陆/3.jpg)
![](/images/posts/ssh免密登陆/4.jpg)
![](/images/posts/ssh免密登陆/5.jpg)
![](/images/posts/ssh免密登陆/6.jpg)
![](/images/posts/ssh免密登陆/7.jpg)
![](/images/posts/ssh免密登陆/8.jpg)

### 以下为ssh-agent命令的一些常用选项。
启动与关闭ssh-agent
```
方法一：
ssh-agent bash
在子shell中打开ssh-agent，退出子shell自动结束代理

方法二：
eval `ssh-agent`
在当前shell中打开ssh-agent，退出当前shell时最好使用ssh-agent -k关闭对应代理
```
**将私钥添加到ssh代理**
```
ssh-add 私钥名
#ssh-add id_rsa
Enter passphrase for id_rsa: 密钥口令
Identity added: id_rsa (id_rsa)
```
**查看代理中的私钥**
```
ssh-add -l
```
**查看代理中的私钥对应的公钥**
```
ssh-add -L
```
**移除指定的私钥**
```
ssh-add -d 私钥名

#ssh-add -d id_rsa
Identity removed: id_rsa (root@7-ansible-0)
```
**移除代理中的所有私钥**
```
ssh-add -D

#ssh-add -D
All identities removed.
```
