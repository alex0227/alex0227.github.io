---
layout: post
title: linux LVM
categories: linux
description: linux LVM
keywords: redhat，LVM,linux
---

## LVM——Logical Volume Manager
LVM就是动态卷管理，可以将多个硬盘和硬盘分区做成一个逻辑卷，并把这个逻辑卷作为一个整体来统一管理，动态对分区进行扩缩空间大小，安全快捷方便管理。

### 四个概念：
①PE(Physical Extend) 物理拓展
②PV(Physical Volume) 物理卷
③VG(Volume Group) 卷组
④LV(Logical Volume) 逻辑卷

## 特点：
LVM最大的特点就是可以对磁盘进行动态管理。因为逻辑卷的大小是可以动态调整的，而且不会丢失现有的数据。我们如果新增加了硬盘，其也不会改变现有上层的逻辑卷。作为一个动态磁盘管理机制，逻辑卷技术大大提高了磁盘管理的灵活性！

## 工作原理：
(1)物理磁盘被格式化为PV，空间被划分为一个个的PE
(2)不同的PV加入到同一个VG中，不同PV的PE全部进入到了VG的PE池内
(3)LV基于PE创建，大小为PE的整数倍，组成LV的PE可能来自不同的物理磁盘
(4)LV现在就直接可以格式化后挂载使用了
(5)LV的扩充缩减实际上就是增加或减少组成该LV的PE数量，其过程不会丢失原始数据

## 操作：
1. 查看：  
pes、pedisplay                查看pe的大小(pes==pescan)  
pvs、pvdisplay                查看物理卷  
vgs、vgdisplay、              查看卷组  
lvs、lvdisplay、              查看逻辑卷  
fdisk -L                     查看磁盘分区
2. 创建：  
pvcreate 设备路径               创建物理卷  
vgcreate 名字 pv路径            创建卷组  
lvcreate -n 名字 -L 大小 vg名   创建逻辑卷   
格式化：mkfs.ext4 lv完整路径               格式化逻辑卷（mkfs.文件系统格式或-t 文件系统格式）  
挂载：mount  lv完整路径  挂载点          挂载使用（可以使用/etc/fstab或autofs）  
3. 逻辑卷删除：
 1. 卸载：umount
 2. 删lv：lvremove lv完整路径
 3. 删vg：vgremove vg名
 4. 删PV：pvremove 设备完整路径 去硬盘
4. 逻辑卷扩展：
 1. 扩展pv：相当于创建pv
 2. 扩展vg： vgextend vg名 新增pv路径
 3. 扩展lv： lvextend -L +扩展量 lv完整名
 4. 刷新文件系统：resize2fs lv完整路径
注意：灵活运用，看实际情况，注意顺序 （支持在线操作）
5. 逻辑卷的缩小：
 1. 首先进行卸载 umount 检查文件系统：e2fsck -f lv完整路径
 2. 减少文件系统：resize2fs lv完整路径 减少到的大小
 3. 减少lv卷大小：lvreduce -L -减少量的大小 lv的完整路径
 4. 挂载使用
减小需谨慎，文件系统的减小后大小一定要和lv卷最终大小相等
