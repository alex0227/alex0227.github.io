---
 layout: post
 title: linux/mac输入terminal命令的快捷键
 categories: terminal
 keywords: 快捷键，技巧
---
### linux/mac输入terminal命令的快捷键：

快捷键| 功能
:---:|
Tab |自动补全   
Ctrl+a |光标移动到开始位置
Ctrl+e |光标移动到最末尾
Ctrl+k| 删除此处至末尾的所有内容
Ctrl+u |删除此处至开始的所有内容
Ctrl+d |删除当前字符
Ctrl+h |删除当前字符前一个字符
Ctrl+w |删除此处到左边的单词
Ctrl+y |粘贴由 Ctrl+u ， Ctrl+d ， Ctrl+w 删除的单词
Ctrl+l |相当于clear，即清屏
Ctrl+r |查找历史命令
Ctrl+b |向回移动光标
Ctrl+f |向前移动光标
Ctrl+t |将光标位置的字符和前一个字符进行位置交换
Ctrl+& |恢复 ctrl+h 或者 ctrl+d 或者 ctrl+w 删除的内容
Ctrl+S |暂停屏幕输出
Ctrl+Q |继续屏幕输出
Ctrl+Left-Arrow |光标移动到上一个单词的词首
Ctrl+Right-Arrow |光标移动到下一个单词的词尾
Ctrl+p |向上显示缓存命令
Ctrl+n |向下显示缓存命令
Ctrl+d |关闭终端
Ctrl+xx |在EOL和当前光标位置移动
Ctrl+x@|显示可能hostname补全
Ctrl+c |终止进程/命令
Shift +上或下 |终端上下滚动
Shift+PgUp/PgDn |终端上下翻页滚动
Ctrl+Shift+n |新终端
alt+F2 |输入gnome-terminal打开终端
Shift+Ctrl+T |打开新的标签页
Shift+Ctrl+W |关闭标签页
Shift+Ctrl+C |复制
Shift+Ctrl+V |粘贴
Alt+数字 |切换至对应的标签页
Shift+Ctrl+N |打开新的终端窗口
Shift+Ctrl+Q |管壁终端窗口
Shift+Ctrl+PgUp/PgDn |左移右移标签页
Ctrl+PgUp/PgDn |切换标签页
F1 |打开帮助指南
F10 |激活菜单栏
F11 |全屏切换
Alt+F |打开 “文件” 菜单（file）
Alt+E |打开 “编辑” 菜单（edit）
Alt+V |打开 “查看” 菜单（view）
Alt+S |打开 “搜索” 菜单（search）
Alt+T |打开 “终端” 菜单（terminal）
Alt+H |打开 “帮助” 菜单（help）

另外一些小技巧包括：在终端窗口命令提示符下，连续按两次 Tab 键、或者连续按三次 Esc 键、或者按 Ctrl+I 组合键，将显示所有的命令及工具名称。Application 键即位置在键盘上右 Ctrl 键左边的那个键，作用相当于单击鼠标右键。
### mac系统打开/关闭/切换终端 常用快捷键
快捷键| 功能
:---:|
      cmd+t|开一个terminal标签页    
      cmd+shift+left|标签页切换     
      cmd+shift+r|标签页切换
      cmd+n|开一个新的terminal窗口  
      cmd+left|窗口切换   
      cmd+right|窗口切换
      cmd+d|垂直切分当前窗口    
      cmd+shift+d|合并垂直切分的窗口
2.vim快捷键
      1. 移动光标位置：
          hjkl    
            shift+4($):末尾    
            g+下划线:末尾       0:开头   
            shift+6(^):开头            w:下一个单词(仅包含字母)     W:下一个单词(包含特殊符号)     b:上一个单词    B:上一个单词  e:下一个单词末尾  E:下一个单词末尾
            ctrl+f:forward  page    ctrl+b:backward page   ctrl+d:forward 1/2 page    ctrl+u:backward 1/2page    ctrl+e:next line move   ctrl+y:previous line move
            G:文末    gg:文头    nG:line n     H:head line of page   M:middle line of page    
      2. 插入模式切换:
            esc:进入命令模式      i:当前位置插入     a:下一个字符插入       o:下一行插入      O:上一行插入       cw:删除当前位置下一个单词插入       x:删除当前字符
      3. 复制粘贴：
             dd:删除一行  p:粘贴    yy:拷贝一行   ndd nyy:删除复制n行
             . : repeat previous cmd
             100idesu:插入100次desu  . :再次复制100次    
       4. 查找,替换:
             /pattern  前向查找    ?pattern 后向查找     n:next      shift+n:反向的next
             s/pattern1/pattern2:替换       s/pattern1/pattern2/g:替换当前行所有
             1,$s/pattern1/pattern2/g:替换从第一行开始所有
             编程实际相关:  *查找当前单词的下一次出现  #查找当前单词的上一次出现
      5. 块操作:
             ctrl+v:开始选择要操作的块
             I 然后   ＃插入多行注释，
             d:删除   s:删除后替换  
      6. 分屏操作:
            split:上下分屏    vsplit:左右分屏
            ctrl+w+hjkl:切换分屏到不同方向
            e:打开文件
