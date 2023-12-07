---
title: Stable Diffusion 报错及解决方法
author: leazhi
tags:
  - [stable diffusion]
  - [Linux]
categories:
  - [linux]
  - [kali]
date: 2023-10-11 14:10:15
cover: 
discription: 
keywords: 
password: 
message: 
---

## Stable Diffusion 部署报错

### 错误一：Error: Command '['/data/stable-diffusion-webui/venv/bin/python', '-Im', 'ensurepip', '--upgrade', '--default-pip']' returned non-zero exit status 1.

使用 python 创建 venv 时报错：
```bash
xhh@xhh-Z790-AORUS-ELITE-AX:/data/stable-diffusion-webui$ python -m venv venv
Error: Command '['/data/stable-diffusion-webui/venv/bin/python', '-Im', 'ensurepip', '--upgrade', '--default-pip']' returned non-zero exit status 1.
```

**解决方法**：

删除 stable-diffusion-webui 目录下的 venv 目录，然后重新生成：
```bash
xhh@xhh-Z790-AORUS-ELITE-AX:/data/stable-diffusion-webui$ rm -rf venv
xhh@xhh-Z790-AORUS-ELITE-AX:/data/stable-diffusion-webui$ python -m venv venv
```

### 错误二：ERROR: No matching distribution found for tb-nightly

找不到 tb-nightly 模块：
```bash
ERROR: Could not find a version that satisfies the requirement tb-nightly (from versions: none)
ERROR: No matching distribution found for tb-nightly
```

**解决方法**：
先查看下系统使用的默认源所哪个？有可能是该源中没有该包
```bash
pip config list
```

然后切换 pip 源：
```bash
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple
```

### 错误三：ModuleNotFoundError: No module named '_bz2'

没有 _bz2 模块
```bash
Traceback (most recent call last):
  File "/home/leazhi/Downloads/stable-diffusion-webui/launch.py", line 48, in <module>
    main()
  File "/home/leazhi/Downloads/stable-diffusion-webui/launch.py", line 44, in main
    start()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/launch_utils.py", line 432, in start
    import webui
  File "/home/leazhi/Downloads/stable-diffusion-webui/webui.py", line 13, in <module>
    initialize.imports()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/initialize.py", line 16, in imports
    import pytorch_lightning  # noqa: F401
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/pytorch_lightning/__init__.py", line 35, in <module>
    from pytorch_lightning.callbacks import Callback  # noqa: E402
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/pytorch_lightning/callbacks/__init__.py", line 14, in <module>
    from pytorch_lightning.callbacks.batch_size_finder import BatchSizeFinder
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/pytorch_lightning/callbacks/batch_size_finder.py", line 24, in <module>
    from pytorch_lightning.callbacks.callback import Callback
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/pytorch_lightning/callbacks/callback.py", line 25, in <module>
    from pytorch_lightning.utilities.types import STEP_OUTPUT
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/pytorch_lightning/utilities/types.py", line 27, in <module>
    from torchmetrics import Metric
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchmetrics/__init__.py", line 14, in <module>
    from torchmetrics import functional  # noqa: E402
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchmetrics/functional/__init__.py", line 50, in <module>
    from torchmetrics.functional.image._deprecated import (
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchmetrics/functional/image/__init__.py", line 17, in <module>
    from torchmetrics.functional.image.lpips import learned_perceptual_image_patch_similarity
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchmetrics/functional/image/lpips.py", line 45, in <module>
    from torchvision import models as tv
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchvision/__init__.py", line 6, in <module>
    from torchvision import datasets, io, models, ops, transforms, utils
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchvision/datasets/__init__.py", line 1, in <module>
    from ._optical_flow import FlyingChairs, FlyingThings3D, HD1K, KittiFlow, Sintel
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchvision/datasets/_optical_flow.py", line 13, in <module>
    from .utils import _read_pfm, verify_str_arg
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchvision/datasets/utils.py", line 1, in <module>
    import bz2
  File "/usr/local/python3.10.6/lib/python3.10/bz2.py", line 17, in <module>
    from _bz2 import BZ2Compressor, BZ2Decompressor
ModuleNotFoundError: No module named '_bz2'
```

**解决方法**：

1.安装 ibbz2-dev 
```bash
┌──(leazhi㉿kali-desktop)-[~/Downloads/stable-diffusion-webui]
└─$ sudo apt install -y libbz2-dev 
```

2.使用 find 命令查找
```bash
┌──(leazhi㉿kali-desktop)-[~/Downloads/stable-diffusion-webui]
└─$ sudo find /* -type f -iname '_bz2.cp*'
/data/docker/overlay2/59c1f3ac9350fbf6801c6e6d5bc5f8385812affe3fba340f2bef9265ee050f0d/diff/usr/lib/python3.10/lib-dynload/_bz2.cpython-310-x86_64-linux-gnu.so
/data/docker/overlay2/06f14c3d5039b1817166bf990e2d1716ddff91fa375bcf6b16fb8e7dcd37443c/diff/opt/gitlab/embedded/lib/python3.9/lib-dynload/_bz2.cpython-39-x86_64-linux-gnu.so
/data/docker/overlay2/8f79d506567332e5814d68c27aadba99fa26bffbf78c0665b5b0701f890a3eb8/diff/usr/lib/python3.9/lib-dynload/_bz2.cpython-39.so
find: ‘/run/user/1000/doc’: 权限不够
find: ‘/run/user/1000/gvfs’: 权限不够
/usr/local/python3.10.6/lib/python3.10/lib-dynload/_bz2.cpython-310-x86_64-linux-gnu.so
/usr/lib/python3.11/lib-dynload/_bz2.cpython-311-x86_64-linux-gnu.so
/var/lib/docker/overlay2/0f3272773808176e32169b1f4b50b3d5a182fc42c615452261369843a3fc9ce3/diff/usr/local/lib/python3.11/lib-dynload/_bz2.cpython-311-x86_64-linux-musl.so
/var/lib/docker/overlay2/331b39e62a6cbf5e06301ef3f148a844ca300ba1bad43d4d6d9569601f91b73a/diff/usr/local/lib/python3.11/lib-dynload/_bz2.cpython-311-x86_64-linux-musl.so
/var/lib/docker/overlay2/3013125df28f9cf32167fa734d0f1b670be74eb142b5ced4ae3731db219839c2/merged/usr/local/lib/python3.11/lib-dynload/_bz2.cpython-311-x86_64-linux-musl.so
```

