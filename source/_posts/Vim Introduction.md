---
title: Vim Introduction
url: vim-introduction
date: 2018-05-18 16:03:32
description: VIM入门
categories: Computer Science
tags: [Tools]
---

## 配置
安装原生态的Vim之后，界面非常简略：行号，自动缩进，括号匹配都没有.

为了我们使用的方便，进行一些基本的配置：
```bash
sudo vim /etc/vim/vimrc
```
进入配置文件后可以删掉注释或添加新的配置:

```shell
set number  # 左侧显示行号
set ts=4  # Tab键长度4个空格
set expandtab  # 输入Tab自动转换为空格
set cursorline  # 突出显示当前行
set autoindent  # 自动缩进
set showmatch  # 显示括号匹配
syntax on
```

## 使用
Vim有三种模式：输入模式、命令模式和末行命令模式。  
输入模式用来输入文字，命令模式用来下达编排文件的操作指令，末行命令模式用来进行文件存档、离开编辑器等操作:
![image](vimmode.png)

1. 进入及离开
末行模式下：
```shell
:w  # 保存当前文件
:x  # 保存文件并退出
:q!  # 放弃此次编辑并强制退出
:wq a.txt  # 保存退出
:syntax on  # 一次性语法高亮
:set number  # 一次性显示行号
```
2. 基本编辑
命令模式下按v进入visual模式：
```shell
d  # 选中目标文字段，按d(delete)剪切
y  # 选中目标文字段，按y(yank)复制
p  # 移动光标到目标位置，按p(put)粘贴
```

命令模式:
hjkl和方向键类似,w可以按词向后移动,b按词向前移动,ctrl+f/ctrl+b与pageup/down功能一样  
88gg直接跳到第88行 10j向下跳10行 10k向上跳10行  
/Node高亮所有Node以后 按n后跳 按shift+n前跳  
cc/dd/yy,按u取消操作 c2c删掉连续2行  
p粘贴,按u取消操作

编辑模式:  
ctrl+n自动补全

插件:  
vim plug  
vim awesome

## 中文输入

确保键盘输入系统选中fcitx，搜狗拼音输入法基于fcitx(Free Chinese Input Toy for X)框架，所以要选中fcitx：
![image](fcitx.png)

下载并安装搜狗输入法[安装包](https://pinyin.sogou.com/linux/?r=pinyin)，切换到安装包所在的目录，改下安装包的名字，不然太长了不方便，我这里改为`ha.deb`，之后安装：
```bash
sudo dpkg -i ha.deb
```

这里可能会提示依赖关系不满足(没提示的话跳过即可)：
![image](dependency.png)

这是由于你的电脑可能没有安装有关fictx的内容，修复依赖关系：
```bash
sudo apt-get install -f
```

顺利的话，桌面右上角会出现键盘图标：  
![image](success.png)  
可以看到搜狗输入法已经安装。

如果看不到搜狗的图标(尤其是第一次安装)，重启系统。
右键键盘图标，选择configure(或者搜fcitx configuration)：
![image](configure.png)

你现在应该没有Sogou Pinyin这一项，点那个+号：
![image](1260581-20211229103646985-2704756.png)

取消Only Show Current Language前的对号，搜索Sogou Pinyin，点击OK。  
现在，输入法应该已经安装好了。可以去桌面那个键盘图标看看了~

## Ref
[vim教程](https://www.bilibili.com/video/BV1Yt411X7mu)