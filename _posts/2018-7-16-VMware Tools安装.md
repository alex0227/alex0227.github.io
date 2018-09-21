---
layout: post
title: VMware Tools安装
categories:
description: VMware Tools安装
keywords:
---

## VMware Tools介绍：
VMware Tools是VMware虚拟机中自带的一种增强工具，相当于VirtualBox中的增强功能（Sun VirtualBox Guest Additions），是VMware提供的增强虚拟显卡和硬盘性能、以及同步虚拟机与主机时钟的驱动程序。   
只有在VMware虚拟机中安装好了VMware Tools，才能实现主机与虚拟机之间的文件共享，同时可支持自由拖拽的功能，鼠标也可在虚拟机与主机之间自由移动（不用再按ctrl+alt），且虚拟机屏幕也可实现全屏化。  

## VMware Tools安装：
环境：VMware Workstation 12
操作系统：centos6.9
在没有安装VMware Tools 之前，界面应该如下图所示，界面比较小，不能自动适应窗口大小，看起来很不好看，使用起来不方便

找到菜单：虚拟机—>安装VMware Tools，点击安装VMware Tools，之后后自动挂载。注意：安装VMware Tools之前不要挂载其他光盘



进入命令行终端进行安装VMware Tools

请在命令行终端运行下面的命令
```
[root@centos6 VMware Tools]# ls
manifest.txt run_upgrader.sh VMwareTools-10.0.0-2977863.tar.gz vmware-tools-upgrader-32 vmware-tools-upgrader-64
[root@centos6 VMware Tools]# mkdir /vmtools
[root@centos6 VMware Tools]# tar xvf VMwareTools-10.0.0-2977863.tar.gz -C /vmtools
[root@centos6 VMware Tools]# cd /vmtools/
[root@centos6 vmtools]# ls
vmware-tools-distrib
[root@centos6 vmtools]# cd vmware-tools-distrib/
```
执行vmware-install.pl--这是个perl语言写的脚本，之后一路回车即可
```
[root@centos6 vmware-tools-distrib]# ./vmware-install.pl
 ```
安装完成后，重启机器即可
```
[root@centos6 vmware-tools-distrib]# reboot
```
重启后界面自适应窗口大小，如下图所示：

到了这里，VMware Tools就安装完成了！
