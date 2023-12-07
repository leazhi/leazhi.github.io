---
title: ubuntu 22.04 服务部署-Stable Diffusion
author: leazhi
tags:
  - [stable diffusion]
  - [Linux]
categories:
  - [linux]
  - [kali]
date: 2023-10-10 22:08:30
cover: 
discription: 
keywords: 
password: 
message: 
---

## 系统环境

- 系统版本：ubuntu-23.10-desktop-amd64.iso
- 内核版本：6.5.0-9-generic #9-Ubuntu SMP PREEMPT_DYNAMIC Sat Oct  7 01:35:40 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
- Python版本：Python 3.11.5

## 硬件配置
- 内存：64G
- 显卡：GeForce GTX 1050 Ti
- 硬盘：2T SSD
- CPU：Inter Core i7-10700K x16
- 主板：ASUS ROG STRIX Z490-A GAMING

## 前提条件
根据 [AUTOMATIC1111/stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui/) 提供的文档说明，需要满足 这些条件：[点这里查看需要满足的条件](https://github.com/AUTOMATIC1111/stable-diffusion-webui/#installation-and-running)

- 一定要用良好的网络环境，这个是保证顺利安装 Stable Diffusion 的首要任务(比如，能正常访问到 :https://huggingface.cn)
- Python 版本：3.10.6
- 推荐使用 Nvidia 显卡

## 部署过程

### python 降级（升级）

根据安装说明，首要任务就是对 python 进行降级，过程如下;

**关于 python 的降级操作，请参考：[python3 升、降级的正确方式](https://hexo.linuser.com/2023/10/14/8c8168212c10/)**

### Stable Diffusion 部署

1.从项目地址将 Stable Diffusion 克隆到本地：
```bash
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
```

2.进入克隆目录：
```bash
cd stable-diffusion-webui
```

#### 方式一：使用项目提供的 shell 脚本

1.将脚本下载到本地：
```bash
leazhi@leazhi-System-Version:~$ wget -q https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/webui.sh
```
2.创建目录：
```bash
leazhi@leazhi-System-Version:~$  python -m venv ./venv
```

3.执行脚本：
```bash
leazhi@leazhi-System-Version:/data/stable-diffusion-webui$ ./webui.sh --skip-torch-cuda-test --lowvram --xformers --no-half

################################################################
Install script for stable-diffusion + Web UI
Tested on Debian 11 (Bullseye)
################################################################

################################################################
Running on leazhi user
################################################################

################################################################
Repo already cloned, using it as install directory
################################################################

################################################################
Create and activate python venv
################################################################

################################################################
Launching launch.py...
################################################################
Cannot locate TCMalloc (improves CPU memory usage)
Python 3.10.6 (main, Oct 13 2023, 21:08:02) [GCC 13.2.0]
Version: v1.6.0
Commit hash: 5ef669de080814067961f28357256e8fe27544f4
Launching Web UI with arguments: --skip-torch-cuda-test --lowvram --xformers --no-half
Loading weights [6ce0161689] from /data/stable-diffusion-webui/models/Stable-diffusion/v1-5-pruned-emaonly.safetensors
Running on local URL:  http://127.0.0.1:7860

To create a public link, set `share=True` in `launch()`.
Startup time: 5.3s (import torch: 2.4s, import gradio: 0.5s, setup paths: 0.9s, initialize shared: 0.1s, other imports: 0.4s, load scripts: 0.3s, create ui: 0.4s, gradio launch: 0.1s).
Creating model from config: /data/stable-diffusion-webui/configs/v1-inference.yaml
Applying attention optimization: xformers... done.
Model loaded in 2.8s (load weights from disk: 0.7s, create model: 0.3s, apply weights to model: 1.4s, calculate empty prompt: 0.3s).
```

**安装过程报错，请参考**：[Stable Diffusion 报错及解决方法](https://hexo.linuser.com/2023/10/11/67db69a57348/)

#### 方式二：执行项目中的 python 脚本

直接在项目目录下执行 ` python launch.py --lowvram --xformers` 进行安装（为了加快安装速度：这里推荐替换 launch.py 文件中的：:227,241s@https://github.com@https://ghproxy.com/https://github.com@g ）：
```bash
leazhi@leazhi-System-Version:~$ python launch.py  --skip-torch-cuda-test --lowvram --xformers --no-half                    

Python 3.10.13 (main, Oct 10 2023, 18:56:10) [GCC 13.2.0]
Version: v1.6.0
Commit hash: 5ef669de080814067961f28357256e8fe27544f4
Installing torch and torchvision
Defaulting to user installation because normal site-packages is not writeable
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple, https://download.pytorch.org/whl/cu118
Collecting torch==2.0.1
  Downloading https://download.pytorch.org/whl/cu118/torch-2.0.1%2Bcu118-cp310-cp310-linux_x86_64.whl (2267.3 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 0.0/2.3 GB 421.2 kB/s eta 1:29:40
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, please update the hashes. Otherwise, examine the package contents carefully; someone may have tampered with them.
    torch==2.0.1 from https://download.pytorch.org/whl/cu118/torch-2.0.1%2Bcu118-cp310-cp310-linux_x86_64.whl#sha256=a7a49d459bf4862f64f7bc1a68beccf8881c2fa9f3e0569608e16ba6f85ebf7b:
        Expected sha256 a7a49d459bf4862f64f7bc1a68beccf8881c2fa9f3e0569608e16ba6f85ebf7b
             Got        1d2b716c98d92c3b7fb015ec31ee4cc6855a2f9bd3bdff53d064b21ec01f9a37


[notice] A new release of pip is available: 23.0.1 -> 23.2.1
[notice] To update, run: pip3.10 install --upgrade pip
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

**安装过程报错，请参考**：[Stable Diffusion 报错及解决方法](https://hexo.linuser.com/2023/10/11/67db69a57348/)


### 快捷启动方式

在 `/usr/share/applications` 目录下创建 `stable-diffusion-webui.desktop` 文件，内容如下：
```bash
[Desktop Entry]
Version=v1.6
Encoding=UTF-8
Name=Stable Diffusion WebUI
Exec=/data/stable-diffusion-webui/start_stable_diffusion_webui.sh
Icon=/data/stable-diffusion-webui/modules/icons/stable-diffusion-webui.
GenericName=Stable Diffusion AI
GenericName[zh_CN]=AI 制图
Keywords=sd
Categories=Network;
# Exec=/data/stable-diffusion-webui/launch.py
Type=Application
Terminal=false
StartupNotify=true
```

脚本 `/data/stable-diffusion-webui/start_stable_diffusion_webui.sh` 内容：
```bash
#!/usr/bin/env path
#
Stable_Diffusion_Dir=/data/stable-diffusion-webui

if [ ! -d "${Stable_Diffusion_Dir}" ]; then
    echo "Stable Diffusion Install Dir does not exist"
    exit 0
else
   cd ${Stable_Diffusion_Dir}
   ./webui.sh --skip-torch-cuda-test --lowvram --xformers  --no-half
fi
```

## 参考：

- 1.[在 NVidia GPU 上安装和运行](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Install-and-Run-on-NVidia-GPUs)
- 2.[在 AMD GPU 上安装和运行](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Install-and-Run-on-AMD-GPUs)
