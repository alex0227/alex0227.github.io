---
layout: post
title: redhat配置yum仓库
categories: redhat
description: redhat配置yum仓库
keywords: yum,redhat,linux
---


## 配置yum仓库

 1. vim /etc/yum.repo.d/rhce.repo
 2. 输入信息
 ```
 [rhce]
 name = rhce
 baseurl = file:////media/cdrom
 enabled = 1
 gpgcheck = 0
 ```
 Yum软件仓库的作用是为了进一步简化RPM管理软件难度以及自动分析所需软件包及其依赖关系的技术。您可以把Yum想象成是一个硕大的软件仓库，里面保存有几乎所有常用的工具，而只需要说出所需的软件包名称，系统就会自动的为您搞定一切。  
方法/步骤

    1. 首先要进入到"/etc/yum.repos.d/"目录中（因为该目录存放着yum仓库的配置文件）
    2. 使用vim编辑器创建一个名为rhel7.repo的新配置文件（文件名称可随意，但后缀必须为repo），逐项写入下面加粗的配置参数并保存退出（不写中文注释）。  
[rhel-media] yum仓库唯一标识符，避免与其他仓库冲突。    
name=linuxprobe yum仓库的名称描述，易于识别仓库用处。。    
baseurl=file:///media/cdrom 提供方式包括FTP（ftp://..）、HTTP（http://..）、本地（file:///..）  
enabled=1 设置此源是否可用，1为可用，0为禁用。  
gpgcheck=1 设置此源是否校验文件，1为校验，0为不校验。  
gpgkey=file:///media/cdrom/RPM-GPG-KEY-redhat-release 若为校验请指定公钥文件地址。  
    3. 按配置参数的路径把光盘挂载，并把光盘挂载信息写入到/etc/fstab文件中。
    4. 使用"yum install httpd -y"命令检查Yum仓库是否已经可用。
