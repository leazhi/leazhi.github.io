---
title: 兼职单 007-在虚拟机中安装 Stable Diffusion.md
author: leazhi
tags:
  - [stable diffusion]
categories:
  - [stable diffusion]
date: 2023-10-10 08:46:48
cover: 
discription: 
keywords: 
password: 
message: 
---


## 硬件资源
- 处理器：13th Gen Intel(R) Core(TM) i7-13700K   3.40 GHz
- 内存：64G
- 硬盘：4T SSD
- 显卡：AMD Radeon RT 7900 XTX

## 系统环境：
- Windows 11 专业版 22H2

## 客户需求：
要求在该电脑上安装虚拟机，并且在虚拟机中安装 Stable Diffusion。同时，尽可能多的安装 Stable Diffusion 相关组件，如：
- 安装 Stable Diffusion 软件
- 安装 Stable Diffusion 相关组件


## 技术选型
- VMware-workstation-full-17.0.2-21581411
   - 序列号：JU090-6039P-08409-8J0QH-2YR7F
- ubuntu-22.04.3-desktop-amd64

- CPU：4*2
- Memory：16G
- Disk space：150G

## 部署要求

- Python 版本要求最好在 python3.10.6 这个版本，安装好的 ubuntu 22.04.3 自带 
    ```bahs
    ubuntu@ubuntu-virtual-machine:~$ python3 --version
    Python 3.10.12
    ```
## 部署步骤：

