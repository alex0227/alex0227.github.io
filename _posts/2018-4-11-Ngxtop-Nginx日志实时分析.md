---
layout: post
title: Ngxtop-Nginx日志实时分析
categories: Ngxtop-Nginx日志实时分析
description: Ngxtop-Nginx日志实时分析
keywords: Ngxtop-Nginx 日志实时分析利 pip
---

ngxtop实时解析nginx访问日志，并且将处理结果输出到终端，功能类似于系统命令top，所以这个软件起名ngxtop。有了ngxtop，你可以实时了解到当前nginx的访问状况，再也不需要tail日志看屏幕刷新，由于ngxtop是python编写，我们使用pip安装，本文先介绍pip的一些用法。

## 1、Pip介绍
Pip类似RedHat里面的yum，安装Python包非常方便。本节详细介绍pip的安装、以及使用方法。    
Pip下载地址：https://pypi.python.org/pypi/pip/8.1.2
下载最新版的pip上传到服务器的/usr/local/src目录下  
```
cd /usr/local/src/

tar zxvf pip-8.1.2.tar.gz

cd pip-8.1.2

python setup.py install
```
报错：缺少模块setuptools
```
Traceback (most recent call last):
File "setup.py", line 6, in <module>
from setuptools import setup, find_packages
ImportError: No module named setuptools
```
解决：安装setuptools模块   
地址：https://pypi.python.org/pypi/setuptools
使用命令直接安装setuptools模块：
```
wget https://bootstrap.pypa.io/ez_setup.py -O - | python
```
上图可以安装setuptools已经正确安装，然后重新安装pip
安装完成如下图：   

检测pip是否安装成功，查看pip版本：
```
[root@zabbix pip-8.1.2]# pip -V
pip 8.1.2 from /usr/lib/python2.6/site-packages/pip-8.1.2-py2.6.egg (python 2.6)
```
2、pip使用详解   
2.1、使用pip安装相关软件
```
# pip install SomePackage

[...]

Successfully installed SomePackage
```
2.2、使用pip查看已安装的包
```
# pip show --files SomePackage

Name: SomePackage

Version: 1.0

Location: /my/env/lib/pythonx.x/site-packages

Files:

../somepackage/__init__.py

[...]
```
2.3、使用pip检查那些包需要更新
```
pip list --outdated

#可以看到图中有一个警告信息，告诉我们系统自带的python将在新版的pip失去支持，建议升级python版本。
```
2.4、使用pip升级软件包
```
# pip install --upgrade SomePackage

[...]

Found existing installation: SomePackage 1.0

Uninstalling SomePackage:

Successfully uninstalled SomePackage

Running setup.py install for SomePackage

Successfully installed SomePackage
```
2.5、使用pip卸载软件包
```
$ pip uninstall SomePackage

Uninstalling SomePackage:

/my/env/lib/pythonx.x/site-packages/somepackage

Proceed (y/n)? y

Successfully uninstalled SomePackage
```

3、pip实战   

3.1、使用pip安装和卸载redis
```
pip install redis

pip uninstall redis
```

3.2、Pip参数详解
```
# pip --help

Usage:

pip <command> [options]

Commands:

install                     安装包.

uninstall                   卸载包.

freeze                      按着一定格式输出已安装包列表

list                        列出已安装包.

show                        显示包详细信息.

search                      搜索包，类似yum里的search.

wheel                       Build wheels from your requirements.

zip                         不推荐. Zip individual packages.

unzip                       不推荐. Unzip individual packages.

bundle                      不推荐. Create pybundles.

help                        当前帮助.

General Options:

-h, --help                  显示帮助.

-v, --verbose               更多的输出，最多可以使用3次

-V, --version               现实版本信息然后退出.

-q, --quiet                 最少的输出.

--log-file <path>           覆盖的方式记录verbose错误日志，默认文件：/root/.pip/pip.log

--log <path>                不覆盖记录verbose输出的日志.

--proxy <proxy>             Specify a proxy in the form [user:passwd@]proxy.server:port.

--timeout <sec>             连接超时时间 (默认15秒).

--exists-action <action>    Default action when a path already exists: (s)witch, (i)gnore, (w)ipe, (b)ackup.

--cert <path>               证书.
```

