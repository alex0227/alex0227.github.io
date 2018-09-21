---
layout: post
title: iptables
categories:  iptables
description: iptables
keywords:  iptables
---


添加防火墙规则
iptables -A INPUT -P udp --dport 69 -j ACCEPT

查看iptables规则带编号
iptables -L -n --line-number

删除制定编号的规则
 iptables -D INPUT 编号
