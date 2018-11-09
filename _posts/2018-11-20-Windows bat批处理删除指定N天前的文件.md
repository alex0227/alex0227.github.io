---
layout: post
title: Windows bat批处理删除指定N天前的文件
categories:
description: Windows bat批处理删除指定N天前的文件
keywords:
---
Windows下bat批处理，自动获取文件的最后修改时间，然后将指定多少天之前的文件删除，需要Windows支持forfiles命令，XP及以上系统都自带forfiles命令。
    删除指定目录下7天前的文件（目录为C:\test）：
```    
forfiles /p "c:\test" /s /m *.* /d -7 /c "cmd /c del @path"
    删除批处理文件当前目录下7天前的文件：
forfiles /p %~dp0 /s /m *.* /d -7 /c "cmd /c del @path"
    将以上其中一行复制，新建文本文件，粘贴并保存为bat格式，定期运行或者加入计划任务。

显示当前目录下七天以前的文件路径
forfiles /p %cd% /s /m *.* /d -7 /c "cmd /c echo @path"
```
