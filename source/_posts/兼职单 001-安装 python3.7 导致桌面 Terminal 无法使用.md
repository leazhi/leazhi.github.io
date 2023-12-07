---
title: 兼职单 001-安装 python3.7 导致桌面 Terminal 无法使用
author: leazhi
tags:
  - [ubuntu]
  - [python3]
categories:
  - [ubuntu]
  - [python3]
date: 2023-08-23 18:21:40
cover: 
discription: 
keywords: 
password: 
message: 
---

## 系统环境
- OS: Ubuntu 20.04.4 LTS
- Kernel: 5.15.0-79-generic
- 桌面环境: GNOME Shell 3.36.9
- 终端: gnome-terminal 3.36.2


## 问题描述
用户用 python 开发，由于系统自带 python 版本（2.7）过低，需要升级高版本（>=3.0）。此时，用户安装 python3.7 之后，发现 gnome-terminal 无法正常打开，也没有任何提示。

## 问题分析

1.远程协助前，询问了用户有哪些操作（执行过哪些命令），用户告知，只是用 `apt install -y python3.7` 安装了下新版本的python, 然后配置了当前用户 ~/.bash_proc 文件中的 python 环境：

2.远程协助，使用 vscode 打开终端命令行工具，查看系统日志，发现报：
```bash
Traceback (most recent call last):
  File "/usr/bin/add-apt-repository", line 11, in <module>
    from softwareproperties.SoftwareProperties import SoftwareProperties, shortcut_handler
  File "/usr/lib/python3/dist-packages/softwareproperties/SoftwareProperties.py", line 67, in <module>
    from gi.repository import Gio
  File "/usr/lib/python3/dist-packages/gi/__init__.py", line 42, in <module>
    from . import _gi
ImportError: cannot import name '_gi' from partially initialized module 'gi' (most likely due to a circular import) (/usr/lib/python3/dist-packages/gi/__init__.py)
```

3.**问题所在关键**：根据日志的报错信息，查看 `/usr/lib/python3/dist-packages/gi/` 目录，发现文件 `_gi_cairo.cpython-38-x86_64-linux-gnu.so` 和 `_gi_cairo.cpython-38-x86_64-linux-gnu.so`

4.执行命令 python --version 查看 python 版本，发现当前系统环境调用的确实所 python 3.7，说明 python 安装成功。

5.**问题所在关键**：接着执行命令 `which python`,发现当前系统不仅只有 python2.7, python3.7 ,还有 python3.8 ,python3.9 ,也就是说客户的机器上目前总共是有 4 个版本的 python. 

## 解决过程

**验证无效**：搜了下 google ，发现都是告诉修改 `/usr/lib/python3/dist-packages/gi/` 目录下的文件 `_gi_cairo.cpython-38-x86_64-linux-gnu.so` 为 `_gi_cairo.cpython-37m-x86_64-linux-gnu.so`和 `_gi_cairo.cpython-38-x86_64-linux-gnu.so` 为 `_gi_cairo.cpython-37m-x86_64-linux-gnu.so` 

1.卸载 python3.8 和 3.9 ：
```bash
sudo apt purge -y python3.8 python3.9
```

卸载过程中有报错：
```bash
errors were encountered while processing python3.7 mercurial  gir1.2-dee  ubuntu-advantage-tools
```

解决方法：
```bash
sudo dpkg --remove --force-remove-reinstreq python3.7
sudo rm -rf /var/lib/dpkg/info/mercurial*
sudo rm -rf /var/lib/dpkg/info/gir1.2-glib-1.0*
sudo rm -rf /var/lib/dpkg/info/ubuntu-advantage-tools*
sudo apt autoremove -y
```

2.重新安装 python3.7、gnome-terminal 及删除的包 ： 
```bash
sudo apt reinstall -y python3.7 mercurial mercurial-common  gir1.2-dee1.0 nautilus-extension-gnome-terminal
```



## 后遗症

### ～/Desktop/ 目录下的文件不在桌面显示

解决方法：

1.打开 terminal,安装 `gnome-Shell-extension-prefs`

2.打开 extensions.gnome.org 网站，搜索 `Desktop Icons` 并安装（前提条件，浏览器要安装 browser-extension 插件）

### 不显示 Dock 菜单栏了

解决方法：

打开 extensions.gnome.org 网站，搜索 `Desh to Dock` 并安装（前提条件，浏览器要安装 browser-extension 插件）

### 软件中心无法打开

解决方法：

打开 terminal,安装 `ubuntu-software`