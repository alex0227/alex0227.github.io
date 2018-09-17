---
layout: post
title: Linux下对lvm逻辑卷分区大小的调整（针对xfs和ext4不同文件系统）
categories: lvm
description: Linux下对lvm逻辑卷分区大小的调整（针对xfs和ext4不同文件系统）
keywords: lvm
---


## 背景
当我们在安装系统的时候，由于没有合理分配分区空间，在后续维护过程中，发现有些分区空间不够使用，而有的分区空间却有很多剩余空间。如果这些分区在装系统的时候使用了lvm（前提是这些分区要是lvm逻辑卷分区），那么就可以轻松进行扩容或缩容！不同文件系统类型所对应的创建、检查、调整命令不同，下面就针对xfs和ext2/3/4文件系统的lvm分区空间的扩容和缩容的操作做一记录：


**特别注意的是：**
* resize2fs命令            针对的是ext2、ext3、ext4文件系统
* xfs_growfs命令         针对的是xfs文件系统


1）ext2/ext3/ext4文件系统的调整命令是resize2fs（增大和减小都支持）
```
lvextend -L 120G /dev/mapper/centos-home     //增大至120G
lvextend -L +20G /dev/mapper/centos-home     //增加20G
lvreduce -L 50G /dev/mapper/centos-home      //减小至50G
lvreduce -L -8G /dev/mapper/centos-home      //减小8G
resize2fs /dev/mapper/centos-home            //执行调整
```
2）xfs文件系统的调整命令是xfs_growfs（只支持增大）
```
lvextend -L 120G /dev/mapper/centos-home    //增大至120G
lvextend -L +20G /dev/mapper/centos-home    //增加20G
xfs_growfs /dev/mapper/centos-home          //执行调整
```
就是说：xfs文件系统只支持增大分区空间的情况，不支持减小的情况（切记！！！！！）。
硬要减小的话，只能在减小后将逻辑分区重新通过mkfs.xfs命令重新格式化才能挂载上，这样的话这个逻辑分区上原来的数据就丢失了。如果有重要文件，那就歇菜喽～～～

## 实例1（当系统上还有空闲空间的时候）
```
1）查看分区空间。如下可知是xfs文件系统（df的-T参数就能看出文件格式）。
[root@localhost ~]# df -hT
文件系统                类型      容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root xfs       200G  2.2G  198G    2% /
devtmpfs                devtmpfs   32G     0   32G    0% /dev
tmpfs                   tmpfs      32G     0   32G    0% /dev/shm
tmpfs                   tmpfs      32G   49M   32G    1% /run
tmpfs                   tmpfs      32G     0   32G    0% /sys/fs/cgroup
/dev/sda1               xfs       197M  139M   59M   71% /boot
tmpfs                   tmpfs     6.3G     0  6.3G    0% /run/user/0
/dev/mapper/centos-home xfs       628G   33M  718G    1% /home
```
2）使用vgdisplay命令查看系统上的空闲空间
```
root@localhost ~]# vgdisplay
  --- Volume group ---
  VG Name               centos
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  6
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                3
  Open LV               3
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               930.80 GiB
  PE Size               4.00 MiB
  Total PE              238285
  Alloc PE / Size       212736 / 831.00 GiB
  Free  PE / Size       25549 / 99.80 GiB       //这一项表示目前该系统上还存在99.80G的空闲空间（25549）
  VG UUID               a5hiAh-LB8M-9lRv-Ps1a-z35L-J4fk-sP3KrF
  ```

3) 将上面查到的空闲空间中的90G增减到/home分区上
```
[root@localhost ~]# lvextend -L +90G /dev/mapper/centos-home       //或者使用－l参数（跟PE数量），即lvextend -l +25500 /dev/mapper/centos-home
  Size of logical volume centos/home changed from 628.00 GiB (160768 extents) to 718.00 GiB (183808 extents).
  Logical volume centos/home successfully resized.

[root@localhost ~]# xfs_growfs /dev/mapper/centos-home
meta-data=/dev/mapper/centos-home isize=512    agcount=4, agsize=41156608 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=164626432, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=80384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 164626432 to 188219392
 ```
4）再次看着系统分区，发现home分区已经增加了90G（这种方式增加后，home分区之前的数据还不会丢失）
```
[root@localhost ~]# df -h
文件系统                 容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root  200G  2.2G  198G    2% /
devtmpfs                  32G     0   32G    0% /dev
tmpfs                     32G     0   32G    0% /dev/shm
tmpfs                     32G   49M   32G    1% /run
tmpfs                     32G     0   32G    0% /sys/fs/cgroup
/dev/sda1                197M  139M   59M   71% /boot
tmpfs                    6.3G     0  6.3G    0% /run/user/0
/dev/mapper/centos-home  718G   33M  718G    1% /home
```
虽然xfs文件系统只支持增加，不支持减少。但并不是说在xfs系统文件下不能减小，只是减小后，需要重新格式化才能挂载上。这样原来的数据就丢失了！

## 实例2: 这种情况只适用于系统刚安装好，逻辑分区内没有什么数据或数据不多且不重要可以删除或拷贝的情况下

