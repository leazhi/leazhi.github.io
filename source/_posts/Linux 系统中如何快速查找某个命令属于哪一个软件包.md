---
title: Linux 系统中如何快速查找某个命令属于哪一个软件包
author: leazhi
tags:
  - [linux]
  - [ubuntu]
  - [centos]
categories:
  - [linux]
  - [ubuntu]
  - [centos]
date: 2023-12-01 08:34:56
cover: 
discription: 
keywords: 
password: 
message: 
---

## Ubuntu 系统

### 方法一：对未安装过的命令进行搜索

1.首先需要安装 `apt-file` 包:
```bash
sudo apt install -y apt-file
```

2.其次，需要更新下 apt-file 缓存数据：
```bash
sudo apt-file update
```

3.最后，就可以使用 apt-file 来获取命令属于哪个包了？：
```bash
┌─[leazhi@ubuntuhome] - [~] - [2023-12-01 08:37:39]
└─[0] sudo apt-file search iostat  |egrep '/usr/bin/iostat'
pcp-import-iostat2pcp: /usr/bin/iostat2pcp
sysstat: /usr/bin/iostat
```

### 方法二：对已安装过的命令进行搜索

1.首先，使用 `witch` 命令获取命令所在的路径：
```bash
┌─[leazhi@ubuntuhome] - [~] - [2023-12-01 08:45:54]
└─[0] which sar        
/usr/bin/sar
```

2.其次，使用 `which -S PATH` 获取查看命令是否是软链接的：
```bash
┌─[leazhi@ubuntuhome] - [~] - [2023-12-01 08:46:10]
└─[0] which -S /usr/bin/sar        
/usr/bin/sar -> /etc/alternatives/sar -> /usr/bin/sar.sysstat
```

3.最后，使用 dpkg 获取命令属于哪个包
```bash
┌─[leazhi@ubuntuhome] - [~] - [2023-12-01 08:46:20]
└─[0]  dpkg -S  /usr/bin/sar.sysstat
sysstat: /usr/bin/sar.sysstat
```

## CentOS 系统

由于暂时没有该环境，所以先记录下，后续有环境了，再补充。
