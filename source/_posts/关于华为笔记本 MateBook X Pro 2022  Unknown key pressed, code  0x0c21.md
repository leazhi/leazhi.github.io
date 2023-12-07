---
title: 关于华为笔记本 MateBook X Pro 2022  Unknown key pressed, code  0x0c21
author: leazhi
tags:
- [huwei matebook x pro 2022]
- [debian]
categories:
- [huwei matebook x pro 2022]
- [debian]
date: 2023-08-06 09:51:36
cover: 
discription: 
keywords: 
password: 
message: 
---

{% note success simple %}
**系统及硬件环境说明：**
硬件型号：HUAWEI MRG-WXX
内存：16GB
处理器：11th Gen Intel® Core™ i7-1195G7 × 8
显卡：Mesa Intel® Xe Graphics (TGL GT2)
磁盘：512GB

操作系统名称：Debian GNU/Linux 12 (bookworm)
操作系统类型：64位
GNOME 版本：43.6
窗口系统：Wayland
{% endnote %}


## 起因

华为 MateBook X Pro 2022 安装 Debian 12 ，发现一旦电脑休眠时间过长，就无法唤醒（无论是使用鼠标点击还是按电脑键盘，只有键盘指示灯亮，但是屏幕是黑的）

## 日志分析：

### demsg -T 硬件日志

日志详情：

```bash
oot@debian:~# dmesg -T 
[Sun Aug  6 09:26:12 2023] microcode: microcode updated early to revision 0x2a, date = 2022-12-28
[Sun Aug  6 09:26:12 2023] Linux version 6.1.0-10-amd64 (debian-kernel@lists.debian.org) (gcc-12 (Debian 12.2.0-14) 12.2.0, GNU ld (GNU Binutils for Debian) 2.40) #1 SMP PREEMPT_DYNAMIC Debian 6.1.38-2 (2023-07-27)
...
[Sun Aug  6 09:26:13 2023] ACPI BIOS Error (bug): Could not resolve symbol [\S6DE], AE_NOT_FOUND (20220331/psargs-330)
[Sun Aug  6 09:26:13 2023] ACPI Error: Aborting method \_SB.PC00.LPCB.H_EC.SEN6._STA due to previous error (AE_NOT_FOUND) (20220331/psparse-529)
[Sun Aug  6 09:26:13 2023] ACPI BIOS Error (bug): Could not resolve symbol [\S7DE], AE_NOT_FOUND (20220331/psargs-330)
[Sun Aug  6 09:26:13 2023] ACPI Error: Aborting method \_SB.PC00.LPCB.H_EC.SEN7._STA due to previous error (AE_NOT_FOUND) (20220331/psparse-529)
[Sun Aug  6 09:26:13 2023] ACPI BIOS Error (bug): Could not resolve symbol [\S8DE], AE_NOT_FOUND (20220331/psargs-330)
[Sun Aug  6 09:26:13 2023] ACPI Error: Aborting method \_SB.PC00.LPCB.H_EC.SEN8._STA due to previous error (AE_NOT_FOUND) (20220331/psparse-529)
[Sun Aug  6 09:26:13 2023]  nvme0n1: p1 p2 p3 p4 p5 p6
...
[Sun Aug  6 09:26:16 2023] iwlwifi 0000:00:14.3: TLV_FW_FSEQ_VERSION: FSEQ Version: 89.3.35.37
[Sun Aug  6 09:26:16 2023] iwlwifi 0000:00:14.3: firmware: failed to load iwl-debug-yoyo.bin (-2)
[Sun Aug  6 09:26:16 2023] firmware_class: See https://wiki.debian.org/Firmware for information about missing firmware
[Sun Aug  6 09:26:16 2023] iwlwifi 0000:00:14.3: firmware: failed to load iwl-debug-yoyo.bin (-2)
[Sun Aug  6 09:26:16 2023] iwlwifi 0000:00:14.3: loaded firmware version 72.daa05125.0 QuZ-a0-hr-b0-72.ucode op_mode iwlmvm
...

[Sun Aug  6 09:26:16 2023] iwlwifi 0000:00:14.3: TLV_FW_FSEQ_VERSION: FSEQ Version: 89.3.35.37
[Sun Aug  6 09:26:16 2023] iwlwifi 0000:00:14.3: firmware: failed to load iwl-debug-yoyo.bin (-2)
[Sun Aug  6 09:26:16 2023] firmware_class: See https://wiki.debian.org/Firmware for information about missing firmware
[Sun Aug  6 09:26:16 2023] iwlwifi 0000:00:14.3: firmware: failed to load iwl-debug-yoyo.bin (-2)
[Sun Aug  6 09:26:16 2023] iwlwifi 0000:00:14.3: loaded firmware version 72.daa05125.0 QuZ-a0-hr-b0-72.ucode op_mode iwlmvm
```


