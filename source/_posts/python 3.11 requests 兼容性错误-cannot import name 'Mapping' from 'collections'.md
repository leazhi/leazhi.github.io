---
title: python 3.11 requests 兼容性错误-cannot import name 'Mapping' from 'collections'
author: leazhi
tags:
  - [python]
categories:
  - [python]
date: 2023-09-15 11:14:56
cover: 
discription: 
keywords: 
password: 
message: 
---

## 环境说明：
- OS：windows 10 pro
- python：3.11.0
- fdfs_client：4.0.6
- requests ：2.29.0
- mutagen: 1.45.0
- django: 3.2.20

## 问题描述：

使用 django + rest_framework 框架，使用 fdfs_client 进行文件上传，在 django shell 模式下导入 fastdfs_client 模块时报：

```bash
>>> from fdfs_client.client import Fdfs_client
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\client.py", line 11, in <module>
    from fdfs_client.tracker_client import *
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\tracker_client.py", line 17, in <module>
    from fdfs_client.utils import *
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\utils.py", line 10, in <module>
    from requests.compat import basestring
  File "D:\Program Files\Python311\Lib\site-packages\requests\__init__.py", line 43, in <module>
    import urllib3
  File "D:\Program Files\Python311\Lib\site-packages\urllib3\__init__.py", line 8, in <module>
    from .connectionpool import (
  File "D:\Program Files\Python311\Lib\site-packages\urllib3\connectionpool.py", line 29, in <module>
    from .connection import (
  File "D:\Program Files\Python311\Lib\site-packages\urllib3\connection.py", line 39, in <module>
    from .util.ssl_ import (
  File "D:\Program Files\Python311\Lib\site-packages\urllib3\util\__init__.py", line 3, in <module>
    from .connection import is_connection_dropped
  File "D:\Program Files\Python311\Lib\site-packages\urllib3\util\connection.py", line 3, in <module>
    from .wait import wait_for_read
  File "D:\Program Files\Python311\Lib\site-packages\urllib3\util\wait.py", line 1, in <module>
    from .selectors import (
  File "D:\Program Files\Python311\Lib\site-packages\urllib3\util\selectors.py", line 14, in <module>
    from collections import namedtuple, Mapping                 # 原始导入内容
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
ImportError: cannot import name 'Mapping' from 'collections' (D:\Program Files\Python311\Lib\collections\__init__.py)
```

**解决方法**：

根据报错信息，编辑 `D:\Program Files\Python311\Lib\site-packages\urllib3\util\selectors.py` 文件，将第 14 行内容 `from collections import namedtuple, Mapping` 注释掉，然后在该行下面添加如下两行：

```bash
from collections import namedtuple
from typing import Mapping
```

保存修改！然后重新执行命令 `from fdfs_client.client import Fdfs_client`，导入 Fdfs_client 模块，再次报错：
```bash
>>> from fdfs_client.client import Fdfs_client
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\client.py", line 11, in <module>
    from fdfs_client.tracker_client import *
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\tracker_client.py", line 17, in <module>
    from fdfs_client.utils import *
  File "D:\Program Files\Python311\Lib\site-packages\fdfs_client\utils.py", line 10, in <module>
    from requests.compat import basestring
  File "D:\Program Files\Python311\Lib\site-packages\requests\__init__.py", line 43, in <module>
    import urllib3
  File "D:\Program Files\Python311\Lib\site-packages\urllib3\__init__.py", line 8, in <module>
    from .connectionpool import (
  File "D:\Program Files\Python311\Lib\site-packages\urllib3\connectionpool.py", line 29, in <module>
    from .connection import (
  File "D:\Program Files\Python311\Lib\site-packages\urllib3\connection.py", line 50, in <module>
    from ._collections import HTTPHeaderDict
  File "D:\Program Files\Python311\Lib\site-packages\urllib3\_collections.py", line 2, in <module>
    from collections import Mapping, MutableMapping         # 原始导入内容
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
ImportError: cannot import name 'Mapping' from 'collections' (D:\Program Files\Python311\Lib\collections\__init__.py)
```

**解决方法**：

根据报错信息，编辑 `D:\Program Files\Python311\Lib\site-packages\urllib3\_collections.py` 文件，注释掉 `from collections import Mapping, MutableMapping ` 这一行，然后在该行下面添加 `from typing import Mapping, MutableMapping` 即可！

## 总结

该问题是由于Python3.10版本以后对requests库进行调整，collections中不能直接调用方法Mapping，MutableMapping