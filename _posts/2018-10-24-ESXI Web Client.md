---
layout: post
title: VMware vSphere client
categories:
description: VMware vSphere client
keywords:
---

VMware vSphere client，用来连接VMware ESXi主机和vCenter server，相对于管理软件，界面呈现和workstation差不多，但功能上要高于workstation。

ESXI安装完成之后，配置好ip，我们就可以使用了？那么如何使用呢?一般来说有两种方式，一种是安装专门的管理客户端，client端，另一种更加方便，就是使用web client端。

下面来介绍一下esxi的web端管理界面

首先，我们要知道自己esxi服务器的ip，然后浏览器直接输入ip地址

当前页面选择open the vmware host client

进入登陆界面

输入安装时设置好的用户名密码然后进入主界面

管理界面是比较简单的，在当前界面，我们可以看到当前服务器的硬件信息，使用情况，服务开启情况等。

然后是manage界面，在这个界面我们可以做一些管理的操作，比如下载个插件啊，换个许可证啊，修改一下系统时间啊之类的。

Monitor界面就是一系列的检测数据，cpu内存等的使用情况，任务执行情况，日志，报错，警报。你甚至可以导出系统的各种日志。


然后是三个实际使用的界面，第一个是Vitral Machines，虚拟机的管理界面，新建虚拟机，管理虚拟机等都在这个界面，如图

然后是Storage界面，对于一个安装虚拟机的服务器来说，硬盘空间是很重要的，在这里我们可以看硬盘的容量，使用情况，也可以查看虚拟硬盘和硬盘的硬件情况。
最后是网络管理界面，在这个界面我们可以查看网络的接口，网络设置，还可以设置防火墙规则等。

这就是单台的esxi的管理界面，当然esxi也可以组成集群，我们可以用另一个软件来管理我们的集群情况，那就是vSphere Web Client/vSphere Client，它的管理功能更加强大，而且更加详细。
---------------------