1.从 [点击这里 Stable Diffusion](https://github.com/invoke-ai/InvokeAI) 下载打包好的 zip 文件到服务器：
```bash

wget -O /usr/local/src/InvokeAI-installer-v3.2.0.zip https://objects.githubusercontent.com/github-production-release-asset-2e65be/525592995/1f6b7a66-f77f-4315-a393-ae17a53ecc0f?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231010%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231010T013247Z&X-Amz-Expires=300&X-Amz-Signature=99f9d2da5b6c51054b2fa5835e0a6c49d2d00bd9ef765f7a219435a78c58c76b&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=525592995&response-content-disposition=attachment%3B%20filename%3DInvokeAI-installer-v3.2.0.zip&response-content-type=application%2Foctet-stream



https://github.com/invoke-ai/InvokeAI
```

2.将下载文件解压到下载目录：


3.按钻过
```
$ sudo apt install -y python3.10-venv
```

4.进入解压目录，执行 ./install.sh
```bash
ubuntu@ubuntu-virtual-machine:~/下载/InvokeAI-installer-v3.2.0/InvokeAI-Installe
r$ ./install.sh 
Initializing the installer. This may take a minute - please wait...
────────────────────────────────────────────────────────────────────────────────
╔═════════════════════ Welcome to the InvokeAI Installer ══════════════════════╗
║                                                                              ║
║  Some of the installation steps take a long time to run. Please be patient.  ║
║   If the script appears to hang for more than 10 minutes, please interrupt   ║
║                          with Control-C and retry.                           ║
║                                                                              ║
╚════════════════════════════════ Linux-x86_64 ════════════════════════════════╝

InvokeAI will be installed in /home/ubuntu/invokeai
Use this location? [y/n] (y): y

───────────────────── 👾 GPU (Graphics Card) selection 👾 ──────────────────────
                                                                                
                                                                                
  Detected the Linux-x86_64 platform                                            
                                                                                
  See https://invoke-ai.github.io/InvokeAI/#system to ensure your system meets  
  the minimum requirements.                                                     
                                                                                
  🠶 Your GPU drivers must be correctly installed before using InvokeAI! 🠴       
                                                                                
  Please select the type of GPU installed in your computer.                     
                                                                                
    1 🢒an NVIDIA GPU (using CUDA™)                                              
    2 🢒an AMD GPU (using ROCm™)                                                 
    3 🢒no compatible GPU, or specifically prefer to use the CPU                 
    4 🢒I'm not sure what to choose                                              
                                                                                
                                                                                
                                                                                
Please make your selection: 2
──────────────────────────── Installing PyTorch 🔥 ─────────────────────────────
Looking in indexes: https://pypi.org/simple, https://download.pytorch.org/whl/rocm5.4.2
Collecting numpy~=1.24.0
  ...
  0.1+rocm5.4.2 torchmetrics-0.11.4 torchvision-0.15.2+rocm5.4.2 typing-extensions-4.8.0 urllib3-1.26.17
────────────────────────────────────────────────────────────── Installing the InvokeAI Application 🎨 ───────────────────────────────────────────────────────────────
Looking in indexes: https://pypi.org/simple, https://download.pytorch.org/
...
8 websockets-11.0.3 werkzeug-3.0.0 wsproto-1.2.0 xxhash-3.4.1 yapf-0.40.2 yarl-1.9.2 zipp-3.17.0
────────────────────────────────────────────────────────────────── InvokeAI Configuration Section ───────────────────────────────────────────────────────────────────
                                                                                                                                                                     
                                                                                                                                                                     
  Libraries are installed and InvokeAI will now set up its root directory and configuration. Choose between:                                                         
                                                                                                                                                                     
    * AUTOMATIC configuration:  install reasonable defaults and a minimal set of starter models.                                                                     
    * MANUAL configuration: manually inspect and adjust configuration options and pick from a larger set of starter models.                                          
                                                                                                                                                                     
  Later you can fine tune your configuration by selecting option [6] 'Change InvokeAI startup options' from the invoke.bat/invoke.sh launcher script.                
                                                                                                                                                                     
                                                                                                                                                                     
Choose <a>utomatic or <m>anual configuration [a/m] (a): a
[2023-10-10 11:46:03,292]::[InvokeAI]::INFO --> Initializing InvokeAI runtime directory
[2023-10-10 11:46:03,297]::[InvokeAI]::INFO --> Scanning /home/ubuntu/invokeai/models for new models
[2023-10-10 11:46:03,345]::[InvokeAI]::INFO --> Scanned 8 files and directories, imported 0 models
[2023-10-10 11:46:03,389]::[InvokeAI]::INFO --> Installing support models
[2023-10-10 11:46:03,389]::[InvokeAI]::INFO --> Installing ESRGAN Upscaling models...
[2023-10-10 11:46:03,389]::[InvokeAI]::INFO --> Installing RealESRGAN_x4plus.pth model file https://github.com/xinntao/Real-ESRGAN/releases/download/v0.1.0/RealESRGAN_x4plus.pth...
[2023-10-10 11:46:03,389]::[InvokeAI]::INFO --> ...exists
[2023-10-10 11:46:03,389]::[InvokeAI]::INFO --> Installing RealESRGAN_x4plus_anime_6B.pth model file https://github.com/xinntao/Real-ESRGAN/releases/download/v0.2.2.4/RealESRGAN_x4plus_anime_6B.pth...
[2023-10-10 11:46:03,389]::[InvokeAI]::INFO --> ...exists
[2023-10-10 11:46:03,389]::[InvokeAI]::INFO --> Installing ESRGAN_SRx4_DF2KOST_official.pth model file https://github.com/xinntao/Real-ESRGAN/releases/download/v0.1.1/ESRGAN_SRx4_DF2KOST_official-ff704c30.pth...
[2023-10-10 11:46:03,390]::[InvokeAI]::INFO --> ...exists
[2023-10-10 11:46:03,390]::[InvokeAI]::INFO --> Installing RealESRGAN_x2plus.pth model file https://github.com/xinntao/Real-ESRGAN/releases/download/v0.2.1/RealESRGAN_x2plus.pth...
[2023-10-10 11:46:03,390]::[InvokeAI]::INFO --> ...exists
[2023-10-10 11:46:03,390]::[InvokeAI]::INFO --> Installing lama infill model
[2023-10-10 11:46:03,390]::[InvokeAI]::INFO --> Installing lama infill model model file https://github.com/Sanster/models/releases/download/add_big_lama/big-lama.pt...
[2023-10-10 11:46:03,390]::[InvokeAI]::INFO --> ...exists
[2023-10-10 11:46:03,390]::[InvokeAI]::INFO --> Downloading core tokenizers and text encoders
[2023-10-10 11:47:03,512]::[InvokeAI]::ERROR --> Can't load tokenizer for 'bert-base-uncased'. If you were trying to load it from 'https://huggingface.co/models', make sure you don't have a local directory with the same name. Otherwise, make sure 'bert-base-uncased' is the correct path to a directory containing all relevant files for a BertTokenizerFast tokenizer.
[2023-10-10 11:47:03,561]::[InvokeAI]::INFO --> Scanning /home/ubuntu/invokeai/models for new models
[2023-10-10 11:47:03,594]::[InvokeAI]::INFO --> Scanned 8 files and directories, imported 0 models
[2023-10-10 11:47:03,621]::[InvokeAI]::INFO --> Installing runwayml/stable-diffusion-inpainting [1/11]

```


pth 文件下载路径：
```
/home/ubuntu/invokeai/models/core/upscaling/realesrgan
```






```v2ray-client

{
  "inbounds": [
    {
      "port": 1080,
      "protocol": "socks",
      "domainOverride": ["tls", "http"],
      "settings": {
        "auth": "noauth"
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "xiaomi.cdn003.com",
            "port": 443,
            "users": [
              {
                "id": "dcd02033-0237-442b-953f-9ccf84d8cd01",
		"alterId": 0
	      }
	    ]
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "security": "tls",
        "wsSettings": {
          "path": "/"
        }
      }
    }
  ]
}

```