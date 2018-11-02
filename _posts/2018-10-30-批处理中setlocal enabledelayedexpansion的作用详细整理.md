---
layout: post
title: 批处理中setlocal enabledelayedexpansion的作用详细整理
categories:
description: 批处理中setlocal enabledelayedexpansion的作用详细整理
keywords:
---

设置本地为延迟扩展。其实也就是：延迟变量，全称延迟环境变量扩展, 想进阶，变量延迟是必过的一关！所以这一部分希望你能认真看。

设置本地为延迟扩展。其实也就是：延迟变量，全称延迟环境变量扩展, 想进阶，变量延迟是必过的一关！所以这一部分希望你能认真看。
为了更好的说明问题，我们先引入一个例子。
例1:

@echo off
set a=4
set a=5&echo %a%
pause
结果：4
解说：为什么是4而不是5呢？在echo之前明明已经把变量a的值改成5了？让我们先了解一下批处理运行命令的机制：批处理读取命令时是按行读取的（另外例如for命令等，其后用一对圆括号闭合的所有语句也当作一行），在处理之前要完成必要的预处理工作，这其中就包括对该行命令中的变量赋值。我们现在分析一下例1，批处理在运行到这句“set a=5&echo %a%”之前，先把这一句整句读取并做了预处理——对变量a赋了值，那么%a%当然就是4了！（没有为什么，批处理就是这样做的。）而为了能够感知环境变量的动态变化，批处理设计了变量延迟。简单来说，在读取了一条完整的语句之后，不立即对该行的变量赋值，而会在某个单条语句执行之前再进行赋值，也就是说“延迟”了对变量的赋值。那么如何开启变量延迟呢？变量延迟又需要注意什么呢？
举个例子说明一下：
例2:
```
@echo off
setlocal enabledelayedexpansion
set a=4
set a=5&echo !a!
pause
```
结果：5
解说：由于启动了变量延迟，得到了正确答案。变量延迟的启动语句是“setlocal enabledelayedexpansion”，并且变量要用一对叹号“!!”括起来（注意要用英文的叹号），否则就没有变量延迟的效果。分析一下例2，首先“setlocal enabledelayedexpansion”开启变量延迟，然后“set a=4”先给变量a赋值为4，“set a=5&echo !a!”这句是给变量a赋值为5并输出（由于启动了变量延迟，所以批处理能够感知到动态变化，即不是先给该行变量赋值，而是在运行过程中给变量赋值，因此此时a的值就是5了）。再举一个例子巩固一下。
例3:
```
@echo off
setlocal enabledelayedexpansion
for /l %%i in (1,1,5) do ( set a=%%i echo !a! )
pause
```
结果：12345
解说：本例开启了变量延迟并用“!!”将变量扩起来，因此得到我们预期的结果。如果不用变量延迟会出现什么结果呢？结果是这样的：ECHO 处于关闭状态。ECHO 处于关闭状态。ECHO 处于关闭状态。ECHO 处于关闭状态。ECHO 处于关闭状态。即没有感知到for语句中的动态变化。
batman的说明
我来简要说一下吧：
```
set：设置
local：本地（环境变量）
enable：能够
delayed：延迟
expansion：扩展
setlocal enabledelayedexpansion就是扩展本地环境变量延迟，
比较下面两段代码：
```
```
@echo off
for /l %%i in (1,1,10) do (
set "str=%%i"
echo %str%
)
pause>nul

@echo off&setlocal enabledelayedexpansion
for /l %%i in (1,1,10) do (
set "str=%%i"
echo !str!
)
pause>nul
```
第一段代码只会显示10行“ECHO 处于关闭状态。”，而第二段代码则会正确显示1－10的10行数字。这是为什么呢？因为在两段代码的for循环前str都是没有被定义的，而由于第一段代码没有开启变量延迟，所以str值一直是没有定义，因而显示出了10行报
错信息；而第二段代码开启了变量延迟，在for循环中每次赋予str的值被传递下去，因而会正确显示10行数字，但这里的str变量符必须要写成!str!，这是没有道理可讲的，只要记住就好了。

setlocal enabledelayedexpansion 是什么意思？

是：设置本地为延迟扩展。其实也就是：延迟变量，全称"延迟环境变量扩展",
在cmd执行命令前会对脚本进行预处理，其中有一个过程是变量识别过程，在这个过程中，如果有两个%括起来的如%value%类似这样的变量，就会对其进行识别，并且查找这个变量对应的值，再而将值替换掉这个变量，这个替换值的过程,就叫做变量扩展，然后再执行命令。
在解释之前，先看几个例子的区别：
例一：
```
set value=kkkkkkk
echo %value%
```
将这段代码保存到一个后缀为bat的文本文件中。然后打开dos，进到对应目录下，执行这个文件，结果如下：
```
C:\Documents and Settings\Administrator\桌面\ln\temp\bat>set value=kkkkkkk
C:\Documents and Settings\Administrator\桌面\ln\temp\bat>echo kkkkkkk
kkkkkkk
```
最后一行是结果，但是在结果之前，还有两句，set value=kkkkkkk 和 echo kkkkkkk，但是在语句中，我们并没有写echo kkkkkkk的语句，这表明至少在执行到echo %value% 这句时，对变量进行的值的替换。这就是变量的扩展。
那么什么是变量的延迟扩展呢？
如果大家知道C++的“静态变量”概念，那就应该知道，c++编译的时候，会对静态变量进行值的替换，但这个替换是基于静态的前提下，那么进行变量扩展时，也是这样，但如果出现动态的情况会怎样？在cmd执行中，发生动态的一种情况是在 for语句中进行变量赋值，例如：
例二：
```
@echo off
for /l %%i in (1,1,3) do (
set k=%%i ::对k进行循环赋值
echo %k% %%i
)
```
执行这样的脚本，出现如下结果：
```
_1
_2
_3
结果出现这三句话。_ 表示空格
```
注：k没有赋初值，则替换为空。

例三：
```
@echo off
set k=yyy
for /l %%i in (1,1,3) do (
set k= %%i ::对k进行循环赋值
echo %k% %%i
)
```
结果:
```
yyy 1
yyy 2
yyy 3
```
注：k有赋初值，则都替换为yyy。、
实例四：
```
@echo off
setlocal enabledelayedexpansion
set k= 3
for /l %%i in (1,1,3) do (
set k=%%i
echo %k% %%i
)
```

结果：
```
3 1
3 2
3 3
```
这里已经是用了延迟变量，为什么还会出现这种情况呢？再看实例五：
实例五：
```
@echo off
setlocal enabledelayedexpansion
set k= 3
for /l %%i in (1,1,3) do (
set k=%%i
echo !k! %%i
)
```
结果：
```
1 1
2 2
3 3
```
原来在延迟变量扩展中，要使用！来引用变量。
