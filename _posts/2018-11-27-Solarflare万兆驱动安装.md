---
layout: post
title: Solarflare万兆驱动安装
categories:
description: Solarflare万兆驱动安装
keywords:
---

。 从 www.openonload.org 网站下载 openonload-<version>.tgz 文件 使用 tar 命令解压 tar 文件: [root@system-N]# tar -zxvf openonload-<version>.tgz 在 openonload-<version>/scripts 子目录下运行 onload_install 命令： [root@system-N]# ./onload_install 如果 openonload 安装正确，安装过程将输出如下 3 行提示 onload_install: Build complete. onload_install: Installing OpenOnload. onload_install: Install complete.

./onload_tool restart
