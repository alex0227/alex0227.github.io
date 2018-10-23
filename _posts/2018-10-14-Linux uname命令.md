---
layout: post
title: Linux uname命令
categories:
description: Linux uname命令
keywords:
---


Linux uname命令用于显示系统信息。uname可显示电脑以及操作系统的相关信息。
语法参数：
```
uname [参数]
参数：
-a或--all：显示全部的信息；
-m或--machine：显示电脑类型；
-n或-nodename：显示在网络上的主机名称；
-r或--release：显示操作系统的发行编号；
-s或--sysname：显示操作系统名称；
-v：显示操作系统的版本；
-p或--processor：输出处理器类型或"unknown"；
-i或--hardware-platform：输出硬件平台或"unknown"；
-o或--operating-system：输出操作系统名称；
用法实例：
显示系统信息
 linuxidc@linuxidc:~$ uname -a
Linux linuxidc 4.18.3-041803-generic #201808180530 SMP Sat Aug 18 09:32:58 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
显示计算机类型
linuxidc@linuxidc:~$ uname -m
x86_64
显示计算机名
linuxidc@linuxidc:~$ uname -n
linuxidc
显示操作系统发行编号
linuxidc@linuxidc:~$ uname -r
4.18.3-041803-generic
显示操作系统名称
linuxidc@linuxidc:~$ uname -s
Linux
显示系统时间
linuxidc@linuxidc:~$ uname -v
#201808180530 SMP Sat Aug 18 09:32:58 UTC 2018
linuxidc@linuxidc:~$
```
