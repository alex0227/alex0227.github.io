---
layout: post
title: Mysql报错：Please DISCARD the tablespace before IMPORT
categories:
description: Mysql报错：Please DISCARD the tablespace before IMPORT
keywords:
---
今天在使用MySQL，创建表的时候遇到错误，tablespace for table ××××× exist, Please DISCARD the tablespace before IMPORT
看了以下，其实并没有这个表的存在，后来看mysql的数据目录的时候，发现，在对应数据库的目录下真的还有该表的文件存在，应该是以前删除表的时候
没有删除干净，导致表的一些数据文件还存在，所以，创建表的时候失败了。
         解决方案：删除数据库目录下相应的表文件，重新创建表即可
---------------------
