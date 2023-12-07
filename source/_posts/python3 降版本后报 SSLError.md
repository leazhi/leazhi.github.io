---
title: python3 降版本报 SSLError
author: leazhi
tags:
  - [python]
categories:
  - [python]
date: 2023-10-10 19:15:00
cover: 
discription: 
keywords: 
password: 
message: 
---

##  系统环境:

- OS：Kali Linux 2023.3
- Kernel：6.5.0-kali1-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.5.3-1kali1 (2023-09-19) x86_64 GNU/Linux

## 源码编译安装降级版本

**关于 python 升降级请参考：[python3 升、降级的正确方式](https://hexo.linuser.com/2023/10/14/8c8168212c10/)**


因为在给客户部署 Stable Diffusion 项目，出现了一些报错。查看下安装文档说明 python 版本最好是用 3.10.6 ，否则更新的版本不支持 `torch` 库。而我 Kali Linux  系统上 python 的版本为 3.11.6。所以索性就决定给 python 降个版本。结果，降级安装没问题，却在使用低版本的 python 时报错了！



### 开始使用的 configure 参数

第一次不带  `--with-ssl` 参数的 configure 配置：
```bash
configure --prefix=/usr/local/python3.10.3 --enable-optimizations
```

安装成功后，使用低版本的 python 时，结果报如下错误：
```bash
┌──(leazhi㉿kali-desktop)-[~/stable-diffusion-webui]
└─$ python launch.py --lowvram --xformers --listen

Python 3.10.13 (main, Oct 10 2023, 18:44:01) [GCC 13.2.0]
Version: v1.6.0
Commit hash: 5ef669de080814067961f28357256e8fe27544f4
Installing torch and torchvision
Defaulting to user installation because normal site-packages is not writeable
WARNING: pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple, https://download.pytorch.org/whl/cu118
WARNING: Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /simple/torch/
WARNING: Retrying (Retry(total=3, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /simple/torch/
WARNING: Retrying (Retry(total=2, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /simple/torch/
WARNING: Retrying (Retry(total=1, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /simple/torch/
WARNING: Retrying (Retry(total=0, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /simple/torch/
Could not fetch URL https://pypi.tuna.tsinghua.edu.cn/simple/torch/: There was a problem confirming the ssl certificate: HTTPSConnectionPool(host='pypi.tuna.tsinghua.edu.cn', port=443): Max retries exceeded with url: /simple/torch/ (Caused by SSLError("Can't connect to HTTPS URL because the SSL module is not available.")) - skipping
WARNING: Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /whl/cu118/torch/
WARNING: Retrying (Retry(total=3, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /whl/cu118/torch/
WARNING: Retrying (Retry(total=2, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /whl/cu118/torch/
WARNING: Retrying (Retry(total=1, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /whl/cu118/torch/
WARNING: Retrying (Retry(total=0, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /whl/cu118/torch/
Could not fetch URL https://download.pytorch.org/whl/cu118/torch/: There was a problem confirming the ssl certificate: HTTPSConnectionPool(host='download.pytorch.org', port=443): Max retries exceeded with url: /whl/cu118/torch/ (Caused by SSLError("Can't connect to HTTPS URL because the SSL module is not available.")) - skipping
ERROR: Could not find a version that satisfies the requirement torch==2.0.1 (from versions: none)
ERROR: No matching distribution found for torch==2.0.1
WARNING: pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.
Could not fetch URL https://pypi.tuna.tsinghua.edu.cn/simple/pip/: There was a problem confirming the ssl certificate: HTTPSConnectionPool(host='pypi.tuna.tsinghua.edu.cn', port=443): Max retries exceeded with url: /simple/pip/ (Caused by SSLError("Can't connect to HTTPS URL because the SSL module is not available.")) - skipping
Could not fetch URL https://download.pytorch.org/whl/cu118/pip/: There was a problem confirming the ssl certificate: HTTPSConnectionPool(host='download.pytorch.org', port=443): Max retries exceeded with url: /whl/cu118/pip/ (Caused by SSLError("Can't connect to HTTPS URL because the SSL module is not available.")) - skipping
Traceback (most recent call last):
  File "/home/leazhi/stable-diffusion-webui/launch.py", line 48, in <module>
    main()
  File "/home/leazhi/stable-diffusion-webui/launch.py", line 39, in main
    prepare_environment()
  File "/home/leazhi/stable-diffusion-webui/modules/launch_utils.py", line 352, in prepare_environment
    run(f'"{python}" -m {torch_command}', "Installing torch and torchvision", "Couldn't install torch", live=True)
  File "/home/leazhi/stable-diffusion-webui/modules/launch_utils.py", line 115, in run
    raise RuntimeError("\n".join(error_bits))
RuntimeError: Couldn't install torch.
Command: "/usr/local/python3.10.3/bin/python" -m pip install torch==2.0.1 torchvision==0.15.2 --extra-index-url https://download.pytorch.org/whl/cu118
Error code: 1
```

**解决方法**：


进入 python 低版本的源码目录，加上参数 `--with-ssl` 重新 configure ，之前编译安装就OK 了
```bash
./configure --prefix=/usr/local/python3.10.3 --enable-optimizations --with-ssl
```

然后再次编译安装即可！