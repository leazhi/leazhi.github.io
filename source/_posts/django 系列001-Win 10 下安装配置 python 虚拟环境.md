---
title: django 系列001:Win 10 下安装配置 python 虚拟环境.md
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-07-20 10:46:13
cover: 
discription: 
keywords: 
password: 
message: 
---


{% note warning simple %}
前提条件：物理机必须先安装有 python 环境，即打开命令行终端工具，执行 python -V 能正常输出 python 版本信息方可
```cmd
C:\Users\leazh>python -V
Python 3.11.4
```
{% endnote %}


# python 虚拟环境安装

python 虚拟环境是用于隔离 python 项目开发过程中的一种技术实现，其表现为 python 版本，库版本的不同,而导致项目兼容问题。

## windows 环境安装：

打开 cmd 命令窗口，执行命令：
```cmd
pip3 install virtualenv -i https://pypi.douban.com/simple
pip3 installvirtualenvwrapper-win -i https://pypi.douban.com/simple
```

# 环境配置：

**注意：配置 python 虚拟环境的目录路径不能带有中文，否则：**
```bash
C:\Users\leazh>mkvirtualenv env03
created virtual environment CPython3.11.4.final.0-64 in 239ms
  creator CPython3Windows(dest=D:\Study\好课优选\Django\练习\环境\env03, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=C:\Users\leazh\AppData\Local\pypa\virtualenv)
    added seed packages: pip==23.1.2, setuptools==67.8.0, wheel==0.40.0
  activators BashActivator,BatchActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator

(env03) C:\Users\leazh>deactivate
'deactivate' 不是内部或外部命令，也不是可运行的程序
或批处理文件。

(env03) C:\Users\leazh>deactivate
'deactivate' 不是内部或外部命令，也不是可运行的程序
或批处理文件。
```

## windows 环境：



### **<font color="red">方式一</font>** 不自定义配置 python 虚拟环境 `WORKON_HOME` 的情况；

1.不自定义配置 python 虚拟环境 `WORKON_HOME` 的情况：

在当前用户下以当前用户身份打开 cmd 命令行工具创建 python 虚拟环境的目录会默认创建在 `C:\Users\leazh\Envs` 下, 且可以通过 `deactivate` 命令退出当前虚拟环境：
```cmd
C:\Users\leazh>mkvirtualenv env01
 C:\Users\leazh\Envs is not a directory, creating
created virtual environment CPython3.11.4.final.0-64 in 315ms
  creator CPython3Windows(dest=C:\Users\leazh\Envs\env01, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=C:\Users\leazh\AppData\Local\pypa\virtualenv)
    added seed packages: pip==23.1.2, setuptools==67.8.0, wheel==0.40.0
  activators BashActivator,BatchActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator

(env01) C:\Users\leazh>deactivate

C:\Users\leazh>if defined _OLD_VIRTUAL_PYTHONPATH (set "PYTHONPATH=" )
```

在当前用户下以管理员身份打开 cmd 命令行工具创建 python 虚拟环境的目录也会默认创建在 `C:\Users\leazh\Envs` 下,且可以通过 `deactivate` 命令退出当前虚拟环境：
```cmd
C:\Windows\system32>mkvirtualenv env02
created virtual environment CPython3.11.4.final.0-64 in 306ms
  creator CPython3Windows(dest=C:\Users\leazh\Envs\env02, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=C:\Users\leazh\AppData\Local\pypa\virtualenv)
    added seed packages: pip==23.1.2, setuptools==67.8.0, wheel==0.40.0
  activators BashActivator,BatchActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator

(env02) C:\Windows\System32>deactivate

C:\Windows\System32>if defined _OLD_VIRTUAL_PYTHONPATH (set "PYTHONPATH=" )
```


### **<font color="red">方式二</font>** 配置在用户环境变量下

2.假设将 python 虚拟环境变量（WORKON_HOME）路径配置在当前用户（leazhi）环境变量里面：
```cmd
WORKON_HOME = `D:\Study\www.xiaoe-tech.com\Django\practise\environment`
```