3.由于我现在使用的 python 版本为 3.10 ,所以，我们直接复制 3.10 版本的模块文件到 python 安装目录下：
```bash
┌──(leazhi㉿kali-desktop)-[~/Downloads/stable-diffusion-webui]
└─$ sudo cp /data/docker/overlay2/59c1f3ac9350fbf6801c6e6d5bc5f8385812affe3fba340f2bef9265ee050f0d/diff/usr/lib/python3.10/lib-dynload/_bz2.cpython-310-x86_64-linux-gnu.so /usr/local/python3.10.6/lib/python3.10/lib-dynload
```

### 错误四：ModuleNotFoundError: No module named '_lzma'

没有 _lzma 模块

```bash
Traceback (most recent call last):
  File "/home/leazhi/Downloads/stable-diffusion-webui/launch.py", line 48, in <module>
    main()
  File "/home/leazhi/Downloads/stable-diffusion-webui/launch.py", line 44, in main
    start()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/launch_utils.py", line 432, in start
    import webui
  File "/home/leazhi/Downloads/stable-diffusion-webui/webui.py", line 13, in <module>
    initialize.imports()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/initialize.py", line 16, in imports
    import pytorch_lightning  # noqa: F401
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/pytorch_lightning/__init__.py", line 35, in <module>
    from pytorch_lightning.callbacks import Callback  # noqa: E402
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/pytorch_lightning/callbacks/__init__.py", line 14, in <module>
    from pytorch_lightning.callbacks.batch_size_finder import BatchSizeFinder
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/pytorch_lightning/callbacks/batch_size_finder.py", line 24, in <module>
    from pytorch_lightning.callbacks.callback import Callback
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/pytorch_lightning/callbacks/callback.py", line 25, in <module>
    from pytorch_lightning.utilities.types import STEP_OUTPUT
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/pytorch_lightning/utilities/types.py", line 27, in <module>
    from torchmetrics import Metric
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchmetrics/__init__.py", line 14, in <module>
    from torchmetrics import functional  # noqa: E402
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchmetrics/functional/__init__.py", line 50, in <module>
    from torchmetrics.functional.image._deprecated import (
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchmetrics/functional/image/__init__.py", line 17, in <module>
    from torchmetrics.functional.image.lpips import learned_perceptual_image_patch_similarity
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchmetrics/functional/image/lpips.py", line 45, in <module>
    from torchvision import models as tv
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchvision/__init__.py", line 6, in <module>
    from torchvision import datasets, io, models, ops, transforms, utils
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchvision/datasets/__init__.py", line 1, in <module>
    from ._optical_flow import FlyingChairs, FlyingThings3D, HD1K, KittiFlow, Sintel
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchvision/datasets/_optical_flow.py", line 13, in <module>
    from .utils import _read_pfm, verify_str_arg
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torchvision/datasets/utils.py", line 6, in <module>
    import lzma
  File "/usr/local/python3.10.6/lib/python3.10/lzma.py", line 27, in <module>
    from _lzma import *
ModuleNotFoundError: No module named '_lzma'
```

**解决方法**：

1.同错误三一样，使用 find 方法先查找：
```bash
┌──(leazhi㉿kali-desktop)-[~/Downloads/stable-diffusion-webui]
└─$ sudo find /* -type f -iname '_lzma.cpy*'
/data/docker/overlay2/59c1f3ac9350fbf6801c6e6d5bc5f8385812affe3fba340f2bef9265ee050f0d/diff/usr/lib/python3.10/lib-dynload/_lzma.cpython-310-x86_64-linux-gnu.so
/data/docker/overlay2/06f14c3d5039b1817166bf990e2d1716ddff91fa375bcf6b16fb8e7dcd37443c/diff/opt/gitlab/embedded/lib/python3.9/lib-dynload/_lzma.cpython-39-x86_64-linux-gnu.so
/data/docker/overlay2/8f79d506567332e5814d68c27aadba99fa26bffbf78c0665b5b0701f890a3eb8/diff/usr/lib/python3.9/lib-dynload/_lzma.cpython-39.so
find: ‘/run/user/1000/doc’: 权限不够
find: ‘/run/user/1000/gvfs’: 权限不够
/usr/lib/python3.11/lib-dynload/_lzma.cpython-311-x86_64-linux-gnu.so
/var/lib/docker/overlay2/0f3272773808176e32169b1f4b50b3d5a182fc42c615452261369843a3fc9ce3/diff/usr/local/lib/python3.11/lib-dynload/_lzma.cpython-311-x86_64-linux-musl.so
/var/lib/docker/overlay2/331b39e62a6cbf5e06301ef3f148a844ca300ba1bad43d4d6d9569601f91b73a/diff/usr/local/lib/python3.11/lib-dynload/_lzma.cpython-311-x86_64-linux-musl.so
/var/lib/docker/overlay2/3013125df28f9cf32167fa734d0f1b670be74eb142b5ced4ae3731db219839c2/merged/usr/local/lib/python3.11/lib-dynload/_lzma.cpython-311-x86_64-linux-musl.so
```

2.然后再复制
```bash
┌──(leazhi㉿kali-desktop)-[~/Downloads/stable-diffusion-webui]
└─$ sudo cp /data/docker/overlay2/59c1f3ac9350fbf6801c6e6d5bc5f8385812affe3fba340f2bef9265ee050f0d/diff/usr/lib/python3.10/lib-dynload/_lzma.cpython-310-x86_64-linux-gnu.so /usr/local/python3.10.6/lib/python3.10/lib-dynload 
```

