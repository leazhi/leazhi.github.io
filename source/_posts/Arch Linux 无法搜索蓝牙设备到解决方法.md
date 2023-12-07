---
title: Arch Linux 无法搜索蓝牙设备到解决方法
author: leazhi
tags:
  - [Arch Linux]
  - [Linux]
categories:
  - [Arch Linux]
  - [Linux]
date: 2023-08-08 21:24:43
cover: 
discription: 
keywords: 
password: 
message: 
---

## 系统环境

### OS 环境：
```bash
Linux Arch 6.4.8-arch1-1 #1 SMP PREEMPT_DYNAMIC Thu, 03 Aug 2023 16:02:01 +0000 x86_64 GNU/Linux    
```
### 桌面环境：
KDE 5.27.7

## 蓝牙无法搜索设备

根据上面提供到安装文档，安装完成后，发现蓝牙服务启动成功，在蓝牙配置里面始终都搜索不到蓝牙设备。

### 解决方法：

安装
```bash
sudo pacman -S pavucontrol pulseaudio-alsa pulseaudio-bluetooth bluez bluez-utils
```

然后重启下系统即可！

参考的安装文档：[Arch Linux 安装使用教程 - ArchTutorial - Arch Linux Studio](https://archlinuxstudio.github.io/ArchLinuxTutorial/#/)