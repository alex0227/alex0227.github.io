---
layout: post
title: Linux vim快捷键
categories: Linux
description: Linux vim快捷键
keywords: Linux，终端，快捷键
---

## vim常用快捷键

  1. 命令模式
  默认进入命令模式，在输入模式按esc回到命令模式  
  dd 删除（剪切）光标所在行  
  5dd 5行  
  yy 复制    
  5yy 5行    
  n 显示搜索命令定位到下一个字符串    
  N 显示  上一个
  p 将之前删除（dd）或者复制（yy）的数据粘贴到光标处  
  ？字符串 从文中自下而上搜索  
  /字符串 从上而下   
  2. 末行模式
  命令模式输入：进入末行模式
  :w 保存  
  :q 退出  
  :q!强制退出  
  :wq保存退出  
  :wq!强制保存退出  
  :set nu显示行号  
  :set nonu 不显示行号   
  :命令 执行命令  
  :整数 跳到该行  
  3. 输入模式
  命令模式时输入以下切换输入模式
  a在光标后一位输入
  i在光标当前位置输入
  o在光标下一行输入
