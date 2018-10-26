---
layout: post
title: 安装VMware ESXI 5.1的服务器
categories:
description: 安装VMware ESXI 5.1的服务器
keywords:
---


安装说明：
1、安装VMware ESXI 5.1的服务器CPU必须支持虚拟化，Intel (VT-X) 或者AMD (AMD-V)
2、服务器内存必须在2G以上，硬盘容量大于40G

安装：
1、下载VMware ESXI 5.1系统镜像文件
截止目前ESXI最新版本为：
VMware-VMvisor-Installer-5.1.0.update01-1065491.x86_64.iso
2、刻录ESXI镜像光盘，服务器从光驱启动，出现如下：

默认选中第一项nstall，回车安装

安装程序正在检测服务器硬件信息，如果不满足系统安装条件会跳出错误提示。
检测完成之后

回车continue

按F11  accept and continue

这里列出了服务器硬盘信息，默认回车，出现下面界面

键盘模式，默认即可，回车continue

服务器root账户密码设置（注意：密码长度7位以上）

按F11开始安装

正在安装中

安装完成，回车重新启动系统。

系统正在重启中

系统启动完成，下面对ESXI进行设置
按F2

输入root密码，回车
继续按F2，


切换到Configure Management Network选项，回车

选择network adapters进去选择激活状态的网卡，用空格键进行选择，enter键确认

选择IP Configuration 回车

用空格选择第二项，设置为静态IP
输入自己的IP、子网掩码、网关信息 回车

继续选择DNS Configuration 回车

用空格选中第二项，使用自定义的DNS，填写你的DNS 、Hostname修改为你需要的名字
回车

输入Y保存上面的配置信息

按ESC返回到主界面
选择restart network 进行网络重启

按F12重启系统

输入root密码 回车

按F11 回车
