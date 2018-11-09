---
layout: post
title: linux里面的home文件夹不小心删除了怎么办
categories:
description: linux里面的home文件夹不小心删除了怎么办
keywords:
---

mkdir 新建目录，再在新的 /home 目录创建原来用户对应的目录。
之后要 chown 换所有者。

用 root 登录字符界面，两行命令：
mkdir /home/xxxxxxx
xxxxxx 是用户 id
chown user:group /home/xxxxx
user 是用户 id ，group 是这个用户对应的组，一般系统默认会建立一个同用户 id 名字的组。也就是 user:user 就行。不过看系统，如果不确定，:group 可以省略（那个“冒号”也一起不要哦）。

巨灵根那家伙无脑复制粘贴的吧？
没 home 目录肯定能启动。一般默认 root 用户的个人目录在 /root ，肯定不受影响。而且如果 home 目录真的没了，一般用户也是可以登录字符界面的，只是登录时会有提示，之后启动命令行后默认应该是出现在 / 里。
