---
title: kvm 系列 001-kvm 安装
author: leazhi
tags:
  - [kvm]
categories:
  - [kvm]
date: 2023-10-24 11:45:25
cover: 
discription: 
keywords: 
password: 
message: 
---

## 系统环境
- 系统版本：6.5.0-9-generic #9-Ubuntu SMP PREEMPT_DYNAMIC Sat Oct  7 01:35:40 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
- 内核版本：ubuntu 23.10 (Mantic Minotaur)

## 网桥配置

1.编辑网卡配置文件 `/etc/netplan/01-network-manager-all.yaml`, 内容如下：
```yaml
network:
  version: 2
  #renderer: NetworkManager
  ethernets: 
    enp6s0: 
      dhcp4: false
      dhcp6: false
  bridges:
    br0: 
      interfaces: [enp6s0]
      dhcp4: false
      addresses: [192.168.3.120/24]
      gateway4: 192.168.3.1
      mtu: 1500
      #macaddress: 3c:7c:3f:b9:66:31
      #routes:
      #  - to: default
      #    via: 192.168.3.1
      #    metric: 100
      nameservers: 
        addresses: [8.8.8.8]
      parameters:
        stp: true
        forward-delay: 4
      dhcp6: false
```

2.执行命令 `sudo netplane apply` 使配置立即生效：
```bash
sudo netplan apply 
```

如果执行过程报 `/etc/netplan/01-network-manager-all.yaml` too open ，则执行：
```bash
sudo chmod 0600 /etc/netplan/01-network-manager-all.yaml
```

## 安装 kvm

1.执行命令，安装如下包：
```bash
sudo apt install -y qemu-system libvirt-daemon-system libvirt-daemon virtinst bridge-utils libosinfo-bin 
```

2.创建虚拟机文件存放目录,并将创建的目录所属组和主都修改当前登陆的普通用户：
```bash
sudo mkdir -p /data/kvm/images
sudo chown -R leazhi:leazhi /data/kvm/images
```

## 安装虚拟机

1.从 ubuntu 官方站点下载 iso 文件到本地；

2.执行命令安装：
```bash
virt-install --name ubuntu2204-001 \
--ram 2048 \
--disk path=/data/kvm/images/ubuntu2204-001.img,size=50 \
--vcpus 2 \
--os-variant ubuntu22.04 \
--network bridge=br0 \
--graphics none  \
--console pty,target_type=serial \
--extra-args 'console=ttyS0,115200n8' \
--arch x86_64 \
--autostart \
--location /home/leazhi/Downloads/ubuntu-22.04.3-live-server-amd64.iso,kernel=casper/vmlinuz,initrd=casper/initrd
```

### 安装报错

#### 错误一：stderr=failed to parse default acl file `/etc/qemu/bridge.conf'

执行安装命令报：
```bash
leazhi@ubuntu2310:~$ virt-install --name ubuntu2204-001 --ram 2048 --disk path=/data/kvm/images/ubuntu2204-001.img,size=50 --vcpus 2 --os-variant ubuntu22.04 --network bridge=br0 --graphics none  --console pty,target_type=serial --extra-args 'console=ttyS0,115200n8'   --arch x86_64 --autostart  --location /home/leazhi/Downloads/ubuntu-22.04.3-live-server-amd64\ \(1\).iso,kernel=casper/vmlinuz,initrd=casper/initrd

开始安装......
正在检索 ' vmlinuz'                                                                                        |    0 B  00:00:00 ... 
正在检索 ' initrd'                                                                                         |    0 B  00:00:00 ... 
正在分配 'ubuntu2204-001.img'                                                                              |    0 B  00:00:00 ... 
正在删除磁盘 'ubuntu2204-001.img'                                                                            |    0 B  00:00:00     
ERROR    /usr/lib/qemu/qemu-bridge-helper --use-vnet --br=br0 --fd=32: failed to communicate with bridge helper: stderr=failed to parse default acl file `/etc/qemu/bridge.conf'
: Transport endpoint is not connected
域安装似乎没有成功。
如果是这样，则可以通过运行以下命令来重新启动域：
  virsh --connect qemu:///session start ubuntu2204-001
