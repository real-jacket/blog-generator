---
title: vscode 快捷操作
date: 2018-12-18 21:21:46
tags: vscode
categories: 
- 工具
- vscode
---
## 命令行使用vs code

code + filename/filepath 打开文件或者文件目录
参数：

- -r 表示在同一窗口打开，但不是添加工作区，而是删掉原有的工作区，只留一个
- `-g <filel:line[:character]` 打开文件，然后滚到文件中某个特定的行和列
- -d 用来比较两个文件的内容，传入两个文件路径

eg：code -r -g package.json:128 表示打开package.json文件，自动跳转到128行

通过命令行控制vscode
ls | code - 把当前目录下的所有文件名都展示在编辑器里

## 双手不离键盘

### 光标的移动

- 移动一个单词的距离  Opiton/Ctrl + 方向键
- 移动到行首或行尾 cmd+方向键/home、end（window与linux）
- 代码块的光标移动（光标在中括号上的跳转） Cmd/Ctrl + shift + \
- 移动到文档的第一行、最后一行  Cmd/Ctrl + Home/End

### 文本选择

基于光标移动的操作基础上 多一个 shift 键就可以在移动的同时选择文本

### 删除操作

单次操作删除代码：
删除光标左右两侧所有内容：window未绑定快捷键

##