### journalctl 查看系统日志
```bash
root@debian:~# journalctl -b -1 -n 100
Aug 06 08:58:04 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:58:22 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:58:33 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:58:34 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:58:42 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:58:47 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:58:50 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:58:55 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:58:57 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:00 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:01 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:03 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:06 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:10 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:15 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:25 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:29 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:32 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:33 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:33 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:35 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:36 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:37 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:38 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:39 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:40 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:42 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:44 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:50 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:50 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:52 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:53 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:54 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:55 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:57 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 08:59:59 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:02 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:04 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:05 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:08 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:12 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:13 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:14 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:28 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:28 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:32 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:35 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:37 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:38 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:41 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:46 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:54 debian systemd[1]: Starting lmt-poll.service - Laptop Mode Tools - Battery Polling Service...
Aug 06 09:00:54 debian sh[14329]: laptop-mode: Battery polling disabled
Aug 06 09:00:54 debian systemd[1]: lmt-poll.service: Deactivated successfully.root@debian:~# journalctl -b -1 -n 100

Aug 06 09:00:38 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:41 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:46 debian kernel: input input10: Unknown key pressed, code: 0x02c1
Aug 06 09:00:54 debian systemd[1]: Starting lmt-poll.service - Laptop Mode Tools - Battery Polling Service...
Aug 06 09:00:54 debian sh[14329]: laptop-mode: Battery polling disabled
Aug 06 09:00:54 debian systemd[1]: lmt-poll.service: Deactivated successfully.
Aug 06 09:00:54 debian systemd[1]: Finished lmt-poll.service - Laptop Mode Tools - Battery Polling Service.
Aug 06 09:00:55 debian systemd-logind[2780]: The system will suspend now!
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.0495] manager: sleep: sleep requested (sleeping: no  enabled: yes)
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.0495] device (p2p-dev-wlp0s20f3): state change: disconnected -> unmanaged (reason 'sleeping', sys-iface-state: 'managed')
Aug 06 09:00:55 debian ModemManager[2862]: <info>  [sleep-monitor-systemd] system is about to suspend
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.0497] manager: NetworkManager state is now ASLEEP
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.0498] device (wlp0s20f3): state change: activated -> deactivating (reason 'sleeping', sys-iface-state: 'managed')
Aug 06 09:00:55 debian google-chrome.desktop[5770]: [5754:5877:0806/090055.050721:ERROR:connection_factory_impl.cc(471)] ConnectionHandler failed with net error: -2
Aug 06 09:00:55 debian dbus-daemon[2770]: [system] Activating via systemd: service name='org.freedesktop.nm_dispatcher' unit='dbus-org.freedesktop.nm-dispatcher.service' requested by ':1.14'>
Aug 06 09:00:55 debian systemd[1]: Starting NetworkManager-dispatcher.service - Network Manager Script Dispatcher Service...
Aug 06 09:00:55 debian dbus-daemon[2770]: [system] Successfully activated service 'org.freedesktop.nm_dispatcher'
Aug 06 09:00:55 debian systemd[1]: Started NetworkManager-dispatcher.service - Network Manager Script Dispatcher Service.
Aug 06 09:00:55 debian kernel: wlp0s20f3: deauthenticating from 62:98:9a:30:96:0b by local choice (Reason: 3=DEAUTH_LEAVING)
Aug 06 09:00:55 debian wpa_supplicant[2829]: wlp0s20f3: CTRL-EVENT-DISCONNECTED bssid=62:98:9a:30:96:0b reason=3 locally_generated=1
Aug 06 09:00:55 debian wpa_supplicant[2829]: wlp0s20f3: CTRL-EVENT-DSCP-POLICY clear_all
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.2289] device (wlp0s20f3): supplicant interface state: completed -> disconnected
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.2290] device (wlp0s20f3): state change: deactivating -> disconnected (reason 'sleeping', sys-iface-state: 'managed')
Aug 06 09:00:54 debian systemd[1]: Finished lmt-poll.service - Laptop Mode Tools - Battery Polling Service.
Aug 06 09:00:55 debian systemd-logind[2780]: The system will suspend now!
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.0495] manager: sleep: sleep requested (sleeping: no  enabled: yes)
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.0495] device (p2p-dev-wlp0s20f3): state change: disconnected -> unmanaged (reason 'sleeping', sys-iface-state: 'managed')
Aug 06 09:00:55 debian ModemManager[2862]: <info>  [sleep-monitor-systemd] system is about to suspend
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.0497] manager: NetworkManager state is now ASLEEP
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.0498] device (wlp0s20f3): state change: activated -> deactivating (reason 'sleeping', sys-iface-state: 'managed')
Aug 06 09:00:55 debian google-chrome.desktop[5770]: [5754:5877:0806/090055.050721:ERROR:connection_factory_impl.cc(471)] ConnectionHandler failed with net error: -2
Aug 06 09:00:55 debian dbus-daemon[2770]: [system] Activating via systemd: service name='org.freedesktop.nm_dispatcher' unit='dbus-org.freedesktop.nm-dispatcher.service' requested by ':1.14' (uid=0 pid=2827 comm="/usr/sbin/NetworkManager --no-daemon")
Aug 06 09:00:55 debian systemd[1]: Starting NetworkManager-dispatcher.service - Network Manager Script Dispatcher Service...
Aug 06 09:00:55 debian dbus-daemon[2770]: [system] Successfully activated service 'org.freedesktop.nm_dispatcher'
Aug 06 09:00:55 debian systemd[1]: Started NetworkManager-dispatcher.service - Network Manager Script Dispatcher Service.
Aug 06 09:00:55 debian kernel: wlp0s20f3: deauthenticating from 62:98:9a:30:96:0b by local choice (Reason: 3=DEAUTH_LEAVING)
Aug 06 09:00:55 debian wpa_supplicant[2829]: wlp0s20f3: CTRL-EVENT-DISCONNECTED bssid=62:98:9a:30:96:0b reason=3 locally_generated=1
Aug 06 09:00:55 debian wpa_supplicant[2829]: wlp0s20f3: CTRL-EVENT-DSCP-POLICY clear_all
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.2289] device (wlp0s20f3): supplicant interface state: completed -> disconnected
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.2290] device (wlp0s20f3): state change: deactivating -> disconnected (reason 'sleeping', sys-iface-state: 'managed')
Aug 06 09:00:55 debian avahi-daemon[2767]: Withdrawing address record for fe80::be17:b8ff:fe8c:1653 on wlp0s20f3.
Aug 06 09:00:55 debian avahi-daemon[2767]: Leaving mDNS multicast group on interface wlp0s20f3.IPv6 with address fe80::be17:b8ff:fe8c:1653.
Aug 06 09:00:55 debian avahi-daemon[2767]: Interface wlp0s20f3.IPv6 no longer relevant for mDNS.
Aug 06 09:00:55 debian google-chrome.desktop[5770]: [5754:5877:0806/090055.230095:ERROR:connection_factory_impl.cc(428)] Failed to connect to MCS endpoint with error -105
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.2485] dhcp4 (wlp0s20f3): canceled DHCP transaction
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.2485] dhcp4 (wlp0s20f3): activation: beginning transaction (timeout in 45 seconds)
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.2485] dhcp4 (wlp0s20f3): state changed no lease
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.2486] dhcp6 (wlp0s20f3): canceled DHCP transaction
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.2486] dhcp6 (wlp0s20f3): activation: beginning transaction (timeout in 45 seconds)
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.2486] dhcp6 (wlp0s20f3): state changed no lease
Aug 06 09:00:55 debian avahi-daemon[2767]: Interface wlp0s20f3.IPv4 no longer relevant for mDNS.
Aug 06 09:00:55 debian avahi-daemon[2767]: Leaving mDNS multicast group on interface wlp0s20f3.IPv4 with address 192.168.3.69.
Aug 06 09:00:55 debian avahi-daemon[2767]: Withdrawing address record for 192.168.3.69 on wlp0s20f3.
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.2743] device (wlp0s20f3): set-hw-addr: set MAC address to EE:CD:31:D5:9F:35 (scanning)
Aug 06 09:00:55 debian avahi-daemon[2767]: Joining mDNS multicast group on interface wlp0s20f3.IPv4 with address 192.168.3.69.
Aug 06 09:00:55 debian avahi-daemon[2767]: New relevant interface wlp0s20f3.IPv4 for mDNS.
Aug 06 09:00:55 debian avahi-daemon[2767]: Registering new address record for 192.168.3.69 on wlp0s20f3.IPv4.
Aug 06 09:00:55 debian avahi-daemon[2767]: Withdrawing address record for 192.168.3.69 on wlp0s20f3.
Aug 06 09:00:55 debian avahi-daemon[2767]: Leaving mDNS multicast group on interface wlp0s20f3.IPv4 with address 192.168.3.69.
Aug 06 09:00:55 debian avahi-daemon[2767]: Interface wlp0s20f3.IPv4 no longer relevant for mDNS.
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.3138] device (wlp0s20f3): state change: disconnected -> unmanaged (reason 'sleeping', sys-iface-state: 'managed')
Aug 06 09:00:55 debian NetworkManager[2827]: <info>  [1691283655.4417] device (wlp0s20f3): set-hw-addr: reset MAC address to BC:17:B8:8C:16:53 (unmanage)
Aug 06 09:00:55 debian systemd[1]: Reached target sleep.target - Sleep.
Aug 06 09:00:55 debian wpa_supplicant[2829]: p2p-dev-wlp0s20: CTRL-EVENT-DSCP-POLICY clear_all
Aug 06 09:00:55 debian wpa_supplicant[2829]: p2p-dev-wlp0s20: CTRL-EVENT-DSCP-POLICY clear_all
Aug 06 09:00:55 debian wpa_supplicant[2829]: nl80211: deinit ifname=p2p-dev-wlp0s20 disabled_11b_rates=0
Aug 06 09:00:55 debian systemd[1]: Starting systemd-suspend.service - System Suspend...
Aug 06 09:00:55 debian systemd-sleep[14359]: Entering sleep state 'suspend'...
Aug 06 09:00:55 debian kernel: PM: suspend entry (s2idle)
```