### 错误五：ModuleNotFoundError: No module named '_ctypes'

找不到 ctypes 模块：
```bash
Launching Web UI with arguments: --skip-torch-cuda-test
Traceback (most recent call last):
  File "/data/stable-diffusion-webui/launch.py", line 48, in <module>
    main()
  File "/data/stable-diffusion-webui/launch.py", line 44, in main
    start()
  File "/data/stable-diffusion-webui/modules/launch_utils.py", line 432, in start
    import webui
  File "/data/stable-diffusion-webui/webui.py", line 13, in <module>
    initialize.imports()
  File "/data/stable-diffusion-webui/modules/initialize.py", line 14, in imports
    import torch  # noqa: F401
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/__init__.py", line 17, in <module>
    import ctypes
  File "/usr/local/python3.10.6/lib/python3.10/ctypes/__init__.py", line 8, in <module>
    from _ctypes import Union, Structure, Array
ModuleNotFoundError: No module named '_ctypes'
```

**解决方法**：

同错误三一样，使用 find 方法先查找：
```bash
xhh@xhh-Z790-AORUS-ELITE-AX:/data/stable-diffusion-webui$ sudo find /* -type f -iname '_ctypes*'
```

然后将库文件复制到 python 安装目录：
```bash
xhh@xhh-Z790-AORUS-ELITE-AX:/data/stable-diffusion-webui$ sudo cp /usr/lib/python3.10/lib-dynload/_ctypes.cpython-310-x86_64-linux-gnu.so /usr/local/python3.10.6/lib/python3.10/lib-dynload/
```

### 错误六：safetensors_rust.SafetensorError: Error while deserializing header: MetadataIncompleteBuffer

没有 v1-5-pruned-emaonly.safetensors 模型文件，或者该模型下载失败（模型文件大小有问题，模型文件不完整）：

