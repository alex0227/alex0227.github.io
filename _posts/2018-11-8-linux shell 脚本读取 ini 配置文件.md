---
layout: post
title: linux shell 脚本读取 ini 配置文件
categories:
description: linux shell 脚本读取 ini 配置文件
keywords:
---

linux shell 脚本读取 ini 配置档打码如下：
#!/bin/bash
configFile="./config.ini"  
function ReadINIfile()  
{   	
  Key=$1
  Section=$2  	
  Configfile=$3
  ReadINI=`awk -F '=' '/\['$Section'\]/{a=1}a==1&&$1~/'$Key'/{print $2;exit}' $Configfile`   	
  echo "$ReadINI"  
  }  
  itemCount=`ReadINIfile "ItemCount" "General" "$configFile"`

  echo $itemCount   

  for((i=0; i<itemCount; i++))
  do
  Section="Item""$i"
  echo $Section 	
  numTest=`ReadINIfile "NumTest" "$Section" "$configFile"`	stringTest=`ReadINIfile "StringTest" "$Section" "$configFile"` 	
  echo $numTest
  echo $stringTest
  done

测试的ini配置档如下：
[General]
ItemCount=2  
[Item0]
NumTest=100
StringTest=for string test 0
[Item1]
NumTest=111
StringTest=for string test 1
测试结果如下：
2
Item0
100
for string test 0
Item1
111
for string test 1

另一个


算编写一个shell脚本来自动备份网站，需要从配置文件中读取一些参数，比如数据库名称、用户名和密码等。我分析了一下wdcp自带的数据库备份脚本mysqlbackup.sh，里面仅仅从文件mrpw.conf中读取了数据库密码，只用到了cat而已：
mrpw=`cat /www/wdlinux/wdcp/conf/mrpw.conf`
1
这个方法虽然简易，但总不能每一个参数都用一个文件来保存吧。现在很多软件的配置参数都是用MS ini文件来保存的，使用方便而且可读性非常好，所以我在网上找了一个shell读取ini文件的方法，本文则是对该方法的学习总结。
这个方法主要知识点是awk命令以及正则表达式，相关资料：
shell awk 入门
正则表达式 - 语法
awk 正则表达式、正则运算符详细介绍
MS ini文件格式可以参照百度百科，大致结构为：
[section1]
name1 = value1
name2 = value2

[section2]
name3 = value3

中括号里面是节，name是键名，value是键值
实现读取ini文件的函数代码看起来比较简单：
function readINI()
{
 FILENAME=$1; SECTION=$2; KEY=$3
 RESULT=`awk -F '=' '/\['$SECTION'\]/{a=1}a==1&&$1~/'$KEY'/{print $2;exit}' $FILENAME`
 echo $RESULT
}

调用方法（从名为Filename的文件中读取Section节中的Key键值保存到变量Value中）：
Value=$(readINI Filename Section Key)


解析：
readINI()函数接收三个参数分别传给FILENAME、SECTION和KEY，然后调用awk命令在文件中查找指定键值。这里最重要的就是awk命令：
awk -F '=' '/\['$SECTION'\]/{a=1}a==1&&$1~/'$KEY'/{print $2;exit}' $FILENAME
1
-F '=' 表示用“=”作为分割符（多余的空格同样会被忽略）；
/\['$SECTION'\]/ 和 /'$KEY'/ 是正则表达式，用于匹配节名和键名，注意在awk正则表达式中参数要用单引号括起来；
/\['$SECTION'\]/{a=1} 表示逐行搜索到目标节时用变量a标记下来；
a==1&&$1~/'$KEY'/{print $2;exit} 表示当目标节已找到（a==1）并且当前行第一个参数匹配键名时，打印第二个参数（键值）并退出，这里exit是为了防止后面其它节有相同的键名。

注意：
该方法的一个缺陷是，如果节名和键名都存在但目标键并不在目标节下时会返回错误的搜索结果，因此在写ini文件的时候需要注意。
