---
title: python3 中 pip 的使用
author: leazhi
tags:
  - [python]
  - [pip]
categories:
  - [python]
  - [pip]
date: 2023-09-10 17:06:46
cover: 
discription: 
keywords: 
password: 
message: 
---

## 源的设置

1.查看 pip 默认的安装源:
```bash
pip config list
```

2.**永久生效**：设置 pip 安装源为国内源（阿里云）：
```bash
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple
```

相当于在用户的家目录下创建一个 pip 文件夹，里面有一个 pip.ini 文件，内容如下：
```bash
# ~/.pip/pip.conf

[global]
index-url=http://pypi.douban.com/simple
[install]
trusted-host=pypi.douban.com
```



**国内几个安装源**
- 阿里云：pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/

- 豆瓣：pip config set global.index-url http://pypi.douban.com/simple/

- 清华：pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

- 腾讯：pip config set global.index-url http://mirrors.cloud.tencent.com/pypi/simple

3.切换回默认源：
```bash
pip config unset global.index-url
```
## 包的安装

### 普通安装（不指定版本）

普通安装就是直接安装最新版本，不指定版本号。
```bash
pip install itsdangerous
```

###  指定版本安装

1.查看要安装包的所有版本：
```bash
PS E:\Python> pip index versions itsdangerous
WARNING: pip index is currently an experimental command. It may be removed/changed in a future release without prior warning.
itsdangerous (2.1.2)
Available versions: 2.1.2, 2.1.1, 2.1.0, 2.0.1, 2.0.0, 1.1.0, 0.24, 0.23, 0.22, 0.21, 0.20, 0.19, 0.18, 0.17, 0.16, 0.15, 0.14, 0.13, 0.12, 0.11, 0.10, 0.9.1, 0.9
  INSTALLED: 2.1.2
  LATEST:    2.1.2
```

2.安装指定版本，比如 1.1.0:
```bash
PS E:\Python> pip install itsdangerous==1.1.0
Defaulting to user installation because normal site-packages is not writeable
Collecting itsdangerous==1.1.0
  Downloading itsdangerous-1.1.0-py2.py3-none-any.whl (16 kB)
Installing collected packages: itsdangerous
  Attempting uninstall: itsdangerous
    Found existing installation: itsdangerous 2.1.2
    Uninstalling itsdangerous-2.1.2:
      Successfully uninstalled itsdangerous-2.1.2
Successfully installed itsdangerous-1.1.0
```

### 指定文件安装

1.将所有要安装的报都存放在一个 .txt 文件中。然后安装的时候使用参数 -r 指定该 .txt 文件，比如：
```bash
pip install -r packages.txt
```

### 指定源安装

**临时生效**：临时性指定源：
```bash
pip install django -i https://pypi.douban.com/simple
```

## 常见 pip 报错及解决方法

### 错误一：error: externally-managed-environment

在 ubuntu 23.10 中使用 pip 安装包时报：
```bash
leazhi@ubuntu2210:/data/gitlab/hexo$ pip install django
error: externally-managed-environment

× This environment is externally managed
╰─> To install Python packages system-wide, try apt install
    python3-xyz, where xyz is the package you are trying to
    install.
    
    If you wish to install a non-Debian-packaged Python package,
    create a virtual environment using python3 -m venv path/to/venv.
    Then use path/to/venv/bin/python and path/to/venv/bin/pip. Make
    sure you have python3-full installed.
    
    If you wish to install a non-Debian packaged Python application,
    it may be easiest to use pipx install xyz, which will manage a
    virtual environment for you. Make sure you have pipx installed.
    
    See /usr/share/doc/python3.11/README.venv for more information.

note: If you believe this is a mistake, please contact your Python installation or OS distribution provider. You can override this, at the risk of breaking your Python installation or OS, by passing --break-system-packages.
hint: See PEP 668 for the detailed specification.
```

**<font color="red">解决方法</font>**:

#### 方法一：常规解决方法