```bash
Downloading: "https://huggingface.co/runwayml/stable-diffusion-v1-5/resolve/main/v1-5-pruned-emaonly.safetensors" to /home/leazhi/Downloads/stable-diffusion-webui/models/Stable-diffusion/v1-5-pruned-emaonly.safetensors

  0%|                                                                              | 455k/3.97G [00:03<8:12:00, 144kB/s]
Calculating sha256 for /home/leazhi/Downloads/stable-diffusion-webui/models/Stable-diffusion/v1-5-pruned-emaonly.safetensors: 9aa89c6abf8bc4e5bab01837824976aef4a1d7eb916bb75bf31e1c9a50eaff05
Loading weights [9aa89c6abf] from /home/leazhi/Downloads/stable-diffusion-webui/models/Stable-diffusion/v1-5-pruned-emaonly.safetensors
loading stable diffusion model: SafetensorError
Traceback (most recent call last):
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 973, in _bootstrap
    self._bootstrap_inner()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 953, in run
    self._target(*self._args, **self._kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/initialize.py", line 147, in load_model
    shared.sd_model  # noqa: B018
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/shared_items.py", line 110, in sd_model
    return modules.sd_models.model_data.get_sd_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 499, in get_sd_model
    load_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 584, in load_model
    state_dict = get_checkpoint_state_dict(checkpoint_info, timer)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 315, in get_checkpoint_state_dict
    res = read_state_dict(checkpoint_info.filename)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 291, in read_state_dict
    pl_sd = safetensors.torch.load_file(checkpoint_file, device=device)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/safetensors/torch.py", line 259, in load_file
    with safe_open(filename, framework="pt", device=device) as f:
safetensors_rust.SafetensorError: Error while deserializing header: MetadataIncompleteBuffer


Stable diffusion model failed to load
Applying attention optimization: Doggettx... done.
Running on local URL:  http://127.0.0.1:7860

To create a public link, set `share=True` in `launch()`.
Startup time: 11.7s (prepare environment: 1.1s, import torch: 1.8s, import gradio: 0.5s, setup paths: 0.5s, initialize shared: 0.1s, other imports: 0.4s, list SD models: 6.1s, load scripts: 0.5s, create ui: 0.4s).
正在现有浏览器会话中打开。
Loading weights [9aa89c6abf] from /home/leazhi/Downloads/stable-diffusion-webui/models/Stable-diffusion/v1-5-pruned-emaonly.safetensors
loading stable diffusion model: SafetensorError
Traceback (most recent call last):
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 973, in _bootstrap
    self._bootstrap_inner()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 392, in pages_html
    return refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 398, in refresh
    pg.refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks_textual_inversion.py", line 13, in refresh
    sd_hijack.model_hijack.embedding_db.load_textual_inversion_embeddings(force_reload=True)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 255, in load_textual_inversion_embeddings
    self.expected_shape = self.get_expected_shape()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 154, in get_expected_shape
    vec = shared.sd_model.cond_stage_model.encode_embedding_init_text(",", 1)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/shared_items.py", line 110, in sd_model
    return modules.sd_models.model_data.get_sd_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 499, in get_sd_model
    load_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 584, in load_model
    state_dict = get_checkpoint_state_dict(checkpoint_info, timer)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 315, in get_checkpoint_state_dict
    res = read_state_dict(checkpoint_info.filename)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 291, in read_state_dict
    pl_sd = safetensors.torch.load_file(checkpoint_file, device=device)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/safetensors/torch.py", line 259, in load_file
    with safe_open(filename, framework="pt", device=device) as f:
safetensors_rust.SafetensorError: Error while deserializing header: MetadataIncompleteBuffer


Stable diffusion model failed to load
Loading weights [9aa89c6abf] from /home/leazhi/Downloads/stable-diffusion-webui/models/Stable-diffusion/v1-5-pruned-emaonly.safetensors
loading stable diffusion model: SafetensorError
Traceback (most recent call last):
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 973, in _bootstrap
    self._bootstrap_inner()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 392, in pages_html
    return refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 398, in refresh
    pg.refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks_textual_inversion.py", line 13, in refresh
    sd_hijack.model_hijack.embedding_db.load_textual_inversion_embeddings(force_reload=True)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 255, in load_textual_inversion_embeddings
    self.expected_shape = self.get_expected_shape()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 154, in get_expected_shape
    vec = shared.sd_model.cond_stage_model.encode_embedding_init_text(",", 1)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/shared_items.py", line 110, in sd_model
    return modules.sd_models.model_data.get_sd_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 499, in get_sd_model
    load_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 584, in load_model
    state_dict = get_checkpoint_state_dict(checkpoint_info, timer)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 315, in get_checkpoint_state_dict
    res = read_state_dict(checkpoint_info.filename)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 291, in read_state_dict
    pl_sd = safetensors.torch.load_file(checkpoint_file, device=device)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/safetensors/torch.py", line 259, in load_file
    with safe_open(filename, framework="pt", device=device) as f:
safetensors_rust.SafetensorError: Error while deserializing header: MetadataIncompleteBuffer


Stable diffusion model failed to load
Loading weights [9aa89c6abf] from /home/leazhi/Downloads/stable-diffusion-webui/models/Stable-diffusion/v1-5-pruned-emaonly.safetensors
loading stable diffusion model: SafetensorError
Traceback (most recent call last):
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 973, in _bootstrap
    self._bootstrap_inner()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui.py", line 1298, in <lambda>
    update_image_cfg_scale_visibility = lambda: gr.update(visible=shared.sd_model and shared.sd_model.cond_stage_key == "edit")
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/shared_items.py", line 110, in sd_model
    return modules.sd_models.model_data.get_sd_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 499, in get_sd_model
    load_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 584, in load_model
    state_dict = get_checkpoint_state_dict(checkpoint_info, timer)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 315, in get_checkpoint_state_dict
    res = read_state_dict(checkpoint_info.filename)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 291, in read_state_dict
    pl_sd = safetensors.torch.load_file(checkpoint_file, device=device)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/safetensors/torch.py", line 259, in load_file
    with safe_open(filename, framework="pt", device=device) as f:
safetensors_rust.SafetensorError: Error while deserializing header: MetadataIncompleteBuffer


Stable diffusion model failed to load
Traceback (most recent call last):
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/routes.py", line 488, in run_predict
    output = await app.get_blocks().process_api(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1431, in process_api
    result = await self.call_function(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1103, in call_function
    prediction = await anyio.to_thread.run_sync(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/to_thread.py", line 33, in run_sync
    return await get_asynclib().run_sync_in_worker_thread(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 877, in run_sync_in_worker_thread
    return await future
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 392, in pages_html
    return refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 398, in refresh
    pg.refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks_textual_inversion.py", line 13, in refresh
    sd_hijack.model_hijack.embedding_db.load_textual_inversion_embeddings(force_reload=True)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 255, in load_textual_inversion_embeddings
    self.expected_shape = self.get_expected_shape()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 154, in get_expected_shape
    vec = shared.sd_model.cond_stage_model.encode_embedding_init_text(",", 1)
AttributeError: 'NoneType' object has no attribute 'cond_stage_model'
Traceback (most recent call last):
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/routes.py", line 488, in run_predict
    output = await app.get_blocks().process_api(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1431, in process_api
    result = await self.call_function(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1103, in call_function
    prediction = await anyio.to_thread.run_sync(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/to_thread.py", line 33, in run_sync
    return await get_asynclib().run_sync_in_worker_thread(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 877, in run_sync_in_worker_thread
    return await future
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 392, in pages_html
    return refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 398, in refresh
    pg.refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks_textual_inversion.py", line 13, in refresh
    sd_hijack.model_hijack.embedding_db.load_textual_inversion_embeddings(force_reload=True)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 255, in load_textual_inversion_embeddings
    self.expected_shape = self.get_expected_shape()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 154, in get_expected_shape
    vec = shared.sd_model.cond_stage_model.encode_embedding_init_text(",", 1)
AttributeError: 'NoneType' object has no attribute 'cond_stage_model'
```

**解决方法**

通过香港的服务器下载训练模型，然后将其复制到本地的 `/home/leazhi/Downloads/stable-diffusion-webui/models/Stable-diffusion/` 目录下：

```bash
scp UserName@ServerIP:/home/Username/v1-5-pruned-emaonly.safetensors /home/leazhi/Downloads/stable-diffusion-webui/models/Stable-diffusion/
```

或者：

确保模型文件大小完整：
```bash
┌──(leazhi㉿kali-desktop)-[~/Downloads/stable-diffusion-webui/models/Stable-diffusion]
└─$ du -sh ./*
0	./Put Stable Diffusion checkpoints here.txt
25M	./v1-5-pruned-emaonly.safetensors
4.0G	./v1-5-pruned-emaonly.safetensors.1
      
┌──(leazhi㉿kali-desktop)-[~/Downloads/stable-diffusion-webui/models/Stable-diffusion]
└─$ rm -rf v1-5-pruned-emaonly.safetensors

┌──(leazhi㉿kali-desktop)-[~/Downloads/stable-diffusion-webui/models/Stable-diffusion]
└─$ mv ./v1-5-pruned-emaonly.safetensors.1 ./v1-5-pruned-emaonly.safetensors   
```

### 错误七：loading stable diffusion model: OutOfMemoryError

