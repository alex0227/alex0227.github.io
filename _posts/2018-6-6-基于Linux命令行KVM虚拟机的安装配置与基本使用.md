---
layout: post
title: 基于Linux命令行KVM虚拟机的安装配置与基本使用
categories: 基于Linux命令行KVM虚拟机的安装配置与基本使用
description: 基于Linux命令行KVM虚拟机的安装配置与基本使用
keywords: Linux KVM虚拟机 安装配置 基本使用
---

## 背景
由于生产环境的服务器并不会安装桌面环境，简单操作的图形化安装也不适合批量部署安装。因此，我还是更倾向于在命令下安装配置KVM虚拟机。结合了一些资料和个人使用的状况，我大致列出了一些基本和常用的使用方法。

## 安装配置

### 一、环境介绍
操作系统：centos6.6 内核版本：2.6 IP地址：172.16.57.24

### 二、检查CPU
和 Xen 不同，KVM 需要有 CPU 的支持（Intel VT 或 AMD SVM），在安装 KVM 之前检查一下 CPU 是否提供了虚拟技术的支持
```
egrep 'vmx|svm' /proc/cpuinfo
```

### 三、安装KVM
1. 这里安装一些虚拟化的组件
```   
yum -y install kvm python-virtinst libvirt  bridge-utils virt-manager qemu-kvm-tools  virt-viewer  virt-v2v libguestfs-tools-c  
```
2. 对kvm进行如下如下配置：
```  
vim /etc/libvirt/libvirtd.conf
```
这里主要是tcp连接的设置
末尾添加如下：
```
listen_tls = 0
listen_tcp = 1
tcp_port = "16509"
listen_addr = "172.16.57.24"
unix_sock_ro_perms = "0777"
unix_sock_rw_perms = "0770"
auth_tcp = "none"
max_clients = 1024
min_workers = 100
max_workers = 200
max_requests = 20
max_client_requests = 50
vim qemu.conf
```
这里主要是对vnc的设置，一会儿通过vnc-viewer连接来进行安装
末尾添加```
vnc_listen = 0.0.0.0
vnc_password = "bigdata"  #vnc连接密码
remote_display_port_min = 5900 #vnc最小端口
remote_display_port_max = 65535 #vnc最大端口
```
3. 确认一下是否 kvm 安装成功：
```
/etc/init.d/libvirtd restart
```
4. 查看是否启动：
```
ps -ef | grep
```
5. 查看kvm模块是否正常加载：
```
lsmod |grep kvm
```

### 四、配置网桥连接
1. 配置网桥br0
```
vim /etc/sysconfig/network-scripts/ifcfg-br0
DEVICE=br0
TYPE=Bridge
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=static
IPADDR=172.16.57.24
NETMASK=255.255.255.0
GATEWAY=172.16.57.1
DNS1=202.96.209.133
vim /etc/sysconfig/network-scripts/ifcfg-em1
DEVICE=em1
TYPE=Ethernet
ONBOOT=yes
BRIDGE=br0
NM_CONTROLLED=yes
```
2. 重启网络
```
/etc/init.d/network restart
```
3. 查看网络连接
```
brctl show
```
如果配置成功，会有以下输出：
```
bridge name	bridge id		STP enabled	interfaces
br0		8000.14187745822e	no		em1
							vnet0
							vnet1
							vnet2
							vnet3
							vnet4
							vnet5
							vnet6
virbr0		8000.5254005fcc0b	yes		virbr0-nic
```
4. 检查ip转发是否开启
```
cat /etc/sysctl.conf |grep ip_forward
net.ipv4.ip_forward = 1
```
如果没有开启：
```
vim /etc/sysctl.conf
```
修改
```
net.ipv4.ip_forward = 1
/sbin/sysctl -p
```

### 五、安装虚拟机
```
virt-install  --name=tomcat_01 --ram 8192 --vcpus=2 /
--disk path=/var/lib/libvirt/images/tomcat_01.img,size=20,format=raw,bus=virtio /
--cdrom /var/iso/CentOS-6.7-x86_64-minimal.iso --network bridge=br0,model=virtio /
--vnc --accelerate --force  --autostart
```
这里解释一下主要的几个参数含义：   
--name    给虚拟机起个名字  
--ram     分配给虚拟机的内存，单位MB  
--vcpus   分配给虚拟机的cpu个数  
--cdrom   指定安装文件的全路径  
--disk    指定虚拟机img文件路径，如果虚拟机使用lvm分区，这里就指向到lvm的分区就行  
  size    虚拟机文件大小，单位GB  
  bus     虚拟机磁盘使用的总线类型，为了使虚拟机达到好的性能，这里使用virtio  
  cache   虚拟机磁盘的cache类型  
--network bridge    指定桥接网卡  
   model  网卡模式，这里也是使用性能更好的virtio  
--graphics图形参数  
这里我安装的虚拟机名字为tomcat_01，接下来查看其vnc端口：
```
ps aux | grep qemu | grep tomcat_01
```
 当时我们设置vnc端口最小为5900，那么这里指的就是5902这个端口。 接下来通过在windows下使用vnc-viewer来连接172.16.57.24:5902，并输入设置的密码，即可进行安装。

### 常用操作
1. 宿主机管理虚拟机 对虚拟机进行开关等管理，需要在虚拟机上安装acpid的服务，并启动它。
2. 克隆虚拟机 一般制作一个常用的虚拟机模板，下次安装可以直接来克隆这个模板。
```
virt-clone --connect qemu:///system --original=tomcat_01 --name=tomcat_02 --file=//var/lib/libvirt/images/tomcat_02.img
--original克隆的虚拟机对象
--name    克隆的新虚拟机名称
```
3. 添加磁盘 一般虚拟机的磁盘结构为系统盘加数据盘，当系统发生故障时，可以重新建一个系统盘，而数据盘不会丢失。 添加磁盘操作:
```
cd /var/lib/libvirt/images/ 进入磁盘存放目录
qemu-img create -f raw test_add.img 100G 创建一块100G的磁盘
virsh attach-disk tomcat_01 /var/lib/libvirt/images/test_add.img vdb --cache none 动态添加磁盘
```
这种方法是动态添加磁盘，而xml配置文件并没有发生变化，为了防止下次启动添加的磁盘失效，进行如下操作：
```
virsh dumpxml tomcat_01 > tomcat_01.xml 将最新的虚拟机配置重定向到其配置文件中
virsh define tomcat_01.xml
```
这样，新增的磁盘就不会丢了。
4. virsh命令的一些操作     
Autostart 自动开始一个域     
Create 从一个 XML 文件创建一个域       
Define 从一个 XML 文件定义（但不开始）一个域   
edit 编辑某个域的 XML 配置   
shutdown 关闭一个域   
start 开始一个（以前定义的）非活跃的域   
reboot 重新启动一个域   
suspend 挂起一个域
resume 重新恢复一个域     
vncdisplay vnc 显示  
