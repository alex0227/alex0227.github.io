---
layout: post
title: Linux下远程桌面连接windows
categories:
description: Linux下远程桌面连接windows
keywords:
---
Linux下远程桌面连接windows

## windows配置：
首先打开windows的远程连接服务，允许用户远程连接到本机：
右键 -> 我的电脑 -> 属性 -> 远程 -> 允许用户远程连接到此计算机


##  Linux配置：
这里我们需要安装rdesktop和tsclient，其中rdesktop是基于命令行的工具，tsclient只是一个图形化的界面，依赖于rdesktop。
```
yum install rdesktop
yum install tsclient
```
使用rdesktop远程windows主机
```
rdesktop -f -a 16 119.0.0.100 即可。参数请man rdesktop查询
```
下面来看一下rdesktop怎么使用吧，开个终端吧
```
$info rdesktop   //看一下帮助信息吧
$rdesktop 192.168.1.1 //打开了一个8位色彩的，
$rdesktop -a 16 192.168.1.1 //这个是16位色彩的了，看起来好多了
$rdesktop -u administrator -p ****** -a 16 192.168.1.1 //都直接登陆了，呵,还差点什么呢
```
还有就是 －f 全屏操作，－g 指定使用屏幕大小 －g 800*600+0+0 这个＋0啊就是，就是你
这个窗口的在你Linux上出现的位置，
其它没什么了吧!加上-r sound:local可以把声音也搞过来了
```
$rdesktop -u administrator -p ****** -a 16 -r sound:local 192.168.1.1
```
其它吧,-r 的作用挺多的可以重定向许多东西
```
-r comport:COM1=/dev/ttyS0    // 将串口 /dev/ttyS0 重定向为 COM1
-r comport:COM1=/dev/ttyS0,COM2=/dev/ttyS1    // 多个串口重定向
-r disk:floppy=/mnt/floppy    // 将 /mnt/floppy 重定向为远程共享磁盘 'floppy'
-r disk:floppy=/mnt/floppy,cdrom=/mnt/cdrom,root=/,c=/mnt/c    // 多个磁盘重定向
-r clientname=<client name>    // 为重定向的磁盘设置显示的客户端名称
-r lptport:LPT1=/dev/lp0    // 将并口 /dev/lp0 重定向为 LPT1
-r lptport:LPT1=/dev/lp0,LPT2=/dev/lp1    // 多个并口重定向
-r printer:mydeskjet    // 打印机重定向
-r printer:mydeskjet="HP LaserJet IIIP"    // 打印机重定向
-r sound:[local|off|remote]    // 声音重定向
rdesktop是linux下一个好用的用来连接Windows远程桌面（当然不仅仅在于此，只要是基于RDP协议的好像都行吧），传说中的3389嘛。
```
不过他是一个基于命令行的，对某些人来说可能有一些困难，这里就集合了一些很不错的命令，共享一下。
最简单的：
```
rdesktop ip
```
这个最实用，如果其他的你还没有掌握那就用这个吧，这个都是默认参数。
如果你想全屏：
```
rdesktop -f ip
```
这样已经很不错了，可以满足很多人了。这里一个最重要的东西，退出全屏，是什么呢？（很多初学者都对这个进去就不能会Linux的家伙很郁闷，）是Ctrl+Alt+Enter。
```
rdesktop -f sound:local ip
```
这个是把远程主机的声音带到本机（用过windows远程桌面的都知道）
```
rdesktop -f -r clipboard:PRIMARYCLIPBOARD sound:local ip
-r clipboard:PRIMARYCLIPBOARD是允许在远程主机和本机之间共享剪切板，就是可以复制粘贴。
rdesktop -f -r disk:MyDisk=/home/comet/temp ip
-r disk:MyDisk=/home/comet/temp就是把你的Linux下某个文件夹挂载到远程主机上
```
 使用tsclient添加远程主机


这里有四个选项，可以根据你所需要的去添加，咱们这里添加就是Windows Terminal Service

将你需要远程主机的信息添加上就ok了！！！！！
