---
title: 关于磁盘大小小于256 G 如何正确安装 ESXI 8.0U2
author: leazhi
tags:
  - [vmware]
  - [esxi]
categories:
  - [vmware]
date: 2023-10-09 18:34:17
cover: 
discription: 
keywords: 
password: 
message: 
---


## 小于 256G 磁盘如何安装？

自 ESXI 7.0 以后，如果磁盘大小小于 256G , 那么在安装 ESXI  系统时，默认会将所有磁盘空间都给分配了。所以，在安装 ESXI，进入 WEB 页面安装虚拟机时，会发现左侧导航栏中的存储是没有设备的，这就会导致我们无法在此情况下安装虚拟机。

所以，正确的做法是：

在 ESXI 安装开始进入引导前先按键盘上的 `shift + o` 键 ，然后在显示的 `cdromBoot runWeasel` 后面输入 `autoPatitionOSDataSize=8192` ,这里的 8192 代表 8G，即给 ESXI 安装分配 8 个 G 的安装空间（8G 足够了）。这样分配后，后面安装完 ESXI，在 WEB 管理页面就不会没有空间安装虚拟机了！