4、使用pip安装ngxtop
4.1、ngxtop简介   
ngxtop实时解析nginx访问日志，并且将处理结果输出到终端，功能类似于系统命令top，所以这个软件起名ngxtop。有了ngxtop，你可以实时了解到当前nginx的访问状况，再也不需要tail日志看屏幕刷新。   
ngxtop项目地址：https://github.com/lebinh/ngxtop

4.2、安装ngxtop   

4.2.1、pip安装
```
pip install ngxtop

#看到上图表示已经安装成功
```
4.2.2、源码安装ngxtop
```
pip uninstall ngxtop
#如果已经pip安装
cd /usr/loca/src

wget https://github.com/lebinh/ngxtop/archive/master.zip -O ngxtop-master.zip

unzip ngxtop-master.zip && cd ngxtop-master

python setup.py install

#看到上图提示表示ngxtop已经安装成功
```
4.3、ngxtop使用详解
```
# ngxtop --help

ngxtop - ad-hoc query for nginx access log.

Usage:

ngxtop [options]

ngxtop [options] (print|top|avg|sum) <var> ...

ngxtop info

ngxtop [options] query <query> ...

Options:

-l <file>, --access-log <file>  需要分析的访问日志

-f <format>, --log-format <format>  log_format指令指定的日志格式 [默认: combined]

--no-follow  ngxtop default behavior is to ignore current lines in log

and only watch for new lines as they are written to the access log.

Use this flag to tell ngxtop to process the current content of the access log instead.

-t <seconds>, --interval <seconds>  report interval when running in follow mode [default: 2.0]

-g <var>, --group-by <var>  根据变量分组 [默认: request_path]

-w <var>, --having <expr>  having clause [default: 1]

-o <var>, --order-by <var>  排序 [默认: count]

-n <number>, --limit <number>  显示的条数 [default: 10]

-a <exp> ..., --a <exp> ...  add exp (must be aggregation exp: sum, avg, min, max, etc.) into output

-v, --verbose  更多的输出

-d, --debug  print every line and parsed record

-h, --help  当前帮助信息.

--version  输出版本信息.
```

高级选项:
```
-c <file>, --config <file>  运行ngxtop解析nginx配置文件

-i <filter-expression>, --filter <filter-expression>  filter in, records satisfied given expression are processed.

-p <filter-expression>, --pre-filter <filter-expression> in-filter expression to check in pre-parsing phase.
```
范例:
```
All examples read nginx config file for access log location and format.

If you want to specify the access log file and / or log format, use the -f and -a options.

"top" like view of nginx requests

$ ngxtop
```
404前十的请求
```
$ ngxtop top request_path --filter 'status == 404'
```
总流量前十的请求
```
$ ngxtop --order-by 'avg(bytes_sent) * count'
```
访问量前十的ip地址
```
$ ngxtop --group-by remote_addr
```
输出400以上状态吗的请求以及请求来源
```
$ ngxtop -i 'status >= 400' print request status http_referer

Average body bytes sent of 200 responses of requested path begin with 'foo':

$ ngxtop avg bytes_sent --filter 'status == 200 and request_path.startswith("foo")'
```
使用common日志格式分析远程服务器Apache访问日志
```
$ ssh remote tail -f /var/log/apache2/access.log | ngxtop -f common
```
5、ngxtop使用实例   

5.1、实时状态   
```
ngxtop -c /opt/nginx/conf/nginx.conf
```
5.2、访问前几的IP
```
ngxtop -c /opt/nginx/conf/nginx.conf top remote_addr
```
5.3、显示状态码为404的请求
```
ngxtop -i 'status == 404' print request status
```
5.4、显示前二十最频繁的请求
```
ngxtop -n 20
```
5.5、使用普通格式从远程服务器解析apache日志
```
ssh user@remote_server tail -f /var/log/apache2/access.log | ngxtop -f common
```

6、注意事项   
1、ngxtop单条命令无法执行

报错意思是说ngxin执行文件要加到PATH路径中，加软链接
```
ln -s /opt/ngixn/sbin/nginx /sbin   
#修改环境变量
# vim /etc/profile

export PATH=$PATH:/opt/nginx/sbin

source /etc/profile
```
