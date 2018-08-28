---
layout: post
title: 在Dropbox上搭建私有的Git仓库的教程
categories: Dropbox Git
description: 在Dropbox上搭建私有的Git仓库的教程
keywords: Dropbox Git
---

### 一、在Dropbox的文件夹下创建远程repository文件夹(.git后缀)

```
cd ~/Dropbox/project
mkdir demo.git
```

### 二、进入demo.git文件夹并初始化repository

```
cd demo.git
git init --bare
```

###三、创建完毕，现在创建一份本地clone，如要创建在～/lab/demo/目录下

```
cd ~/lab/
git clone ～/Dropbox/project/demo.git demo
```

### 四、测试git

```
cd demo
touch README
git add README
git commit -m "fisrt commit"
git push origin master
```
### 五、共享/Dropbox/project/demo.git文件夹

```
[share]
comment=this is Linux share directory
path=/home/myth/share
public=yes
writable=yes
```
