---
title: django-small 系列014-商品图片分布式存储
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [python]
  - [django]
date: 2023-09-15 08:14:29
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## 使用 docker 启动 FastFDS 分布式文件存储部署

### docker  安装

docker 安装部署请参考：[Docker 系列001-Docker 安装（Win 和 Linux)](https://hexo.linuser.com/2023/09/03/c573f3d4b1f0/?highlight=docker)

### 部署 FastFDS

1.拉取镜像

```shell
sudo docker pull delron/fastdfs
```

2.启动 FastFDS 调度器：
```bash
sudo docker run --restart=always -idt --network=host --name fastdfs-tracker -v /data/docker/mounts/fastdfs/tracker:/var/fdfs delron/fastdfs tracker
```

3.启动 FastFDS 存储节点：
```bash
sudo docker run --restart=always -itd --network=host --name fastdfs-storage -e TRACKER_SERVER=192.168.3.200:22122 -v /data/docker/mounts/fastdfs/storage:/var/fdfs delron/fastdfs storage
```
**注意**：这里的IP 和端口一定不能写错，且必须配置，否则在测试上传文件时会遇到 `Error: 2, No such file or directory` 的报错

4.启动完成后，查看容器的状态是否都为 Up:
```bash
leazhi@ubuntu2204-001:~$ docker ps -a
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS                    PORTS     NAMES
eb0369695515   delron/fastdfs   "/usr/bin/start1.sh …"   30 seconds ago   Up 30 seconds                       fastdfs-storage
e6b074861b94   delron/fastdfs   "/usr/bin/start1.sh …"   41 seconds ago   Up 41 seconds                       fastdfs-tracker
```

## django 安装 FastFDS 客户端

**首先，从 github 上克隆 fdfs_client-py 项目到本地**：
```shell
git clone https://github.com/JaceHo/fdfs_client-py
```

### windows 环境(不兼容，下面的方法不适用)

1.编辑 fdfs_client-py 目录下的 setup.py 文件，将 下面 2 行注释掉：
```bash
#    'ext_modules': [Extension('fdfs_client.sendfile',
#                              sources=['fdfs_client/sendfilemodule.c'])],
```

2.编辑 fdfs_client-py\fdfs_client 目录下的 `storage_client.py` 文件，将 `from fdfs_client.sendfile import *` 注释掉：

3.然后回到 fdfs_client-py 目录 ，执行安装命令：
```bash
python setup.py install 
```

4.将安装过后的 fdfs_client 目录下复制到 python 安装目录（D:\Program Files\Python311\Lib\site-packages） 下的 Lib\site-packages\ 目录里面即可！

### Linux 环境

进入克隆下来的 fdfs_client-py 目录，执行安装命令：
```bash
python setup.py install
```

**其次，安装必要的依赖模块**：
```bash
pip install mutagen==1.45.0 requests==2.29.0
```

## 配置 FastFDS 客户端 连接 FastFDS 服务端

1.在 haoke 子目录下的 utils 包目录下创建 fastdfs 目录，并且在该目录中创建 fdfs_client.conf 文件，并配置如下：**
```bash
# connect timeout in seconds
# default value is 30s
connect_timeout=30
# network timeout in seconds
# default value is 30s
network_timeout=60
# the base path to store log files
base_path=/var/fdfs/storage/logs/
# tracker_server can ocur more than once, and tracker_server format is
# "host:port", host can be hostname or ip address
tracker_server=192.168.3.200:22122                         # 这里需要指定 tracker 服务IP：port
#standard log level as syslog, case insensitive, value list:
### emerg for emergency
### alert
### crit for critical
### error
### warn for warning
### notice
### info
### debug
log_level=info
# if use connection pool
# default value is false
# since V4.05
use_connection_pool = false
# connections whose the idle time exceeds this time will be closed
# unit: second
# default value is 3600
# since V4.05
connection_pool_max_idle_time = 3600
# if load FastDFS parameters from tracker server
# since V4.05
# default value is false
load_fdfs_parameters_from_tracker=false
# if use storage ID instead of IP address
# same as tracker.conf
# valid only when load_fdfs_parameters_from_tracker is false
# default value is false
# since V4.05
use_storage_id = false
# specify storage ids filename, can use relative or absolute path
# same as tracker.conf
# valid only when load_fdfs_parameters_from_tracker is false
# since V4.05
storage_ids_filename = storage_ids.conf
#HTTP settings
http.tracker_server_port=80
#use "#include" directive to include HTTP other settiongs
##include http.conf
```

### django shell 测试上传

1.进入 django shell 模式：
```bash
PS E:\GitLab\python3-django-small_haoke\haoke> python .\manage.py shell
Python 3.11.5 (tags/v3.11.5:cce6ba9, Aug 24 2023, 14:38:34) [MSC v.1936 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
```

2.导入模块：
```python
>>> from fdfs_client.client import Fdfs_client
----> 1 from fdfs_client.client import Fdfs_client

File /usr/local/lib/python3.11/dist-packages/fdfs_client_py-1.2.6-py3.11-linux-x86_64.egg/fdfs_client/client.py:11
      1 #!/usr/bin/env python
      2 # -*- coding: utf-8 -*-
      3 # filename: client.py
      5 """
      6   Client module for Fastdfs 3.08
      7   author: scott yuan scottzer8@gmail.com
      8   date: 2012-06-21
      9 """
---> 11 from fdfs_client.tracker_client import *
     12 from fdfs_client.storage_client import *
     13 from fdfs_client.exceptions import *

File /usr/local/lib/python3.11/dist-packages/fdfs_client_py-1.2.6-py3.11-linux-x86_64.egg/fdfs_client/tracker_client.py:17
      9 from fdfs_client.connection import *
     10 from fdfs_client.exceptions import (
     11     FDFSError,
     12     ConnectionError,
   (...)
     15     DataError
     16 )
---> 17 from fdfs_client.utils import *
     20 def parse_storage_status(status_code):
     21     try:

File /usr/local/lib/python3.11/dist-packages/fdfs_client_py-1.2.6-py3.11-linux-x86_64.egg/fdfs_client/utils.py:8
      6 import os
      7 import stat
----> 8 from mutagen._compat import StringIO
     10 from requests.compat import basestring
     12 SUFFIX = ['B', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB']

ModuleNotFoundError: No module named 'mutagen._compat'
```

**解决方法**：

根据报错信息，修改文件 `sudo vim /usr/local/lib/python3.11/dist-packages/fdfs_client_py-1.2.6-py3.11-linux-x86_64.egg/fdfs_client/utils.py
` 的第 8 行，将 `from mutagen._compat import StringIO` 修改为 `from mutagen._senf._compat import StringIO` ，保存！然后重新导入该模块即可！

之所以出现这个问题，是因为：_compat文件是在_senf目录下,而在FastDFS客户端里用的直接是mutagen._compat,怪不得提示找不到模块，我们重新修改下路径

**注意**：如果您使用的 python 环境高于3.10，且使用的 django 版本高于 3.2，那么可能您也会遇到该问题 [ImportError: cannot import name 'Mapping' from 'collections]([./python%203.11%20兼容性错误-cannot%20import%20name%20'Mapping'%20from%20'collections'.md](https://hexo.linuser.com/2023/09/15/db8b2712984a/))

3.设置配置文件路径：
```python
>>> client = Fdfs_client('/data/gitlab/python3-django-small_haoke/haoke/haoke/utils/fastdfs/client.conf')
```

4.测试上传文件：
```python
In [10]: ret=client.upload_by_filename('/home/leazhi/Pictures/1044085.png')
---------------------------------------------------------------------------
DataError                                 Traceback (most recent call last)
Cell In[10], line 1
----> 1 ret=client.upload_by_filename('/home/leazhi/Pictures/1044085.png')

File ~/.local/lib/python3.11/site-packages/fdfs_client/client.py:90, in Fdfs_client.upload_by_filename(self, filename, meta_dict)
     88     raise DataError(errmsg + '(uploading)')
     89 tc = Tracker_client(self.tracker_pool)
---> 90 store_serv = tc.tracker_query_storage_stor_without_group()
     91 return self.get_storage(store_serv).storage_upload_by_filename(tc, store_serv, filename, meta_dict)

File ~/.local/lib/python3.11/site-packages/fdfs_client/tracker_client.py:381, in Tracker_client.tracker_query_storage_stor_without_group(self)
    379 th.recv_header(conn)
    380 if th.status != 0:
--> 381     raise DataError('[-] Error: %d, %s' % (th.status, os.strerror(th.status)))
    382 recv_buffer, recv_size = tcp_recv_response(conn, th.pkg_len)
    383 if recv_size != TRACKER_QUERY_STORAGE_STORE_BODY_LEN:

DataError: [-] Error: 2, No such file or directory
```

**解决方法**：

- 1.确认配置文件中 tracker 服务器的IP 和端口是否正确；
- 2.确认启动的 storage 容器IP和端口是否和配置文件 client.conf 中的一致（我就是在启动容器的时候忘记了指定端口）； 

解决后，再次尝试上传;
```python
In [3]: ret=client.upload_by_filename('/home/leazhi/Pictures/1044085.png')
getting connection
<fdfs_client.connection.Connection object at 0x7f92d81030d0>
<fdfs_client.fdfs_protol.Tracker_header object at 0x7f92d8103f10>
```

上传成功，然后查看 storage 下面也有了数据：
```bash
──(leazhi㉿kali-desktop)-[~/Downloads/fdfs_client-py]
└─$ ls /data/docker/mounts/fastdfs/storage/data 
00  07  0E  15  1C  23  2A  31  38  3F  46  4D  54  5B  62  69  70  77  7E  85  8C  93  9A  A1  A8  AF  B6  BD  C4  CB  D2  D9  E0  E7  EE  F5  FC
01  08  0F  16  1D  24  2B  32  39  40  47  4E  55  5C  63  6A  71  78  7F  86  8D  94  9B  A2  A9  B0  B7  BE  C5  CC  D3  DA  E1  E8  EF  F6  FD
02  09  10  17  1E  25  2C  33  3A  41  48  4F  56  5D  64  6B  72  79  80  87  8E  95  9C  A3  AA  B1  B8  BF  C6  CD  D4  DB  E2  E9  F0  F7  fdfs_storaged.pid
03  0A  11  18  1F  26  2D  34  3B  42  49  50  57  5E  65  6C  73  7A  81  88  8F  96  9D  A4  AB  B2  B9  C0  C7  CE  D5  DC  E3  EA  F1  F8  FE
04  0B  12  19  20  27  2E  35  3C  43  4A  51  58  5F  66  6D  74  7B  82  89  90  97  9E  A5  AC  B3  BA  C1  C8  CF  D6  DD  E4  EB  F2  F9  FF
05  0C  13  1A  21  28  2F  36  3D  44  4B  52  59  60  67  6E  75  7C  83  8A  91  98  9F  A6  AD  B4  BB  C2  C9  D0  D7  DE  E5  EC  F3  FA  storage_stat.dat
06  0D  14  1B  22  29  30  37  3E  45  4C  53  5A  61  68  6F  76  7D  84  8B  92  99  A0  A7  AE  B5  BC  C3  CA  D1  D8  DF  E6  ED  F4  FB  sync
```

**注：**如果您在 windows 下测试，有可能会遇到这个情况(**暂时没找到解决方法！！！**)：
```python
ret=client.upload_by_filename(r'C:\Users\leazh\Pictures\1319109.jpg') 
>>> ret=client.upload_by_filename(r'C:\Users\leazh\Pictures\1319109.jpg')                                                  
Traceback (most recent call last):
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\connection.py", line 131, in get_connection
    conn = self._conns_available.pop()
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^
IndexError: pop from empty list

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\client.py", line 105, in upload_by_filename
    store_serv = tc.tracker_query_storage_stor_without_group()
                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\tracker_client.py", line 374, in tracker_query_storage_stor_witho
ut_group
    conn = self.pool.get_connection()
           ^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\connection.py", line 136, in get_connection
    conn = self.make_conn()
           ^^^^^^^^^^^^^^^^
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\connection.py", line 115, in make_conn
    conn_instance = self.conn_class(**self.conn_kwargs)
                    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\connection.py", line 26, in __init__
    self.remote_port = conn_kwargs['port']
                       ~~~~~~~~~~~^^^^^^^^
KeyError: 'port'
```

## 改写 django 自带的存储模块

### 配置 
1.编辑配置文件 dev.py ,在末尾添加：
```bash
# haoke/settings/dev.py

...

# django fastdfs 文件存储配置
DEFAULT_FILE_STORFAGE = 'haoke.utils.fastdfs.fdfs_storage.FastDFSStorage'

DFFS_URL = '192.168.3.200:8888'
FDFS_CLIENT_CONF=os.path.join(BASE_DIR, 'utils/fastdfs/cient.conf')
```

2.在 haoke/utils/fastdfs 目录下新建 fdfs_storage.py 文件，改写 django 自带的存储模块：
```bash
from fdfs_client.client import Fdfs_client
from django.core.files.storage import Storage
from django.utils.deconstruct import deconstructible
from django.conf import settings

'''

改写 django 自带的存储模块--指定存储的目录和路径到 fastdfs
deconstructible  -- 为自定义类做装饰器的
'''

@deconstructible
class FastDFSStorage(Storage):
    '''
    自定义存储类
    '''
    def __init__(self, base_url=None, client_conf=None):
        '''
        base_url 完整的 url
        client_conf 配置的路径
        '''
        if base_url is None:
            base_url = settings.FDFS_URL
        self.base_url = base_url

        if client_conf is None:
            client_conf = settings.FDFS_CLIENT_CONF
        self.client_conf = client_conf

    def _open(self, name, mode='rb'):
        pass

    def _save(self, name, content):
        '''
        获取到要保存的内容
        '''
        client = Fdfs_client(self.client_conf)
        ret = client.upload_by_filename(content.read())
        if ret.get('status') != "Upload successed.":
            raise Exception('Upload file failed')
        file_name = ret.get('Remote file_id')
        return file_name

    def url(self, name):

        return self.base_url + name

    def exists(self, name):
        return False
```