内存不够引起的报错：
```bash
loading stable diffusion model: OutOfMemoryError
Traceback (most recent call last):
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 973, in _bootstrap
    self._bootstrap_inner()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 953, in run
    self._target(*self._args, **self._kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/initialize.py", line 147, in load_model
    shared.sd_model  # noqa: B018
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/shared_items.py", line 110, in sd_model
    return modules.sd_models.model_data.get_sd_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 499, in get_sd_model
    load_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 626, in load_model
    load_model_weights(sd_model, checkpoint_info, state_dict, timer)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 353, in load_model_weights
    model.load_state_dict(state_dict, strict=False)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 223, in <lambda>
    module_load_state_dict = self.replace(torch.nn.Module, 'load_state_dict', lambda *args, **kwargs: load_state_dict(module_load_state_dict, *args, **kwargs))
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 221, in load_state_dict
    original(module, state_dict, strict=strict)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2027, in load_state_dict
    load(self, state_dict)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  [Previous line repeated 4 more times]
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2009, in load
    module._load_from_state_dict(
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 226, in <lambda>
    conv2d_load_from_state_dict = self.replace(torch.nn.Conv2d, '_load_from_state_dict', lambda *args, **kwargs: load_from_state_dict(conv2d_load_from_state_dict, *args, **kwargs))
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 191, in load_from_state_dict
    module._parameters[name] = torch.nn.parameter.Parameter(torch.zeros_like(param, device=device, dtype=dtype), requires_grad=param.requires_grad)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_meta_registrations.py", line 1780, in zeros_like
    return aten.empty_like.default(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_ops.py", line 287, in __call__
    return self._op(*args, **kwargs or {})
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_refs/__init__.py", line 4254, in empty_like
    return torch.empty_strided(
torch.cuda.OutOfMemoryError: CUDA out of memory. Tried to allocate 20.00 MiB (GPU 0; 3.94 GiB total capacity; 3.44 GiB already allocated; 52.00 MiB free; 3.50 GiB reserved in total by PyTorch) If reserved memory is >> allocated memory try setting max_split_size_mb to avoid fragmentation.  See documentation for Memory Management and PYTORCH_CUDA_ALLOC_CONF


Stable diffusion model failed to load
Applying attention optimization: Doggettx... Loading weights [b1aee72366] from /home/leazhi/Downloads/stable-diffusion-webui/models/Stable-diffusion/v1-5-pruned-emaonly.safetensors
done.
Exception in thread Thread-2 (load_model):
Traceback (most recent call last):
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 953, in run
    self._target(*self._args, **self._kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/initialize.py", line 153, in load_model
    devices.first_time_calculation()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/devices.py", line 152, in first_time_calculation
    conv2d(x)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1501, in _call_impl
    return forward_call(*args, **kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/extensions-builtin/Lora/networks.py", line 444, in network_Conv2d_forward
    return originals.Conv2d_forward(self, input)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/conv.py", line 463, in forward
    return self._conv_forward(input, self.weight, self.bias)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/conv.py", line 459, in _conv_forward
    return F.conv2d(input, weight, bias, self.stride,
RuntimeError: cuDNN error: CUDNN_STATUS_NOT_INITIALIZED
Creating model from config: /home/leazhi/Downloads/stable-diffusion-webui/configs/v1-inference.yaml
loading stable diffusion model: OutOfMemoryError
Traceback (most recent call last):
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 973, in _bootstrap
    self._bootstrap_inner()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 392, in pages_html
    return refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 398, in refresh
    pg.refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks_textual_inversion.py", line 13, in refresh
    sd_hijack.model_hijack.embedding_db.load_textual_inversion_embeddings(force_reload=True)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 255, in load_textual_inversion_embeddings
    self.expected_shape = self.get_expected_shape()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 154, in get_expected_shape
    vec = shared.sd_model.cond_stage_model.encode_embedding_init_text(",", 1)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/shared_items.py", line 110, in sd_model
    return modules.sd_models.model_data.get_sd_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 499, in get_sd_model
    load_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 626, in load_model
    load_model_weights(sd_model, checkpoint_info, state_dict, timer)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 353, in load_model_weights
    model.load_state_dict(state_dict, strict=False)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 223, in <lambda>
    module_load_state_dict = self.replace(torch.nn.Module, 'load_state_dict', lambda *args, **kwargs: load_state_dict(module_load_state_dict, *args, **kwargs))
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 221, in load_state_dict
    original(module, state_dict, strict=strict)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2027, in load_state_dict
    load(self, state_dict)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  [Previous line repeated 4 more times]
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2009, in load
    module._load_from_state_dict(
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 226, in <lambda>
    conv2d_load_from_state_dict = self.replace(torch.nn.Conv2d, '_load_from_state_dict', lambda *args, **kwargs: load_from_state_dict(conv2d_load_from_state_dict, *args, **kwargs))
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 191, in load_from_state_dict
    module._parameters[name] = torch.nn.parameter.Parameter(torch.zeros_like(param, device=device, dtype=dtype), requires_grad=param.requires_grad)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_meta_registrations.py", line 1780, in zeros_like
    return aten.empty_like.default(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_ops.py", line 287, in __call__
    return self._op(*args, **kwargs or {})
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_refs/__init__.py", line 4254, in empty_like
    return torch.empty_strided(
torch.cuda.OutOfMemoryError: CUDA out of memory. Tried to allocate 20.00 MiB (GPU 0; 3.94 GiB total capacity; 3.44 GiB already allocated; 38.00 MiB free; 3.50 GiB reserved in total by PyTorch) If reserved memory is >> allocated memory try setting max_split_size_mb to avoid fragmentation.  See documentation for Memory Management and PYTORCH_CUDA_ALLOC_CONF


Stable diffusion model failed to load
Loading weights [b1aee72366] from /home/leazhi/Downloads/stable-diffusion-webui/models/Stable-diffusion/v1-5-pruned-emaonly.safetensors
Traceback (most recent call last):
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/routes.py", line 488, in run_predict
    output = await app.get_blocks().process_api(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1431, in process_api
    result = await self.call_function(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1103, in call_function
    prediction = await anyio.to_thread.run_sync(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/to_thread.py", line 33, in run_sync
    return await get_asynclib().run_sync_in_worker_thread(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 877, in run_sync_in_worker_thread
    return await future
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 392, in pages_html
    return refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 398, in refresh
    pg.refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks_textual_inversion.py", line 13, in refresh
    sd_hijack.model_hijack.embedding_db.load_textual_inversion_embeddings(force_reload=True)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 255, in load_textual_inversion_embeddings
    self.expected_shape = self.get_expected_shape()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 154, in get_expected_shape
    vec = shared.sd_model.cond_stage_model.encode_embedding_init_text(",", 1)
AttributeError: 'NoneType' object has no attribute 'cond_stage_model'
Creating model from config: /home/leazhi/Downloads/stable-diffusion-webui/configs/v1-inference.yaml
loading stable diffusion model: OutOfMemoryError
Traceback (most recent call last):
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 973, in _bootstrap
    self._bootstrap_inner()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 392, in pages_html
    return refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 398, in refresh
    pg.refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks_textual_inversion.py", line 13, in refresh
    sd_hijack.model_hijack.embedding_db.load_textual_inversion_embeddings(force_reload=True)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 255, in load_textual_inversion_embeddings
    self.expected_shape = self.get_expected_shape()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 154, in get_expected_shape
    vec = shared.sd_model.cond_stage_model.encode_embedding_init_text(",", 1)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/shared_items.py", line 110, in sd_model
    return modules.sd_models.model_data.get_sd_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 499, in get_sd_model
    load_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 626, in load_model
    load_model_weights(sd_model, checkpoint_info, state_dict, timer)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 353, in load_model_weights
    model.load_state_dict(state_dict, strict=False)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 223, in <lambda>
    module_load_state_dict = self.replace(torch.nn.Module, 'load_state_dict', lambda *args, **kwargs: load_state_dict(module_load_state_dict, *args, **kwargs))
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 221, in load_state_dict
    original(module, state_dict, strict=strict)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2027, in load_state_dict
    load(self, state_dict)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  [Previous line repeated 4 more times]
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2009, in load
    module._load_from_state_dict(
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 226, in <lambda>
    conv2d_load_from_state_dict = self.replace(torch.nn.Conv2d, '_load_from_state_dict', lambda *args, **kwargs: load_from_state_dict(conv2d_load_from_state_dict, *args, **kwargs))
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 191, in load_from_state_dict
    module._parameters[name] = torch.nn.parameter.Parameter(torch.zeros_like(param, device=device, dtype=dtype), requires_grad=param.requires_grad)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_meta_registrations.py", line 1780, in zeros_like
    return aten.empty_like.default(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_ops.py", line 287, in __call__
    return self._op(*args, **kwargs or {})
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_refs/__init__.py", line 4254, in empty_like
    return torch.empty_strided(
torch.cuda.OutOfMemoryError: CUDA out of memory. Tried to allocate 20.00 MiB (GPU 0; 3.94 GiB total capacity; 3.44 GiB already allocated; 38.00 MiB free; 3.50 GiB reserved in total by PyTorch) If reserved memory is >> allocated memory try setting max_split_size_mb to avoid fragmentation.  See documentation for Memory Management and PYTORCH_CUDA_ALLOC_CONF


Stable diffusion model failed to load
Loading weights [b1aee72366] from /home/leazhi/Downloads/stable-diffusion-webui/models/Stable-diffusion/v1-5-pruned-emaonly.safetensors
Traceback (most recent call last):
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/routes.py", line 488, in run_predict
    output = await app.get_blocks().process_api(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1431, in process_api
    result = await self.call_function(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1103, in call_function
    prediction = await anyio.to_thread.run_sync(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/to_thread.py", line 33, in run_sync
    return await get_asynclib().run_sync_in_worker_thread(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 877, in run_sync_in_worker_thread
    return await future
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 392, in pages_html
    return refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks.py", line 398, in refresh
    pg.refresh()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui_extra_networks_textual_inversion.py", line 13, in refresh
    sd_hijack.model_hijack.embedding_db.load_textual_inversion_embeddings(force_reload=True)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 255, in load_textual_inversion_embeddings
    self.expected_shape = self.get_expected_shape()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 154, in get_expected_shape
    vec = shared.sd_model.cond_stage_model.encode_embedding_init_text(",", 1)
AttributeError: 'NoneType' object has no attribute 'cond_stage_model'
Creating model from config: /home/leazhi/Downloads/stable-diffusion-webui/configs/v1-inference.yaml
loading stable diffusion model: OutOfMemoryError
Traceback (most recent call last):
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 973, in _bootstrap
    self._bootstrap_inner()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/ui.py", line 1298, in <lambda>
    update_image_cfg_scale_visibility = lambda: gr.update(visible=shared.sd_model and shared.sd_model.cond_stage_key == "edit")
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/shared_items.py", line 110, in sd_model
    return modules.sd_models.model_data.get_sd_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 499, in get_sd_model
    load_model()
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 626, in load_model
    load_model_weights(sd_model, checkpoint_info, state_dict, timer)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_models.py", line 353, in load_model_weights
    model.load_state_dict(state_dict, strict=False)
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 223, in <lambda>
    module_load_state_dict = self.replace(torch.nn.Module, 'load_state_dict', lambda *args, **kwargs: load_state_dict(module_load_state_dict, *args, **kwargs))
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 221, in load_state_dict
    original(module, state_dict, strict=strict)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2027, in load_state_dict
    load(self, state_dict)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2015, in load
    load(child, child_state_dict, child_prefix)
  [Previous line repeated 4 more times]
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 2009, in load
    module._load_from_state_dict(
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 226, in <lambda>
    conv2d_load_from_state_dict = self.replace(torch.nn.Conv2d, '_load_from_state_dict', lambda *args, **kwargs: load_from_state_dict(conv2d_load_from_state_dict, *args, **kwargs))
  File "/home/leazhi/Downloads/stable-diffusion-webui/modules/sd_disable_initialization.py", line 191, in load_from_state_dict
    module._parameters[name] = torch.nn.parameter.Parameter(torch.zeros_like(param, device=device, dtype=dtype), requires_grad=param.requires_grad)
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_meta_registrations.py", line 1780, in zeros_like
    return aten.empty_like.default(
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_ops.py", line 287, in __call__
    return self._op(*args, **kwargs or {})
  File "/home/leazhi/Downloads/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/_refs/__init__.py", line 4254, in empty_like
    return torch.empty_strided(
torch.cuda.OutOfMemoryError: CUDA out of memory. Tried to allocate 20.00 MiB (GPU 0; 3.94 GiB total capacity; 3.44 GiB already allocated; 38.00 MiB free; 3.50 GiB reserved in total by PyTorch) If reserved memory is >> allocated memory try setting max_split_size_mb to avoid fragmentation.  See documentation for Memory Management and PYTORCH_CUDA_ALLOC_CONF


Stable diffusion model failed to load
```

