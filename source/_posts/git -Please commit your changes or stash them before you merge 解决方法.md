---
title: git -Please commit your changes or stash them before you merge 解决方法
author: leazhi
tags:
  - [git]
categories:
  - [git]
date: 2023-09-05 20:29:08
cover: https://hexo.linuser.com/gallery/code/985805.png
discription: git 冲突
keywords: git
password: 
message: 
---

## 问题描述

在本地修改了某个文件，做了 add ，但没有 commit ，于是从远程服务器拉取别人提交的代码到本地报：
```bash
PS E:\GitLab\hexo\source\_posts> git pull
remote: Enumerating objects: 17, done.
remote: Counting objects: 100% (17/17), done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 13 (delta 9), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (13/13), 7.82 KiB | 200.00 KiB/s, done.
From gitlab.linuser.com:brothers/hexo
   88ebc96..09799db  main       -> origin/main
error: Your local changes to the following files would be overwritten by merge:
        source/_posts/django-small 系列009-个人用户中心及Email.md
Please commit your changes or stash them before you merge.
Aborting
Updating 88ebc96..09799db
PS E:\GitLab\hexo\source\_posts> git pull
error: Your local changes to the following files would be overwritten by merge:
        source/_posts/django-small 系列009-个人用户中心及Email.md
Please commit your changes or stash them before you merge.
Aborting
Updating 88ebc96..09799db 
```

## 解决方法


**说明**：
如果您的本地代码比较新，那么推荐使用 **方法一** ；反之，如果本地代码可以丢弃，则可以使用 **方法二**


### 方法一：通过git stash将工作区恢复到上次提交的内容，同时备份本地所做的修改，之后就可以正常git pull了，git pull完成后，执行git stash pop将之前本地做的修改应用到当前工作区。

git stash: 备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中。

git stash pop: 从Git栈中读取最近一次保存的内容，恢复工作区的相关内容。由于可能存在多个Stash的内容，所以用栈来管理，pop会从最近的一个stash中读取内容并恢复。

git stash list: 显示Git栈内的所有备份，可以利用这个列表来决定从那个地方恢复。

git stash clear: 清空Git栈。此时使用gitg等图形化工具会发现，原来stash的哪些节点都消失了。

1.打开命令行终端，输入：
```bash
leazh@DESKTOP-VC622UG MINGW64 /e/GitLab/hexo (main)
$ git stash
Saved working directory and index state WIP on main: 88ebc96 add movie website
```

2.然后，再次执行 git pull 即可：
```bash
leazh@DESKTOP-VC622UG MINGW64 /e/GitLab/hexo (main)
$ git pull
Updating 88ebc96..09799db
Fast-forward
 ...275\225\347\232\204\345\256\236\347\216\260.md" |   2 +-
 ...67\344\270\255\345\277\203\345\217\212Email.md" |   6 +-
 ...224\266\350\216\267\345\234\260\345\235\200.md" | 366 +++++++++++++++++++++
 3 files changed, 370 insertions(+), 4 deletions(-)
 rename "source/_posts/django-samll \347\263\273\345\210\227008-\347\231\273\345\275\225\347\232\204\345\256\236\347\216\260.md" => "source/_posts
/django-small \347\263\273\345\210\227008-\347\231\273\345\275\225\347\232\204\345\256\236\347\216\260.md" (99%)
 create mode 100644 "source/_posts/django-small \347\263\273\345\210\227010-\351\202\256\347\256\261\351\252\214\350\257\201\345\217\212\346\224\2
66\350\216\267\345\234\260\345\235\200.md"
```

3.执行 git stash pop，从Git栈中读取最近一次保存的内容，恢复工作区的相关内容
```bash
git stash pop
```

### 方法二：放弃本地修改 的改法  ----这种方法会丢弃本地修改的代码，而且不可找回

1.打开命令行终端，输入：
```bash
git reset --hard
```

2.执行 git pull 即可：
```bash
git pull
```