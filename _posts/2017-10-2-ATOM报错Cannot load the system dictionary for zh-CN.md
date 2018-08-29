---
layout: post
title: ATOM报错Cannot load the system dictionary for zh-CN
categories: atom
description: ATOM报错Cannot load the system dictionary for zh-CN
keywords: ATOM报错  
---

ATOM每次打开都会报错，提示Cannot load the system dictionary for zh-CN.

![](/images/posts/atom/1.png)


打开提示的文件夹，发现里面有三个文件
![](/images/posts/atom/2.png)


查了一下Hanspell(https://github.com/hunspell/hunspell)是用来做拼写检查的。

然后打开Atom-Files-Settings-Packages搜索spell check，发现了这个spell-check的插件。(拓展里面)

![](/images/posts/atom/3.png)

点Settings进去，发现设置里会根据系统语言自己选择相应的拼写检查设置文件，然而文件夹中只有en-US的设置，所以有两个选择可以解决这个问题

1. 取消掉Use Locales前面的勾选
或者
2. 手动填写en-US设置文件名称

![](/images/posts/atom/4.png)
