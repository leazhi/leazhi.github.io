---
title: python3 升、降级的正确方式
author: leazhi
tags:
  - [python]
categories:
  - [python]
date: 2023-10-14 08:31:44
cover: 
discription: 
keywords: 
password: 
message: 
---

## 系统环境

- 系统版本：ubuntu-23.10-desktop-amd64.iso
- 内核版本： 6.5.0-9-generic #9-Ubuntu SMP PREEMPT_DYNAMIC Sat Oct  7 01:35:40 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
- python3 版本：Python 3.11.6

##  python 降级

为什么要对 python 做降级处理？肯定是因为某些项目针对某一个 python 版本兼容性更好。

### openssl 的安装

**在开始安装之前，确保系统中没有安装过 openssl。如果有，则需要将之前的 openssl 命令重命名**
```bash
sudo mv /usr/bin/openssl /usr/bin/openssl.bak
```

1.[点这里](https://www.openssl.org/source/openssl-1.1.1w.tar.gz) 从官方站点下载指定的 openssl 源码包到 /usr/local/src/ 目录下：
```bash
sudo wget -O /usr/local/src/openssl-1.1.1w.tar.gz https://www.openssl.org/source/openssl-1.1.1w.tar.gz
```

2.将下载好的源码包解压到下载目录：
```bash
sudo tar -zxvf /usr/local/src/openssl-1.1.1w.tar.gz -C /usr/local/src/
```

3.进入 openssl 解压目录：
```bash
cd /usr/local/src/openssl-1.1.1w
```

4.执行 config 命令:
```bash
sudo ./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl/ssl
```

5.执行 make 命令：
```bash
sudo make
```

6.执行 make install 命令：
```bash
sudo make install
```

7.安装完成后，将 libssl.so.1.1 和 libcrypto.so.1.1 两个文件软链接到 /lib/ 目录下：
```bash
sudo ln -s /usr/local/openssl/lib/libssl.so.1.1 /lib/libssl.so.1.1
sudo ln -s /usr/local/openssl/lib/libcrypto.so.1.1 /lib/libcrypto.so.1.1
```

8.编辑 /etc/ld.so.conf 文件，在最后一行添加 /usr/local/openssl/lib
```bash
sudo echo '/usr/local/openssl/lib' >> /etc/ld.so.conf
```

9.执行 ldconfig 命令：
```bash
sudo ldconfig -v
```

10.在 /etc/profile.d 目录下创建 openssl.sh 文件，并添加以下内容：
```bash
export PATH=/usr/local/openssl/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/openssl/lib:$LD_LIBRARY_PATH
```

11.执行 source /etc/profile 命令，使配置生效。


12.执行 openssl version 命令，查看 openssl 版本：
```bash
leazhi@leazhi-System-Version:~$ openssl version
OpenSSL 1.1.1w  11 Sep 2023
```


### python 的安装

1.[点这里](https://www.python.org/ftp/python/3.10.6/Python-3.10.6.tar.xz) 从官方站点下载指定版本的 python 源码包到 /usr/local/src/ 目录下：
```bash
sudo wget -O /usr/local/src/Python-3.10.6.tar.xz https://www.python.org/ftp/python/3.10.6/Python-3.10.6.tar.xz
```

2.将下载好的源码包解压到下载目录：
```bash
sudo tar -xvf /usr/local/src/Python-3.10.6.tar.xz -C /usr/local/src/
```

3.进入 python 解压目录：
```bash
cd /usr/local/src/Python-3.10.6
```

4.执行 configure 命令:
```bash
sudo ./configure --prefix=/usr/local/python3 --with-openssl=/usr/local/openssl --enable-optimizations
```

5.执行 make 命令：
```bash
sudo make
```

6.执行 make install 命令：
```bash
sudo make install
```

7.将 python3.10 和 pip3.10 可执行程序软链接到 /usr/local/python/bin 目录下，并重命名为 python 和 pip：
```bash
sudo ln -s /usr/local/python3/bin/python3.10 /usr/local/python3/bin/python
sudo ln -s /usr/local/python3/bin/pip3.10 /usr/local/python3/bin/pip
```

8..编辑 /etc/ld.so.conf 文件，在最后一行添加 /usr/local/python3/lib
```bash
sudo echo '/usr/local/python3/lib' >> /etc/ld.so.conf
```

9.执行 ldconfig 命令：
```bash
sudo ldconfig -v
```

10.在 /etc/profile.d 目录下创建 python.sh 文件，并添加以下内容：
```bash
export PATH=/usr/local/python3/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/python3/lib:$LD_LIBRARY_PATH
```

11.执行 source /etc/profile 命令，使配置生效。
```bash
source /etc/profile
```

12.执行 python --version 命令，查看 python 版本：
```bash
leazhi@leazhi-System-Version:~$ python -V
Python 3.10.6
```

**到此，python 版本降级完成（3.11.6  ---> 3.10.6）**