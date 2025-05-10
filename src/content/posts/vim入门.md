---
title: Vim
published: 2025-02-18
updated: 2025-02-18
description: 'Vim入门'
image: ''
tags: [Vim]
category: ''
draft: false 
---

## VIM

vim stdio.h 如果有则进入如果没有则创建文件

#### 三种模式

##### 命令模式（默认进入）

yy复制一行  10yy复制十行

p粘贴

dd剪切

u撤销

x删除一个字符

shift g跳到最后一行

gg回到第一行

*用于查找

shift z

##### 插入模式（Insert编辑）

通过i或者a按键进入，esc退出并进入命令模式

##### 底行模式（通过命令模式下进入）：

:w保存 q退出 wq保存退出 wq! 强制保存退出

:10跳到10行

：nohl消除高亮

:set nonu 去除行号

:set nu 加上行号

:%s/old/new替换

/进入底行模式用于查找

如/main
