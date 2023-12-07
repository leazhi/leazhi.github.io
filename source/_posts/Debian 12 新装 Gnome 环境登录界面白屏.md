---
title: Debian 12 新装 Gnome 环境登录界面白屏
author: leazhi
tags:
  - [linux]
  - [debian]
categories:
  - [debian]
date: 2023-09-16 21:51:38
cover: 
discription: 
keywords: 
password: 
message: 
---

## 硬件配置
- 主板：ASUS ROG STRIX Z490-A GAMING
- CPU: Intel® Core™ i7-10700K × 16
- 内存: 64G
- 显卡: Intel® UHD Graphics 630 (CML GT2)
- 磁盘： Predator SSD GM7000 1TB

## 问题描述

新装的 Debian 12 Gnome 环境，装完系统重启，登录界面白屏（没有桌面），按鼠标键盘任意键都没反应。但是系统没有死机！

## 问题分析

根据以往经验，基本可以判断是显卡驱动问题，于是开始排查显卡：

1.CRTL+ALT+F3 , 登录系统。在 /etc/modproc.d/ 目录下新建blacklist-nouveau.conf 文件，内容如下：

```
blacklist nouveau
options nouveau modeset=0
```

2.执行 update-initramfs -u 命令，然后重启系统。重启完成后，可以看到登录窗口，也可以输入账号密码正常登录了。但是又出现新问题了，不能投屏（依旧是显卡驱动）

## 显卡安装

根据以往查看到显卡型号，到NVIDIA 官方站点下载对应的版本驱动，在命令行模式下进行安装。结果报以下错误：

### 故障一：ERROR: You appear to be running an X server; please exit X before installing.

报该错是因为目前运行的是图形化界面，需要先退出图形界面，再安装驱动。

**解决方法**：
```bash
systemctl stop gdm3.service gdm.service
```

### 故障二： ERROR: Unable to find the development tool `cc` in your path;

报改错是因为系统缺少 gcc 编译器，需要先安装 gcc 编译器。

**解决方法**：
```bash
apt-get install gcc
```

### 故障三：ERROR: Unable to find the development tool `make` in your path; please make sure that you have the package 'make' installed.

报改错是因为系统缺少 make 编译器，需要先安装 make 编译器。

**解决方法**：
```bash
apt install -y make
```

### 故障四： ERROR: Unable to find the kernel source tree for the currently running kernel.  Please make sure you have installed the kernel source files for your kernel and that they are properly configured; on Red Hat Linux systems, for example, be sure you have the 'kernel-source' or 'kernel-devel' RPM installed.  If you know the correct kernel source files are installed, you may specify the kernel source path with the '--kernel-source-path' command line option.

**这一步可能走错了，或许安装 kernel-dev 会更容易解决问题，未尝试，期待下次遇见再尝试！！！**

报改错是因为系统缺少内核源码或者内核库文件，需要先安装内核源码或者内核库文件。

**解决方法**：
```bash
apt install -y linux-headers-6.1

tar -xf /usr/src/linux-source-6.1.tar.xf -C /usr/local/src/

./NVIDIA-Linux-x86_64-525.116.03.run --kernel-source-path=/usr/local/src/linux-source-6.1/
```

### 故障五：ERROR: Neither the '/usr/local/src/linux-source-6.1//include/linux/version.h' nor the '/usr/local/src/linux-source-6.1//include/generated/uapi/linux/version.h' kernel header file exists. The most likely reason for this is that the kernel source files in '/usr/local/src/linux-source-6.1/' have not been configured.

报改错是因为内核源码没有编译配置，需要先编译内核源码。

**解决方法**：
```bash
root@debian-desktop:~# cd /usr/local/src/linux-source-6.1/
root@debian-desktop:/usr/local/src/linux-source-6.1# make menuconfig
```

### 故障六：

```bash
root@debian-desktop:/usr/local/src/linux-source-6.1# make menuconfig
  HOSTCC  scripts/basic/fixdep
*
* Unable to find the ncurses package.
* Install ncurses (ncurses-devel or libncurses-dev
* depending on your distribution).
*
* You may also need to install pkg-config to find the
* ncurses installed in a non-default location.
*
make[1]: *** [scripts/kconfig/Makefile:212: scripts/kconfig/mconf-cfg] Error 1
make: *** [Makefile:715: menuconfig] Error 2
```

**解决方法**：

```bash
apt install -y pkg-config libncurses-dev
```

接着继续编译内核源码：

```bash
make menuconfig &&    make && make install
```

**!!! 至此，问题依旧没有解决，因为 make 时间太长了，终止了编译重新安装了 Kali Linux 2023.03**