在当前用户下以当前用户身份打开 cmd 命令行工具创建 python 虚拟环境的目录会默认创建在 `D:\Study\www.xiaoe-tech.com\Django\practise\environment` 下,且可以通过 `deactivate` 命令退出当前虚拟环境(环境目录路径如果配有中文，将会导致的无法使用 deactivate 命令退出当前 python 虚拟环境)：
```cmd
C:\Users\leazh>mkvirtualenv env03
created virtual environment CPython3.11.4.final.0-64 in 2574ms
  creator CPython3Windows(dest=D:\Study\www.xiaoe-tech.com\Django\practise\environment\env03, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=C:\Users\leazh\AppData\Local\pypa\virtualenv)
    added seed packages: pip==23.1.2, setuptools==67.8.0, wheel==0.40.0
  activators BashActivator,BatchActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator

(env03) C:\Users\leazh>deactivate

C:\Users\leazh>if defined _OLD_VIRTUAL_PYTHONPATH (set "PYTHONPATH=" )
```


在当前用户下以管理员身份打开 cmd 命令行工具创建 python 虚拟环境的目录也会默认创建在 `D:\Study\www.xiaoe-tech.com\Django\practise\environment` 下，且可以通过 `deactivate` 命令退出当前虚拟环境(环境目录路径如果配有中文，将会导致的无法使用 deactivate 命令退出当前 python 虚拟环境)：
```cmd
C:\Windows\system32>mkvirtualenv env04
created virtual environment CPython3.11.4.final.0-64 in 256ms
  creator CPython3Windows(dest=D:\Study\www.xiaoe-tech.com\Django\practise\environment\env04, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=C:\Users\leazh\AppData\Local\pypa\virtualenv)
    added seed packages: pip==23.1.2, setuptools==67.8.0, wheel==0.40.0
  activators BashActivator,BatchActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator

(env04) C:\Windows\System32>deactivate

C:\Windows\System32>if defined _OLD_VIRTUAL_PYTHONPATH (set "PYTHONPATH=" )
```

### **<font color="red">方式三</font>** 配置在系统环境变量下

3.假设配置在系统环境里面的 python 虚拟环境：
```cmd
WORKON_HOME = `D:\Study\www.xiaoe-tech.com\Django\practise\environment`
```

在当用户下以当前用户身份打开 cmd 命令行工具创建 python 虚拟环境的目录会默认创建在 `D:\Study\www.xiaoe-tech.com\Django\practise\environment` 下，且可以通过 `deactivate` 命令退出当前虚拟环境(环境目录路径如果配有中文，将会导致的无法使用 deactivate 命令退出当前 python 虚拟环境)：
```cmd
(05) C:\Users\leazh>mkvirtualenv env05
created virtual environment CPython3.11.4.final.0-64 in 241ms
  creator CPython3Windows(dest=D:\Study\www.xiaoe-tech.com\Django\practise\environment\env05, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=C:\Users\leazh\AppData\Local\pypa\virtualenv)
    added seed packages: pip==23.1.2, setuptools==67.8.0, wheel==0.40.0
  activators BashActivator,BatchActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator

(env05) C:\Users\leazh>deactivate

C:\Users\leazh>if defined _OLD_VIRTUAL_PYTHONPATH (set "PYTHONPATH=" )
```

在当前用户下以管理员身份打开 cmd 命令行工具创建 python 虚拟环境的目录会默认创建在 `D:\Study\www.xiaoe-tech.com\Django\practise\environment\env06` 下，且可以通过 `deactivate` 命令退出当前虚拟环境(环境目录路径如果配有中文，将会导致的无法使用 deactivate 命令退出当前 python 虚拟环境)：
```cmd
C:\Windows\system32>mkvirtualenv env06
created virtual environment CPython3.11.4.final.0-64 in 244ms
  creator CPython3Windows(dest=D:\Study\www.xiaoe-tech.com\Django\practise\environment\env06, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=C:\Users\leazh\AppData\Local\pypa\virtualenv)
    added seed packages: pip==23.1.2, setuptools==67.8.0, wheel==0.40.0
  activators BashActivator,BatchActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator

(env06) C:\Windows\System32>deactivate

C:\Windows\System32>if defined _OLD_VIRTUAL_PYTHONPATH (set "PYTHONPATH=" )
```

# 虚拟环境管理命令

### 创建虚拟环境

打开 cmd 命令终端工具，执行：
```cmd
mkvirtualenv VIRTUALENV_NAME
```

### 列出所有虚拟环境：

执行命令：
```cmd
workon
```

### 进入指定虚拟环境

执行命令：
```cmd
workon VIRTUALENV_NAME
```

### 退出虚拟环境

执行命令：
```cmd
deactivate
```
