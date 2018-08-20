---
layout: post
title: redhat设置防火墙，selinux
categories: redhat
description: redhat设置防火墙，selinux
keywords: redhat，防火墙，selinux
---


## 防火墙

 1. 图形化工具firewall-config
    用图像化界面进行配置，记得选择permanent还是run-time
    配置完成点击options，reload firewalld
 2. firewall-cmd
 常用命令  
    1. 添加端口:   
 firewall-cmd --permanent --add-port=3260/tcp
    2. 设置端口转发：  
 firewall-cmd --permanent --add-forward-port=port=5423:proto=tcp:toport=80  
    3. 添加服务：  
 firewall-cmd --permanent --add-service=http  
    4. 添加富规则：  
        1. 端口控制：  
        firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="172.25.0.0/24" port protocol="tcp" port="3260" accept"  
        2. 服务控制：  
        firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="172.25.0.0/24" service name="http" accept"  
      5. 防火墙重启生效
        firewall-cmd --reload
## selinux   
  1. 查看selinux状态
  getenforce
  2. 临时设置
    关闭： getenforce=0
    打开： getenforce=1
    永久设置,编辑文件，命令如下:
    ```
    vim /etc/selinux/config
    ```
  3. sebool值
       1. getsebool -a  | grep httpd | grep user  
      setsebool -P httpd_read_user_content on
       2. semanage port -l | grep http  
  4. semanage  port -a -t http_port_t -p tcp 8998
