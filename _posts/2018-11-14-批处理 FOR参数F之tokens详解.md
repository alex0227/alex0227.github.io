---
layout: post
title: 批处理 FOR参数/F之tokens详解
categories:
description: 批处理 FOR参数/F之tokens详解
keywords:
---

tokens=x,y,m-n 提取列实现代码。

格式：

FOR /F "tokens=x,y,m-n" %%I IN (Command1) DO Command2

用法：

一句话总结：提取列。

通俗讲，共同提取每一行的第m小节的内容。

因此，可以用该命令来指定提取文本信息。

tokens=有时表示提取全部。

tokens=m表示提取第m列。

tokens=m,n表示提取第m列和第n列。

tokens=m-n表示提取第m列至第n列。

Tokens=*表示删除每行前面的空格。忽略行首的所有空格。

tokens=m*提取第m列以后的所有字符，星号表示剩余的字符。

tokens=m,*提取第m列以后的所有字符，星号表示剩余的字符。 

输出变量的个数由定义了的tokens决定。

在 FOR 语句中显式声明 %%i。使用tokens= 隐式声明%%j 和%%k。只要不会引起试图声明高于字母“z”或“Z”的某个变量，则使用tokens= 可以指定最多 26 个输出变量。


接着前面的例子“静夜思”。

如果我要提取第三小节“举头望明月”，如何做到？

@echo off

for /f "delims=， tokens=3" %%i in (静夜思.txt) do echo %%i

pause>nul


讲解：

首先用delims=，表示命令要用逗号作为诗句的分隔符将四句分成四小节。然后用tokens=3提取第三小节，即“举头望明月”了。

delims=和tokens=共用一对双引号，如果单独用双引号，则FOR命令返回的只能是它们之中的一个。因为在第一节说过，FOR是逐一读取命令的，将delims和tokens分开后，FOR只能一次读取一个，不能一次全部读取。


注意：

Tokens常和delims一起使用。

首先，一行内容被delims用分割符号如逗号等分隔成许多小段或小节。

然后，tokens才能提取每行之间对应的这些小段或小节——列。具体看下面例子。


例：提取前面例子文本“a.txt”中每行的第三段内容“ccc”、“kkk”、“ggg”、“考试”。

@echo off

for /f "skip=1 delims=,， tokens=3" %%i in (a.txt) do echo %%i

pause>nul

注意：为什么kkk不出现呢？


例：如何屏蔽掉文本“a.txt”中的标点符号？

@echo off

for /f "skip=1delims=,， tokens=1-4" %%i in (a.txt) do echo %%i %%j %%k %%l

pause>nul


讲解：

%%i %%j %%k %%l是输出变量，它的个数由tokens后面的（m-n）决定，一般有（n-m+1）个，但不能超过Z。

并且%%后面的字母存在先后顺序，%%a %%b是顺向，倒过来则是逆向的。

代行号的tokens=m*，星号表示m后面的所有剩余的列。

例：

for /f "tokens=*" %%i in (a.txt) do echo %%i

讲解：

没有指定具体的列，"tokens=*"将提取全部列，后面只需一个输出变量%%i。


例：

for /f "tokens=2*" %%i in (a.txt) do echo %%i %%j

讲解：

"tokens=2*"提取第二列以后的所有字符，星号表示剩余的字符。输出变量%%i对应于2的输入变量，%%j对应于星号的输入变量。

注意：

这里没有使用delisms但却以空格分隔了，是因为FOR默认空格是做分隔符的。


例：屏蔽掉“易经.txt”中的标点符号：

@echo off

for /f "tokens=1-5 delims=：，" %%a in (易经.txt) do echo %%a %%b %%c %%d %%e

pause>nul

讲解：

当delims定义了两个分隔符“：”和“，”时，提取列要用到tokens。

有五个列，所以tokens要定义提取第一列至第五列，即1-5，相应地，后面需要五个输出显示变量%%a %%b %%c %%d %%e。你也可以这样修改，避免过多的变量符号【补充？】

你也可以将最后多余的句号定义为分隔符“delims=：，。”将其屏蔽掉。


练习：

1、 请简单说说tokens的作用

2、 Tokens=1,3-5后面的输出变量需要多少个？

3、 请使用tokens提取下面网页的标题《硬盘知识及故障大总汇(页 2)》：
