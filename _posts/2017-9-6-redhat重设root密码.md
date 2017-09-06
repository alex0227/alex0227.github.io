
---
layout: post
title: Redhat Linux重设root密码
categories: Redhat
description: Redhat Linux重设root密码
keywords: root密码
---


1、开机，当进入系统选择界面时候按e键
2、在Linux16那行末尾加上rd,break，按ctrl+x进入救援模式
3、输入

```
mount -o remount,rw /sysroot
chroot /sysroot
echo "要设置的密码" | passwd --stdin root
touch /.autorelabel
exit
reboot
```

4、等待系统重启
