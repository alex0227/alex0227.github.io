---
layout: post
title: MySQL root密码的恢复方法之一
categories:
description: MySQL root密码的恢复方法之一
keywords:
---

Linux:
MySQL root密码的恢复方法之一
如果忘记了MySQL root密码，可以用以下方法重新设置：
1.KILL掉系统里的MySQL进程；
    killall -TERM MySQLd
2.用以下命令启动MySQL，以不检查权限的方式启动；
    safe_MySQLd --skip-grant-tables &
3.然后用空密码方式使用root用户登录 MySQL；
    MySQL -u root
4.修改root用户的密码；
    MySQL> update MySQL.user set password=PASSWORD('新密码') where User='root';  
    MySQL> flush privileges;  
    MySQL> quit
重新启动MySQL，就可以使用新密码登录了