否则请重新开始安装。
```

**<font color="red">解决方法</font>:**

1.在 /etc/ 目录下创建 qemu 目录：
```bash
sudo mkdir /etc/qemu
```

2.然后在创建的目录下新建 `bridge.conf` 文件，并加入如下内容：
```bash
allow virbr0
allow all
```

#### 错误二：stderr=failed to create tun device: Operation not permitted
执行安装命令报：
```bash
leazhi@ubuntu2310:~$ virt-install --name ubuntu2204-001 --ram 2048 --disk path=/data/kvm/images/ubuntu2204-001.img,size=50 --vcpus 2 --os-variant ubuntu22.04 --network bridge=br0 --graphics none  --console pty,target_type=serial --extra-args 'console=ttyS0,115200n8'   --arch x86_64 --autostart  --location /home/leazhi/Downloads/ubuntu-22.04.3-live-server-amd64\ \(1\).iso,kernel=casper/vmlinuz,initrd=casper/initrd

开始安装......
正在检索 ' vmlinuz'                                                                                        |    0 B  00:00:00 ... 
正在检索 ' initrd'                                                                                         |    0 B  00:00:00 ... 
正在分配 'ubuntu2204-001.img'                                                                              |    0 B  00:00:00 ... 
正在删除磁盘 'ubuntu2204-001.img'                                                                            |    0 B  00:00:00     
ERROR    /usr/lib/qemu/qemu-bridge-helper --use-vnet --br=br0 --fd=32: failed to communicate with bridge helper: stderr=failed to create tun device: Operation not permitted
: Transport endpoint is not connected
域安装似乎没有成功。
如果是这样，则可以通过运行以下命令来重新启动域：
  virsh --connect qemu:///session start ubuntu2204-001
否则请重新开始安装。
```

**<font color="red">解决方法</font>:**

1.先查看 `/usr/lib/qemu/qemu-bridge-helper` 权限：
```bash
leazhi@ubuntu2310:~$ sudo ls -al /usr/lib/qemu/qemu-bridge-helper 
-rwxr-xr-x 1 root root 696392 10月  4 06:13 /usr/lib/qemu/qemu-bridge-helper
```

2.然后将该文件的权限修改为 4755:
```bash
leazhi@ubuntu2310:~$ sudo chmod 4755 /usr/lib/qemu/qemu-bridge-helper 
leazhi@ubuntu2310:~$ sudo ls -al /usr/lib/qemu/qemu-bridge-helper 
-rwsr-xr-x 1 root root 696392 10月  4 06:13 /usr/lib/qemu/qemu-bridge-helper
```

3.再次执行安装命令即可！

## 安装完成后的操作

由于安装虚拟机使用了参数 `--console pty,target_type=serial` ,所以在安装重启完后，依旧是在虚拟机状态下，如果想退回到宿主机的终端下，则需要按键盘上的 `ctrl + ]` 键，如果想在终端中重新进入虚拟机，则有以下两种方式;

### 方式一：使用 `virsh console 虚拟机名称` 的方式

1.查看宿主机有哪些虚拟机：
```bash
leazhi@ubuntu2310:~$ virsh list
 Id   Name             State
--------------------------------
 4    ubuntu2204-001   running
```

2.然后执行 `virsh console ubuntu2204-001` 进入到虚拟机：
```bash
leazhi@ubuntu2310:~$ virsh console ubuntu2204-001 
Connected to domain 'ubuntu2204-001'
Escape character is ^] (Ctrl + ])           # 断开连接按 ctrl + ],继续进入按 回车键

ubuntu2204-001 login: root
Password: 

Login incorrect
ubuntu2204-001 login: ubuntu
Password: 
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-87-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Oct 24 03:31:34 AM UTC 2023

  System load:  0.02685546875      Processes:               130
  Usage of /:   20.6% of 23.45GB   Users logged in:         0
  Memory usage: 10%                IPv4 address for enp1s0: 192.168.3.139
  Swap usage:   0%

Expanded Security Maintenance for Applications is not enabled.

27 updates can be applied immediately.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ubuntu@ubuntu2204-001:~$            # 断开虚拟机连接
leazhi@ubuntu2310:~$ 
```

### 方式二：使用 SSH 方式连接

这种方式连接的话，需要事先知道虚拟机的IP 地址，然后使用 `ssh username@ip` 的方式连接