**解决方法**：

这个就是显卡内存不够导致的,可以参考该帖子：[在 NVidia GPU 上安装和运行](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Install-and-Run-on-NVidia-GPUs),也就是在启动脚本后面加上参数： `--medvram--lowvram` :
```bash
┌──(leazhi㉿kali-desktop)-[~/Downloads/stable-diffusion-webui]
└─$ /bin/bash -x webui.sh --lowvram --xformers                          
```

### 错误八：RuntimeError: "LayerNormKernelImpl" not implemented for 'Half'

没有找到 GPU ,本身为就没有安装显卡驱动！！！

```bash
loading stable diffusion model: RuntimeError
Traceback (most recent call last):
  File "/usr/local/python/lib/python3.10/threading.py", line 973, in _bootstrap
    self._bootstrap_inner()
  File "/usr/local/python/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/usr/local/python/lib/python3.10/threading.py", line 953, in run
    self._target(*self._args, **self._kwargs)
  File "/data/stable-diffusion-webui/modules/initialize.py", line 147, in load_model
    shared.sd_model  # noqa: B018
  File "/data/stable-diffusion-webui/modules/shared_items.py", line 110, in sd_model
    return modules.sd_models.model_data.get_sd_model()
  File "/data/stable-diffusion-webui/modules/sd_models.py", line 499, in get_sd_model
    load_model()
  File "/data/stable-diffusion-webui/modules/sd_models.py", line 649, in load_model
    sd_model.cond_stage_model_empty_prompt = get_empty_cond(sd_model)
  File "/data/stable-diffusion-webui/modules/sd_models.py", line 537, in get_empty_cond
    return sd_model.cond_stage_model([""])
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1501, in _call_impl
    return forward_call(*args, **kwargs)
  File "/data/stable-diffusion-webui/modules/sd_hijack_clip.py", line 234, in forward
    z = self.process_tokens(tokens, multipliers)
  File "/data/stable-diffusion-webui/modules/sd_hijack_clip.py", line 273, in process_tokens
    z = self.encode_with_transformers(tokens)
  File "/data/stable-diffusion-webui/modules/sd_hijack_clip.py", line 326, in encode_with_transformers
    outputs = self.wrapped.transformer(input_ids=tokens, output_hidden_states=-opts.CLIP_stop_at_last_layers)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1501, in _call_impl
    return forward_call(*args, **kwargs)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/transformers/models/clip/modeling_clip.py", line 822, in forward
    return self.text_model(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1501, in _call_impl
    return forward_call(*args, **kwargs)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/transformers/models/clip/modeling_clip.py", line 740, in forward
    encoder_outputs = self.encoder(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1501, in _call_impl
    return forward_call(*args, **kwargs)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/transformers/models/clip/modeling_clip.py", line 654, in forward
    layer_outputs = encoder_layer(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1501, in _call_impl
    return forward_call(*args, **kwargs)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/transformers/models/clip/modeling_clip.py", line 382, in forward
    hidden_states = self.layer_norm1(hidden_states)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1501, in _call_impl
    return forward_call(*args, **kwargs)
  File "/data/stable-diffusion-webui/extensions-builtin/Lora/networks.py", line 474, in network_LayerNorm_forward
    return originals.LayerNorm_forward(self, input)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/normalization.py", line 190, in forward
    return F.layer_norm(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/functional.py", line 2515, in layer_norm
    return torch.layer_norm(input, normalized_shape, weight, bias, eps, torch.backends.cudnn.enabled)
RuntimeError: "LayerNormKernelImpl" not implemented for 'Half'


Stable diffusion model failed to load
Exception in thread Thread-2 (load_model):
Traceback (most recent call last):
  File "/usr/local/python/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/usr/local/python/lib/python3.10/threading.py", line 953, in run
    self._target(*self._args, **self._kwargs)
  File "/data/stable-diffusion-webui/modules/initialize.py", line 153, in load_model
    devices.first_time_calculation()
  File "/data/stable-diffusion-webui/modules/devices.py", line 148, in first_time_calculation
    linear(x)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/module.py", line 1501, in _call_impl
    return forward_call(*args, **kwargs)
  File "/data/stable-diffusion-webui/extensions-builtin/Lora/networks.py", line 429, in network_Linear_forward
    return originals.Linear_forward(self, input)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/torch/nn/modules/linear.py", line 114, in forward
    return F.linear(input, self.weight, self.bias)
RuntimeError: "addmm_impl_cpu_" not implemented for 'Half'
Traceback (most recent call last):
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/routes.py", line 488, in run_predict
    output = await app.get_blocks().process_api(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1431, in process_api
    result = await self.call_function(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1103, in call_function
    prediction = await anyio.to_thread.run_sync(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/to_thread.py", line 33, in run_sync
    return await get_asynclib().run_sync_in_worker_thread(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 877, in run_sync_in_worker_thread
    return await future
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/data/stable-diffusion-webui/modules/ui_extra_networks.py", line 392, in pages_html
    return refresh()
  File "/data/stable-diffusion-webui/modules/ui_extra_networks.py", line 398, in refresh
    pg.refresh()
  File "/data/stable-diffusion-webui/modules/ui_extra_networks_textual_inversion.py", line 13, in refresh
    sd_hijack.model_hijack.embedding_db.load_textual_inversion_embeddings(force_reload=True)
  File "/data/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 255, in load_textual_inversion_embeddings
    self.expected_shape = self.get_expected_shape()
  File "/data/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 154, in get_expected_shape
    vec = shared.sd_model.cond_stage_model.encode_embedding_init_text(",", 1)
AttributeError: 'NoneType' object has no attribute 'cond_stage_model'
Traceback (most recent call last):
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/routes.py", line 488, in run_predict
    output = await app.get_blocks().process_api(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1431, in process_api
    result = await self.call_function(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/blocks.py", line 1103, in call_function
    prediction = await anyio.to_thread.run_sync(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/to_thread.py", line 33, in run_sync
    return await get_asynclib().run_sync_in_worker_thread(
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 877, in run_sync_in_worker_thread
    return await future
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/anyio/_backends/_asyncio.py", line 807, in run
    result = context.run(func, *args)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/gradio/utils.py", line 707, in wrapper
    response = f(*args, **kwargs)
  File "/data/stable-diffusion-webui/modules/ui_extra_networks.py", line 392, in pages_html
    return refresh()
  File "/data/stable-diffusion-webui/modules/ui_extra_networks.py", line 398, in refresh
    pg.refresh()
  File "/data/stable-diffusion-webui/modules/ui_extra_networks_textual_inversion.py", line 13, in refresh
    sd_hijack.model_hijack.embedding_db.load_textual_inversion_embeddings(force_reload=True)
  File "/data/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 255, in load_textual_inversion_embeddings
    self.expected_shape = self.get_expected_shape()
  File "/data/stable-diffusion-webui/modules/textual_inversion/textual_inversion.py", line 154, in get_expected_shape
    vec = shared.sd_model.cond_stage_model.encode_embedding_init_text(",", 1)
AttributeError: 'NoneType' object has no attribute 'cond_stage_model'

```