系统安装好后，发现home分区过大，想从home分区中拿出100G给／分区
```
[root@localhost ~]# df -hT
文件系统                类型      容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root xfs       205G  2.2G  203G    2% /
devtmpfs                devtmpfs   32G     0   32G    0% /dev
tmpfs                   tmpfs      32G     0   32G    0% /dev/shm
tmpfs                   tmpfs      32G   49M   32G    1% /run
tmpfs                   tmpfs      32G     0   32G    0% /sys/fs/cgroup
/dev/sda1               xfs       197M  139M   59M   71% /boot
tmpfs                   tmpfs     6.3G     0  6.3G    0% /run/user/0
/dev/mapper/centos-home xfs       718G   33M  718G    1% /home

[root@localhost ~]# umount /home/

[root@localhost ~]# lvreduce -L -100G /dev/mapper/centos-home
  WARNING: Reducing active logical volume to 618.00 GiB.
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce centos/home? [y/n]: y
  Size of logical volume centos/home changed from 718.00 GiB (183808 extents) to 618.00 GiB (158208 extents).
  Logical volume centos/home successfully resized.
  ```
如下，很显然xfs文件系统不能执行分区减小的调整！
```
[root@localhost ~]# xfs_growfs /dev/mapper/centos-home
xfs_growfs: /dev/mapper/centos-home is not a mounted XFS filesystem
[root@localhost ~]# mount /dev/mapper/centos-home /home/
mount: /dev/mapper/centos-home：不能读超级块

这样，只能通过重新格式化这个分区，格式化后才能再次挂载到home下
[root@localhost ~]# mkfs.xfs /dev/mapper/centos-home -f
meta-data=/dev/mapper/centos-home isize=512    agcount=4, agsize=41156608 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=164626432, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=80384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

[root@localhost ~]# mount /dev/mapper/centos-home /home/
  ```
再次查看分区，发现home分区已经减小了100G,只不过这个分区里之前的数据都没有了。
```
[root@localhost ~]# df -hT         
文件系统                 类型      容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root xfs       205G  2.2G  203G    2% /
devtmpfs                devtmpfs   32G     0   32G    0% /dev
tmpfs                   tmpfs      32G     0   32G    0% /dev/shm
tmpfs                   tmpfs      32G   49M   32G    1% /run
tmpfs                   tmpfs      32G     0   32G    0% /sys/fs/cgroup
/dev/sda1               xfs       197M  139M   59M   71% /boot
tmpfs                   tmpfs     6.3G     0  6.3G    0% /run/user/0
/dev/mapper/centos-home xfs       618G   73M  578G    1% /home

```
上面在重新格式的时候，也可以将这个格式化为ext4格式。
```
[root@localhost ~]# mkfs.ext4 /dev/mapper/centos-home
[root@localhost ~]# cat /etc/fstab  //将home分区的开机挂载设置里的xfs改为ext4
```
然后将上面从home分区拿出的100G放到／分区下
```
[root@localhost ~]# vgdisplay
  --- Volume group ---
  VG Name               centos
  System ID            
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  9
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                3
  Open LV               3
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               930.80 GiB
  PE Size               4.00 MiB
  Total PE              238285
  Alloc PE / Size       211456 / 826.00 GiB
  Free  PE / Size       26829 / 104.80 GiB
  VG UUID               a5hiAh-LB8M-9lRv-Ps1a-z35L-J4fk-sP3KrF

[root@localhost ~]# lvextend -L +100G /dev/mapper/centos-root
  Size of logical volume centos/root changed from 205.00 GiB (52480 extents) to 305.00 GiB (78080 extents).
  Logical volume centos/root successfully resized.

[root@localhost ~]# xfs_growfs /dev/mapper/centos-root
meta-data=/dev/mapper/centos-root isize=256    agcount=5, agsize=13107200 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=0        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=53739520, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=0
log      =internal               bsize=4096   blocks=25600, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 53739520 to 79953920

[root@localhost ~]# df -hT         
文件系统                 类型        容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root xfs       305G  2.2G  203G    2% /
devtmpfs                devtmpfs   32G     0   32G    0% /dev
tmpfs                   tmpfs      32G     0   32G    0% /dev/shm
tmpfs                   tmpfs      32G   49M   32G    1% /run
tmpfs                   tmpfs      32G     0   32G    0% /sys/fs/cgroup
/dev/sda1               xfs       197M  139M   59M   71% /boot
tmpfs                   tmpfs     6.3G     0  6.3G    0% /run/user/0
/dev/mapper/centos-home xfs       618G   73M  578G    1% /hom
```

**温馨提示**
若是减小分区空间，减小前必须要先卸载这个分区。如果卸载有问题，解决如下：
```
[root@localhost ~]# umount /home/
umount: /home: device is busy.
(In some cases useful info about processes that use
the device is found by lsof(8) or fuser(1))
```
提示无法卸载，则是有进程占用/home，使用如下命令来终止占用进程：
```
[root@localhost ~]# fuser -m -k /home
/home: 1409 1519ce 1531e 1532e 1533e 1534e 1535e 1536e 1537e 1538e 1539e 1541e 1543e 1544e 1545e 1546e 1547e 1548e 1549e 1550e 1601m
```
再次卸载home分区就成功了。
```
[root@localhost ~]# umount /home/
-k 表示自动把霸占home分区的进程kill掉！
如果你不是很明确是否要杀死所有霸占设备的程序，还可以加一个-i 参数，这样每杀死一个程序前，都会询问！（即fuser －m -v -i -k /home）
```
