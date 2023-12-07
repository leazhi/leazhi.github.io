---
title: centos8.3 无法启动，在救援模式下恢复
author: leazhi
tags:
  - [centos]
  - [redhat]
categories:
  - [centos]
  - [redhat]
date: 2023-09-22 22:09:05
cover: 
discription: 
keywords: centos, centos8,rescure
password: 
message: 
---


## 系统环境：
- OS：CentOS-8.3.2011-x86_64-minimal
- Kernal：4.18.0-240.el8.x86_64 #1 SMP Fri Sep 25 19:48:47 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

**系统安装说明：**
- 1.采用的最小化安装；
- 2.安装时没有进行手动分区，而是采用自动分区模式（默认就 2 个分区，一个为 swap ,一个为 root）;
- 3.系统安装完成后，默认的文件系统类型为 lvm ,分区格式为 xfs;
  
```bash
[root@localhost ~]# df -lTh
Filesystem          Type      Size  Used Avail Use% Mounted on
devtmpfs            devtmpfs  354M     0  354M   0% /dev
tmpfs               tmpfs     373M     0  373M   0% /dev/shm
tmpfs               tmpfs     373M  9.8M  363M   3% /run
tmpfs               tmpfs     373M     0  373M   0% /sys/fs/cgroup
/dev/mapper/cl-root xfs        17G  1.8G   16G  11% /
/dev/sda1           xfs      1014M  177M  838M  18% /boot
tmpfs               tmpfs      75M     0   75M   0% /run/user/0
tmpfs               tmpfs      75M     0   75M   0% /run/user/1000
```

 ```bash
[root@localhost ~]# blkid
/dev/mapper/cl-root: UUID="0160a306-be2e-4c6e-83b0-9f0bb95d22bc" BLOCK_SIZE="4096" TYPE="xfs"
/dev/sda2: UUID="Wy5zhS-j38W-i7me-Fzc4-XgOu-c0Fx-eof4d7" TYPE="LVM2_member" PARTUUID="3e24b1c6-02"
/dev/sda1: UUID="cc5d6ffd-cfae-4fbf-9eca-dd68436f9187" BLOCK_SIZE="4096" TYPE="xfs" PARTUUID="3e24b1c6-01"
/dev/sr0: BLOCK_SIZE="2048" UUID="2020-11-18-21-40-17-00" LABEL="CentOS-8-3-2011-x86_64-dvd" TYPE="iso9660" PTUUID="6e66cf88" PTTYPE="dos"
/dev/mapper/cl-swap: UUID="d7315862-65a8-47d4-b1a7-f60f8a635297" TYPE="swap"
```

## 故障模拟

模拟故障，手动删除 `/boot/grub2/grub.cfg` 文件：
```bash
rm -rf /boot/grub2/grub.cfg
```

然后重启系统。无法进入系统，如下图：
 ![grub|left](../static/img/20230923/20230922010.jpg#left)  

## 解决方法

1.插入安装盘，进入救援模式：
 ![](../static/img/20230923/20230922001.jpg)  

![](../static/img/20230923/20230922002.jpg)  

![](../static/img/20230923/20230922003.jpg)  

![](../static/img/20230923/20230922004.jpg)  

到这里，发现无法挂载 Linux 分区，原因是找不到 Linux 分区。

2.扫描磁盘，确认分区：

2.1.输入命令 `lvm vgscan` 扫描 LVM 卷：
![](../static/img/20230923/20230923001.jpg)  

2.2.接着，输入命令 `lvm vgchange -ay` 激活 LVM 卷：
![](../static/img/20230923/20230923002.jpg)  

2.3.输入命令`lvm lvdisplay` ，确认LVM卷组和逻辑卷的名称:
![](../static/img/20230923/20230923003.jpg)  

3.挂在磁盘分区：

3.1.输入命令 `mount /dev/cl/root /mnt` 挂载逻辑卷：
![](../static/img/20230923/20230923004.jpg)  

接下来就是关键绑定挂在，必不可少，否则会出现

3.2.绑定挂载以下目录：
```bash
mount --bind /run /mnt/run
mount --bind /dev /mnt/dev
mount --bind /proc /mnt/proc
mount --bind /sys /mnt/sys
mount --bind /boot /mnt/boot
```

![](../static/img/20230923/20230923005.jpg)  

3.3.以 bash 的方式挂在系统，执行命令 `chroot /mnt bash`
![](../static/img/20230923/20230923006.jpg)  

3.4.以命令的方式挂在分区 `mount /dev/sda1`
![](../static/img/20230923/20230923007.jpg)  

4.最后安装引导 `grub2-mkconfig -o /boot/grub2/grub.cfg` （**注意：图片中的截图/boot/grub2/grub.cfg 输成了 /boot/grub2/grub2.cfg， 多输出了数字 2，会导致修复失败。！！！切记，不要输入这个数字！！！。下面的步骤是我重新修复了一遍的成功的截图**）,如下图：
![](../static/img/20230923/20230923008.jpg)  

5.引导安装完成，接着执行 exit 退出 chroot ：
![](../static/img/20230923/20230923009.jpg)  

6.输入 reboot 命令，重启系统。然后启动加载时选择从本地硬盘启动：
![](../static/img/20230923/20230923010.jpg)  

![](../static/img/20230923/20230923011.jpg)  

![](../static/img/20230923/20230923012.jpg)  

![](../static/img/20230923/20230923013.jpg)  

## 总结：

在网上搜了很多资料，基本都是千篇一律，但都没能解决我的问题。之所以如此，有可能跟我实验环境有区别！不想多说！
 
## 参考：
- 1.[Trying to reinstall GRUB 2, cannot find a device for /boot (is /dev mounted?)](https://askubuntu.com/questions/235362/trying-to-reinstall-grub-2-cannot-find-a-device-for-boot-is-dev-mounted)
- 2.[进入救援模式，可以找到 Linux 分区的解决方法！本人未测试！！！---> 《centos进入救援模式并修复文件系统（7、8）》](https://www.cnblogs.com/xiongty/p/15593438.html)
- 3.[这篇文章给了一些思路，可以参考！！！ --->《解决linux无法启动，进入救援模式也报错：you don‘t have any linux partitions的问题》](https://blog.csdn.net/jiaohuizhuang6019/article/details/109490129)
- 4.[这里面的案例值得实践下！！！--->《如何在 CentOS 8 中恢复 GRUB 2 引导加载程序》](https://www.onitroad.com/jc/linux/centos/troubleshooting/how-to-recover-grub-2-bootloader-in-centos-8.html)