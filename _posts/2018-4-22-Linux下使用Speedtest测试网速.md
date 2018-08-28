---
layout: post
title: Linux下使用Speedtest测试网速
categories: Linux Speedtest 测试网速
description: Linux下使用Speedtest测试网速
keywords: Linux Speedtest 测试 网速
---

Speedtest是用来测试网络性能的开源软件，在Linux下面安装Speedtest可以用来测试网络出口的上传和下载速度，帮助排查网络方面导致的故障。

## Speedtest介绍
由于公司几个项目用户访问的时候响应较慢，项目本身没问题，服务及调用的接口返回信息都比较正常，猜想是网络方面造成的瓶颈，导致用户请求的时候网络响应堵塞，拉长了响应时间。

Speedtest.net是比较广泛的用来测试宽带速度的网站，Speedtest.net的工作原理并不复杂：它在你的浏览器中加载JavaScript代码并自动检测离你最近的Speedtest.net服务器，然后向服务器发送HTTP GET and POST请求来测试上行/下行网速。

但在没有图形化桌面时（例如，当你通过命令行远程登陆服务器或使用没有图形界面的操作系统），基于flash、界面友好的Speedtest.net将无法工作。幸运的是，Speedtest.net提供了一个命令行版本——speedtest-cli。下面我将向你演示如何在Linux的命令行中使用speedtest-cli来测试宽带连接速度。

## 安装speedtest-cli
Github链接：https://github.com/sivel/speedtest-cli
Speendtest.net官网：http://www.speedtest.net/  
测试本机所在网络出口的带宽,访问Speendtest.net，点击首页的Begin开始测试，等待几秒，查看测试结果：

## pip安装
speedtest是用python写的，没使用过pip的需要先安装pip，  
pip安装：https://pip.pypa.io/en/stable/installing/
```
#开启epel源
yum install python-pip –y
```

## 安装speedtest-cli
```
pip install speedtest-cli
```
安装完成测试
```
which speedtest-cli | bash –
```

## github安装
```
pip install git+https://github.com/sivel/speedtest-cli.git  
或者  
git clone https://github.com/sivel/speedtest-cli.git  
python speedtest-cli/setup.py install
```

## shell安装
```
wget -O speedtest-cli https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest_cli.py
chmod +x speedtest-cli
./speedtest-cli
speedtest-cli使用
speedtest-cli –h
/usr/bin/speedtest-cli –share
/usr/bin/speedtest-cli –list
```
