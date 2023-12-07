---
title: 兼职单 004-CondaHTTPError-HTTP 401 UNAUTHORIZED for url
author: leazhi
tags:
  - [conda]
categories:
 - [conda]
date: 2023-09-06 14:14:46
cover: 
discription: 
keywords: 
password: 
message: 
---
## 系统环境：
- OS 版本：22.04.3 LTS
- Kernel 版本：5.15.0-1037-intel-iotg #42-Ubuntu SMP Wed Jul 26 14:01:25 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
- Conda 版本：23.7.2

## 问题描述

客户使用 conda 安装某个包时报：
```bash
(base) lei@innatrix-Z790:~$ conda install conda
Collecting package metadata (current_repodata.json): - DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): repo.anaconda.com:443
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): repo.anaconda.com:443
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): repo.anaconda.com:443
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): repo.anaconda.com:443
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): mirrors.tuna.tsinghua.edu.cn:443
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): conda.graylab.jhu.edu:443
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): mirrors.tuna.tsinghua.edu.cn:443
\ DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): conda.graylab.jhu.edu:443
DEBUG:urllib3.connectionpool:https://repo.anaconda.com:443 "GET /pkgs/r/linux-64/current_repodata.json HTTP/1.1" 304 0
DEBUG:urllib3.connectionpool:https://repo.anaconda.com:443 "GET /pkgs/main/noarch/current_repodata.json HTTP/1.1" 304 0
| DEBUG:urllib3.connectionpool:https://repo.anaconda.com:443 "GET /pkgs/r/noarch/current_repodata.json HTTP/1.1" 304 0
DEBUG:urllib3.connectionpool:https://repo.anaconda.com:443 "GET /pkgs/main/linux-64/current_repodata.json HTTP/1.1" 304 0
DEBUG:urllib3.connectionpool:https://conda.graylab.jhu.edu:443 "GET /noarch/current_repodata.json HTTP/1.1" 401 179
/ DEBUG:urllib3.connectionpool:https://conda.graylab.jhu.edu:443 "GET /linux-64/current_repodata.json HTTP/1.1" 401 179
/ DEBUG:urllib3.connectionpool:https://mirrors.tuna.tsinghua.edu.cn:443 "GET /anaconda/pkgs/free/noarch/current_repodata.json HTTP/1.1" 404 None
DEBUG:urllib3.connectionpool:https://mirrors.tuna.tsinghua.edu.cn:443 "GET /anaconda/pkgs/free/linux-64/current_repodata.json HTTP/1.1" 404 None
\ DEBUG:urllib3.connectionpool:https://mirrors.tuna.tsinghua.edu.cn:443 "GET /anaconda/pkgs/free/noarch/repodata.json HTTP/1.1" 304 0
DEBUG:urllib3.connectionpool:https://mirrors.tuna.tsinghua.edu.cn:443 "GET /anaconda/pkgs/free/linux-64/repodata.json HTTP/1.1" 304 0
failed

CondaHTTPError: HTTP 401 UNAUTHORIZED for url <https://conda.graylab.jhu.edu/linux-64/current_repodata.json>
Elapsed: 00:00.485556

The credentials you have provided for this URL are invalid.

You will need to modify your conda configuration to proceed.
Use `conda config --show` to view your configuration's current state.
Further configuration help can be found at <https://conda.io/docs/config.html>.
```

## 问题分析

从客户提供的报错信息来看，大致判断是由于 conda 配置了要认证的源导致的。而在和客户交流的过程中发现，他自己也不知道是怎么回事就出现了这个带有验证的源地址的。

索性，就使用报错的地址在浏览器中访问下，结果如下：
![](../static/img/20230906/20230906001.jpg)

接着，在命令行终端使用命令查看 conda 配置的源 ，发现：
```bash
(base) lei@innatrix-Z790:~$ conda config --show channels
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - https://USERNAME:PASSWORD@conda.graylab.jhu.edu                 # 这个源有验证，报错的就是这个源引起的
  - defaults
```


## 解决方法：

1.清理源：
```bash
(base) lei@innatrix-Z790:~$ conda config --remove-key channels
```

2.重新添加国内清华大学源：
```bash
(base) lei@innatrix-Z790:~$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
```

3.再次查看 conda 的源信息：
```bash
(base) lei@innatrix-Z790:~$ conda config --show channels
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - defaults
```

4.尝试更新：
```bash
(base) lei@innatrix-Z790:~$ conda update conda
Collecting package metadata (current_repodata.json): \ DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): mirrors.tuna.tsinghua.edu.cn:443
| DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): mirrors.tuna.tsinghua.edu.cn:443
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): repo.anaconda.com:443
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): repo.anaconda.com:443
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): repo.anaconda.com:443
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): repo.anaconda.com:443
- DEBUG:urllib3.connectionpool:https://repo.anaconda.com:443 "GET /pkgs/main/linux-64/current_repodata.json HTTP/1.1" 304 0
\ DEBUG:urllib3.connectionpool:https://repo.anaconda.com:443 "GET /pkgs/main/noarch/current_repodata.json HTTP/1.1" 304 0
| DEBUG:urllib3.connectionpool:https://repo.anaconda.com:443 "GET /pkgs/r/noarch/current_repodata.json HTTP/1.1" 304 0
\ DEBUG:urllib3.connectionpool:https://repo.anaconda.com:443 "GET /pkgs/r/linux-64/current_repodata.json HTTP/1.1" 304 0
- DEBUG:urllib3.connectionpool:https://mirrors.tuna.tsinghua.edu.cn:443 "GET /anaconda/pkgs/free/noarch/current_repodata.json HTTP/1.1" 404 None
DEBUG:urllib3.connectionpool:https://mirrors.tuna.tsinghua.edu.cn:443 "GET /anaconda/pkgs/free/linux-64/current_repodata.json HTTP/1.1" 404 None
| DEBUG:urllib3.connectionpool:https://mirrors.tuna.tsinghua.edu.cn:443 "GET /anaconda/pkgs/free/noarch/repodata.json HTTP/1.1" 304 0
DEBUG:urllib3.connectionpool:https://mirrors.tuna.tsinghua.edu.cn:443 "GET /anaconda/pkgs/free/linux-64/repodata.json HTTP/1.1" 304 0
done
Solving environment: done

## Package Plan ##

  environment location: /home/lei/anaconda3

  added / updated specs:
    - conda


The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    arrow-cpp-11.0.0           |       hda39474_2        10.2 MB  defaults
    blas-1.0                   |              mkl           6 KB  https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
    c-ares-1.19.1              |       h5eee18b_0         118 KB  defaults
    conda-23.7.3               |  py311h06a4308_0         1.3 MB  defaults
    cryptography-39.0.1        |  py311h9ce1e76_0         1.4 MB  defaults
    curl-7.26.0                |                1         451 KB  https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
    cyrus-sasl-2.1.28          |       h9c0eb46_1         237 KB  defaults
    datashader-0.15.2          |  py311h06a4308_0        17.0 MB  defaults
    expat-2.5.0                |       h6a678d5_0         172 KB  defaults
    fontconfig-2.14.1          |       h4c34cd2_2         281 KB  defaults
    grpc-cpp-1.48.2            |       h5bf31a4_0         4.8 MB  defaults
    h5py-3.9.0                 |  py311hdd6beaf_0         1.3 MB  defaults
    hdf5-1.12.1                |       h70be1eb_2         4.1 MB  defaults
```

到此，问题解决！