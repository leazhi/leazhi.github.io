---
title: urllib.error.URLError:<urlopen error [SSL:CERTIFICATE_VERIFY_FAILED] certificate verify failed:unable to get local issuer certificate (_ssl.c:1002)>
author: leazhi
tags:
  - [python]
  - [urllib]
categories:
  - [python]
  - [spider]
date: 2023-07-19 13:51:53
cover: https://hexo.linuser.com/gallery/code/559128.jpg
discription: 
keywords: 
password: 
message: 
---

在爬虫文件中使用 urllib.request.urlretrieve() 方法直接下载图片，结果报：

```python
Traceback (most recent call last):
  File "D:\Program Files\Python3\Lib\urllib\request.py", line 1348, in do_open
    h.request(req.get_method(), req.selector, req.data, headers,
  File "D:\Program Files\Python3\Lib\http\client.py", line 1286, in request
    self._send_request(method, url, body, headers, encode_chunked)
  File "D:\Program Files\Python3\Lib\http\client.py", line 1332, in _send_request
    self.endheaders(body, encode_chunked=encode_chunked)
  File "D:\Program Files\Python3\Lib\http\client.py", line 1281, in endheaders
    self._send_output(message_body, encode_chunked=encode_chunked)
  File "D:\Program Files\Python3\Lib\http\client.py", line 1041, in _send_output
    self.send(msg)
  File "D:\Program Files\Python3\Lib\http\client.py", line 979, in send
    self.connect()
  File "D:\Program Files\Python3\Lib\http\client.py", line 1458, in connect
    self.sock = self._context.wrap_socket(self.sock,
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\Program Files\Python3\Lib\ssl.py", line 517, in wrap_socket
    return self.sslsocket_class._create(
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\Program Files\Python3\Lib\ssl.py", line 1075, in _create
    self.do_handshake()
  File "D:\Program Files\Python3\Lib\ssl.py", line 1346, in do_handshake
    self._sslobj.do_handshake()
ssl.SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1002)

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "D:\Gitlab\spider\alphacoders\spider-技术.py", line 76, in <module>
    W.main()
  File "D:\Gitlab\spider\alphacoders\spider-技术.py", line 68, in main
    self.get_data(response, data_dir)
  File "D:\Gitlab\spider\alphacoders\spider-技术.py", line 60, in get_data
    urllib.request.urlretrieve(link, f'{data_dir}/{img_name}.jpeg')
  File "D:\Program Files\Python3\Lib\urllib\request.py", line 241, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
                            ^^^^^^^^^^^^^^^^^^
  File "D:\Program Files\Python3\Lib\urllib\request.py", line 216, in urlopen
    return opener.open(url, data, timeout)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\Program Files\Python3\Lib\urllib\request.py", line 519, in open
    response = self._open(req, data)
               ^^^^^^^^^^^^^^^^^^^^^
  File "D:\Program Files\Python3\Lib\urllib\request.py", line 536, in _open
    result = self._call_chain(self.handle_open, protocol, protocol +
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\Program Files\Python3\Lib\urllib\request.py", line 496, in _call_chain
    result = func(*args)
             ^^^^^^^^^^^
  File "D:\Program Files\Python3\Lib\urllib\request.py", line 1391, in https_open
    return self.do_open(http.client.HTTPSConnection, req,
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\Program Files\Python3\Lib\urllib\request.py", line 1351, in do_open
    raise URLError(err)
urllib.error.URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1002)>

Process finished with exit code 1

```

解决方法：

在爬虫文件中导入 ssl 库，并设置如下语句为全局变量即可：
```python
import ssl
...

ssl._create_default_https_context = ssl._create_unverified_context
...
```