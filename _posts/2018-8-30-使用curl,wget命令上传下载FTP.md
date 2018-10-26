---
layout: post
title: 使用curl / wget命令上传下载FTP
categories: curl, wget
description: curl, wget
keywords: curl, wget
---

使用curl / wget命令上传下载FTP
curl可以在shell下轻松上传下载ftp上的文件，相比ftp命令更具有优势，因为它能在单命令条件下，下载或者上传一个ftp文件，甚至可以删除文件。

**下面看实例：**
1. 列出ftp服务器上的目录列表：
```
curl ftp://www.quany.info/ --user name:passwd
curl ftp://www.quany.info/ –u name:passwd#简洁写法
curl ftp://name:passwd@www.quany.info #简洁写法2
```
2. 只列出目录，不显示进度条
```
curl ftp://www.quany.info –u name:passwd-s
```
3. 下载一个文件：
```
curl ftp://www.quany.info/size.zip –u name:passwd-o size.zip
```
4. 上载一个文件：
```
curl –u name:passwd-T size.mp3 ftp://www.quany.info/mp3/
```
5. 从服务器上删除文件（使用curl传递ftp协议的DELE命令）:
```
curl –u name:passwdftp://www.quany.info/ -X 'DELE mp3/size.mp3'
```
6. 另外curl不支持递归下载，不过可以用数组方式下载文件，比如我们要下载1-10.gif连续命名的文件：
```
curl –u name:passwdftp://www.quany.info/img/[1-10].gif –O #O字母大写
```
7. 要连续下载多个文件：
```
curl –u name:passwdftp://www.quany.info/img/[one,two,three].jpg –O #O字母大写
```
8. wget下载文件：
用户账户:quany
用户密码:123456
ftp下载
```
wget ftp://quany:123456@www.quany.info/xxx.zip
```
http下载
```

wget --http-user=quany --http-passwd=123456 http://www.quany.info/xxx.zip
```
9. wget参数：
wget的参数较多，但大部分应用只需要如下几个常用的参数：
```
-r 递归；对于HTTP主机，wget首先下载URL指定的文件，然后（如果该文件是一个HTML文档的话）递归下载该文件所引用（超级连接）的所有文件（递归深度由参数-l指定）。对FTP主机，该参数意味着要下载URL指定的目录中的所有文件，递归方法与HTTP主机类似。
-N 时间戳：该参数指定wget只下载更新的文件，也就是说，与本地目录中的对应文件的长度和最后修改日期一样的文件将不被下载。
-m 镜像：相当于同时使用-r和-N参数。
-l 设置递归级数；默认为5。-l1相当于不递归；-l0为无穷递归；注意，当递归深度增加时，文件数量将呈指数级增长。
-t 设置重试次数。当连接中断（或超时）时，wget将试图重新连接。如果指定-t0，则重试次数设为无穷多。
-c 指定断点续传功能。实际上，wget默认具有断点续传功能，只有当你使用别的ftp工具下载了某一文件的一部分，并希望wget接着完成此工作的时候，才需要指定此参数。
```
使用举例：
```
wget -m -l4 -t0 http://www.quany.info/
```
将在本地硬盘建立http://www.quany.info/的镜像，镜像文件存入当前目录下一个名为www.quany.info的子目录中（你也可以使用-nH参数指定不建立该子目录，而直接在当前目录下建立镜像的目录结构），递归深度为4，重试次数为无穷（若连接出现问题，wget将坚韧不拔地永远重试下去，知道任务完成！）
另外一些使用频率稍低的参数如下
```
-A acclist / -R rejlist：
这两个参数用于指定wget接受或排除的文件扩展名，多个名称之间用逗号隔开。例如，假设我们不想下载MPEG视频影像文件和.AU声音文件，可使用如下参数：
-R mpg,mpeg,au
其它参数还有：
-L 只扩展相对连接，该参数对于抓取指定站点很有用，可以避免向宿主主机的其他目录扩散。例如，某个人网站地址为：http://www.quany.info/~ppfl/，使用如下命令行：

wget -L http://www.quany.info/~ppfl/
则只提取该个人网站，而不涉及主机www.quany.info上的其他目录。
-k 转换连接：HTML文件存盘时，将其中的非相对连接转换成为相对连接。
-X 在下载FTP主机上的文件时，排除若干指定的目录
另外，下面参数用于设置wget的工作界面：
-v 设置wget输出详细的工作信息。
-q 设置wget不输出任何信息。
```
