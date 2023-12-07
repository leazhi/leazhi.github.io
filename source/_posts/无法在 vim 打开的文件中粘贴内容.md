---
title: 无法在 vim 打开的文件中粘贴内容.md
author: leazhi
tags:
  - [debian]
  - [vim]
categories:
  - [debian]
  - [vim]
date: 2023-08-31 10:27:23
cover: 
discription: 
keywords: 
password: 
message: 
---

## 系统环境
- 系统: Debian 12
- 内核: 6.1.10-30-generic
- vim: 8.2.3469

## 问题描述

新装的 Debian 系统，安装好 vim 后，使用 vim 编辑文件，粘贴内容提示：寄存器没有东西


## 解决方法：


编辑 /usr/share/vim/vim90/defaults.vim  文件，找到关键字 `set mouse=a` ,将其修改为：
```bash
set mouse-=a
```

最后，保存推出即可！