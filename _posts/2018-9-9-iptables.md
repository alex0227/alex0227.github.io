---
layout: post
title: iptables
categories:  iptables
description: iptables
keywords:  iptables
---

linux开放关闭防火墙端口

添加防火墙规则
iptables -A INPUT -P udp --dport 69 -j ACCEPT
保存修改
/etc/rc.d/init.d/iptables save  
重启服务
/etc/rc.d/init.d/iptables restart
查看端口状态
/etc/init.d/iptables status  

查看iptables规则带编号
iptables -L -n --line-number

删除制定编号的规则
 iptables -D INPUT 编号


 1) 重启后生效
开启： chkconfig iptables on
关闭： chkconfig iptables off

2) 即时生效，重启后失效
开启： service iptables start
关闭： service iptables stop

查看iptables文件
    vim /etc/sysconfig/iptables  

    关闭所有的INPUT FORWARD（转发） OUTPUT的所有端口
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP

参数讲解：
–A 参数就看成是添加一条规则
–p 指定是什么协议，我们常用的tcp 协议，当然也有udp，例如53端口的DNS
–dport 就是目标端口，当数据从外部进入服务器为目标端口
–sport 数据从服务器出去，则为数据源端口使用
–j 就是指定是 ACCEPT -接收 或者 DROP 不接收

禁止某个IP访问
iptables -A INPUT -p tcp -s 192.168.1.2 -j DROP

–s 参数是来源（即192.168.1.2）
后面拒绝就是DROP