## 解决方法一：经测试不生效

网上同样的问题解决方案链接：[HUAWEI MateBook X Pro 2022 “未知按键按下，代码 0x02c1”](https://bbs.archlinux.org/viewtopic.php?id=282658)      
需要安装该驱动：[华为WMI笔记本电脑附加Linux驱动程序](https://github.com/aymanbagabas/Huawei-WMI)

1.先查看自己系统内核的版本：
```bash
root@debian:~# uname -a
Linux debian 6.1.0-10-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.38-2 (2023-07-27) x86_64 GNU/Linux
```

2.根据官方指导，内核版本小于等于 5.0 的，就去安装标签v1.0（内核 <5.0）或标签v3.2（如果您运行版本 5.0），如果你的内核版本大于等于 5.1的，那么就可以去安装最新版本的。我这里安装的 v3.4

3.根据指导，我们进入[驱动下载页面](https://github.com/aymanbagabas/Huawei-WMI/releases)，下载 huawei-wmi-3.4-source-only.dkms.tar.gz 到当前用户的下载目录。

4.安装 dkms 命令：
```bash
leazhi@debian:~/下载$ sudo apt install -y dkms
```

5.安装官方给出的安装命令进行驱动的安装：
```bash
leazhi@debian:~/下载$ sudo dkms ldtarball --archive=huawei-wmi-3.4-source-only.dkms.tar.gz
[sudo] leazhi 的密码：

Loading tarball for huawei-wmi-3.4
Creating symlink /var/lib/dkms/huawei-wmi/3.4/source -> /usr/src/huawei-wmi-3.4

# 安装：
leazhi@debian:~/下载$ sudo dkms install huawei-wmi/3.4
Sign command: /usr/lib/linux-kbuild-6.1/scripts/sign-file
Signing key: /var/lib/dkms/mok.key
Public certificate (MOK): /var/lib/dkms/mok.pub
Certificate or key are missing, generating self signed certificate for MOK...

Building module:
Cleaning build area...
make -j8 KERNELRELEASE=6.1.0-10-amd64 -C /lib/modules/6.1.0-10-amd64/build M=/var/lib/dkms/huawei-wmi/3.4/build...
Signing module /var/lib/dkms/huawei-wmi/3.4/build/huawei-wmi.ko
Cleaning build area...

huawei-wmi.ko:
Running module version sanity check.
Module version  for huawei-wmi.ko
exactly matches what is already found in kernel 6.1.0-10-amd64.
DKMS will not replace this module.
You may override by specifying --force.
depmod...


# 重启系统
leazhi@debian:~/下载$ sudo reboot
```

{% note warning simple %}
**注意：**安装完该驱动后，又会导致蓝牙无法使用
{% endnote %}


## 解决方法二：测试中

1.安装 pm-utils 包，如下
```bash
sudo apt install pm-utils
```

2.然后禁用挂起功能：

2.1.编辑 /etc/systemd/sleep.conf ,将 AllowSuspend 和 
AllowSuspendThenHibernate 都设置为 no:
```bash
leazhi@debian:/data/gitlab$ egrep -v '^$|^#' /etc/systemd/sleep.conf 
[Sleep]
AllowSuspend=no
AllowSuspendThenHibernate=no
```

2.2.执行命令：
```bash
sudo systemctl mask suspend.target hibernate.target 
```

3.编辑 /etc/default/grub 文件，修改 grub 启动参数, 加上 pcie_aspm=off 参数，

3.1.
```bash
root@debian:~# egrep -v '^$|^#' /etc/default/grub
GRUB_DEFAULT=0
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet pcie_aspm=off"
GRUB_CMDLINE_LINUX=""
```

3.2.更新 grub:
```bash
update-grub
```