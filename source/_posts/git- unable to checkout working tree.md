---
title: git- unable to checkout working tree
author: leazhi
tags:
  - [git]
categories:
  - [git]
  - [windows]
date: 2023-09-11 21:45:21
cover: https://hexo.linuser.com/gallery/code/985805.png
discription: 
keywords: 
password: 
message: 
---

## 问题描述

在 windows 10 pro 上拉取 gitlab 服务器上的一个仓库，其中仓库的某个文件下有个名为 `D:\guifeng.csv`  的文件。正是因为这个文件，导致我 git pull 执行成功，但是文件没有被 checkout 出来。

同时，git 报拉取日志如下：
```bash
leazh@DESKTOP-VC622UG MINGW64 /e/GitLab
$ git clone git@gitlab.linuser.com:brothers/python3.git
Cloning into 'python3'...
remote: Enumerating objects: 17906, done.
remote: Counting objects: 100% (427/427), done.
remote: Compressing objects: 100% (408/408), done.
remote: Total 17906 (delta 78), reused 0 (delta 0), pack-reused 17479
Receiving objects: 100% (17906/17906), 1.54 GiB | 1.16 MiB/s, done.
Resolving deltas: 100% (586/586), done.
error: invalid path '爬虫/JS 逆向/携程/D:\guifeng.csv'
fatal: unable to checkout working tree
warning: Clone succeeded, but checkout failed.
You can inspect what was checked out with 'git status'
and retry with 'git restore --source=HEAD :/'
```

## 故障原因

文件名含有冒号：，  憨憨Windows不支持，需要对git作如下配置：

## 解决方法

根据网上查到的解决方法，如下：
```bash
# 忽略路径中的转义字符
$ git config --global core.protectNTFS false

# 禁用换行符转换
$ git config --global core.autocrlf false

# 中文文件名，乱码问题。设为false的话，就不会对0x80以上的字符进行quot
$ git config --global core.quotepath false

# 然后 checked 即可！
$ git checkout
error: unable to create file 爬虫/JS 逆向/携程/D:\guifeng.csv: Invalid argument
Updating files: 100% (17707/17707), done.
D       "爬虫/JS 逆向/携程/D:\\guifeng.csv"
Your branch is up to date with 'origin/main'.
```