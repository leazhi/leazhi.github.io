---
title: windows 7/8/10/11/2016/2019/2022 激活方法
author: leazhi
tags:
  - windows
categories:
  - windows
date: 2023-07-18 15:57:19
cover: https://hexo.linuser.com/gallery/wallpaper/win-1318918.jpg
discription: 
keywords: 
password: 
message: 
---

{% note danger simple %}
激活之前：
  - 必须以管理员的身份打开 cmd 或者 powershell
  - 必须先确认你的系统是否是 VL 版本（可以使用命令查看：wmic os get caption）
{% endnote %}

## 在线激活

网上提供的可以使用的一键激活网址（提供各版本的激活码，也包含 Office）：https://www.kms.pub/index.html。直接在 cmd 或者 powershell 下执行：
```bash
slmgr /skms win.kms.pub
slmgr /ato
```

## 手动激活

仅以激活 windows 10 为例，方法如下：

```cmd
# 专业版：
slmgr -upk
slmgr -ipk W269N-WFGWX-YVC9B-4J6C9-T83GX                
slmgr -skms kms.jm33.me:1688
slmgr -ato
slmgr -dlv

# 企业版：
slmgr.vbs /upk
slmgr /ipk NPPR9-FWDCX-D2C8J-H872K-2YT43
slmgr /skms win.kms.pub
slmgr /ato
```