1.先查看下本地使用的 pip 对应的 python 版本：
```bash
leazhi@ubuntu2310:/usr/lib/python3$ pip --version
pip 23.2 from /usr/lib/python3/dist-packages/pip (python 3.11)
```

可以看到，pip 对应的 python 版本为 3.11

2.进入 `/usr/lib/python3.11/` 目录，重命名或者删除 `EXTERNALLY-MANAGED` 文件：
```bash
leazhi@ubuntu2310:/usr/lib/python3$ cd /usr/lib/python3.11/
leazhi@ubuntu2310:/usr/lib/python3.11$ sudo mv EXTERNALLY-MANAGED EXTERNALLY-MANAGED.bak
```

3.重新执行 pip install 即可！
```bash
leazhi@ubuntu2210:/data/gitlab/hexo$ pip install django
Defaulting to user installation because normal site-packages is not writeable
Collecting django
  Obtaining dependency information for django from https://files.pythonhosted.org/packages/b9/45/707dfc56f381222c1c798503546cb390934ab246fc45b5051ef66e31099c/Django-4.2.6-py3-none-any.whl.metadata
  Downloading Django-4.2.6-py3-none-any.whl.metadata (4.1 kB)
Collecting asgiref<4,>=3.6.0 (from django)
  Obtaining dependency information for asgiref<4,>=3.6.0 from https://files.pythonhosted.org/packages/9b/80/b9051a4a07ad231558fcd8ffc89232711b4e618c15cb7a392a17384bbeef/asgiref-3.7.2-py3-none-any.whl.metadata
  Downloading asgiref-3.7.2-py3-none-any.whl.metadata (9.2 kB)
Collecting sqlparse>=0.3.1 (from django)
  Downloading sqlparse-0.4.4-py3-none-any.whl (41 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 41.2/41.2 kB 259.1 kB/s eta 0:00:00
Downloading Django-4.2.6-py3-none-any.whl (8.0 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 8.0/8.0 MB 1.0 MB/s eta 0:00:00
Downloading asgiref-3.7.2-py3-none-any.whl (24 kB)
Installing collected packages: sqlparse, asgiref, django
  WARNING: The script sqlformat is installed in '/home/leazhi/.local/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
  WARNING: The script django-admin is installed in '/home/leazhi/.local/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
Successfully installed asgiref-3.7.2 django-4.2.6 sqlparse-0.4.4
```

#### 方法二：使用虚拟环境

1.安装 python3-venv 包：
```bash
sudo apt install python3.11-venv -y
```

2.在指定目录下创建 python 虚拟环境，执行：`python -m venv venv` 
```bash
leazhi@ubuntu2310:/data$ python3 -m venv venv
```

3.激活虚拟环境
```bash
leazhi@ubuntu2310:/data$ source venv/bin/activate
(venv) leazhi@ubuntu2310:/data$ 
```

4.再次执行安装命令
```bash
(venv) leazhi@ubuntu2310:/data$ pip install django==4.1.0
Collecting django==4.1.0
  Downloading Django-4.1-py3-none-any.whl (8.1 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 8.1/8.1 MB 1.1 MB/s eta 0:00:00
Collecting asgiref<4,>=3.5.2 (from django==4.1.0)
  Obtaining dependency information for asgiref<4,>=3.5.2 from https://files.pythonhosted.org/packages/9b/80/b9051a4a07ad231558fcd8ffc89232711b4e618c15cb7a392a17384bbeef/asgiref-3.7.2-py3-none-any.whl.metadata
  Using cached asgiref-3.7.2-py3-none-any.whl.metadata (9.2 kB)
Collecting sqlparse>=0.2.2 (from django==4.1.0)
  Using cached sqlparse-0.4.4-py3-none-any.whl (41 kB)
Using cached asgiref-3.7.2-py3-none-any.whl (24 kB)
Installing collected packages: sqlparse, asgiref, django
Successfully installed asgiref-3.7.2 django-4.1 sqlparse-0.4.4
```