**解决方法**：

启动时加上参数 --no-half ,跳过 GPU ：
```bash
leazhi@leazhi-System-Version:/data/stable-diffusion-webui 13:44:34$ ./webui.sh --skip-torch-cuda-test  --no-half
```

### 错误九：OSError: Can't load tokenizer for 'openai/clip-vit-large-patch14'

模块 openai/clip-vit-large-patch14 下载失败：
```bash
Startup time: 2.8s (import torch: 1.1s, import gradio: 0.3s, setup paths: 0.3s, other imports: 0.2s, load scripts: 0.2s, create ui: 0.3s, gradio launch: 0.3s).
creating model quickly: OSError
Traceback (most recent call last):
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 973, in _bootstrap
    self._bootstrap_inner()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/usr/local/python3.10.6/lib/python3.10/threading.py", line 953, in run
    self._target(*self._args, **self._kwargs)
  File "/data/stable-diffusion-webui/modules/initialize.py", line 147, in load_model
    shared.sd_model  # noqa: B018
  File "/data/stable-diffusion-webui/modules/shared_items.py", line 110, in sd_model
    return modules.sd_models.model_data.get_sd_model()
  File "/data/stable-diffusion-webui/modules/sd_models.py", line 499, in get_sd_model
    load_model()
  File "/data/stable-diffusion-webui/modules/sd_models.py", line 602, in load_model
    sd_model = instantiate_from_config(sd_config.model)
  File "/data/stable-diffusion-webui/repositories/stable-diffusion-stability-ai/ldm/util.py", line 89, in instantiate_from_config
    return get_obj_from_str(config["target"])(**config.get("params", dict()))
  File "/data/stable-diffusion-webui/repositories/stable-diffusion-stability-ai/ldm/models/diffusion/ddpm.py", line 563, in __init__
    self.instantiate_cond_stage(cond_stage_config)
  File "/data/stable-diffusion-webui/repositories/stable-diffusion-stability-ai/ldm/models/diffusion/ddpm.py", line 630, in instantiate_cond_stage
    model = instantiate_from_config(config)
  File "/data/stable-diffusion-webui/repositories/stable-diffusion-stability-ai/ldm/util.py", line 89, in instantiate_from_config
    return get_obj_from_str(config["target"])(**config.get("params", dict()))
  File "/data/stable-diffusion-webui/repositories/stable-diffusion-stability-ai/ldm/modules/encoders/modules.py", line 103, in __init__
    self.tokenizer = CLIPTokenizer.from_pretrained(version)
  File "/data/stable-diffusion-webui/venv/lib/python3.10/site-packages/transformers/tokenization_utils_base.py", line 1809, in from_pretrained
    raise EnvironmentError(
OSError: Can't load tokenizer for 'openai/clip-vit-large-patch14'. If you were trying to load it from 'https://huggingface.co/models', make sure you don't have a local directory with the same name. Otherwise, make sure 'openai/clip-vit-large-patch14' is the correct path to a directory containing all relevant files for a CLIPTokenizer tokenizer.

Failed to create model quickly; will retry using slow method.
```

**解决方法**：

1.部署好网络，把该模型 [openai/clip-vit-large-patch14](https://huggingface.co/openai/clip-vit-large-patch14/tree/main) 下的所有文件下载到 stable-diffusion-webui 目录下的 `openai/clip-vit-large-patch14` 目录下:

2.修改文件 `/data/stable-diffusion-webui/repositories/stable-diffusion-stability-ai/ldm/modules/encoders/modules.pyversion` 中的 version 地址，指向模型 `openai/clip-vit-large-patch14` 存放目录