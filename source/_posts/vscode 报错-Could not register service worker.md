---
title: vscode 报错-Could not register service worker
author: leazhi
tags:
  - [vscode]
categories:
  - [vscode]
date: 2023-09-17 16:39:49
cover: 
discription: 
keywords: 
password: 
message: 
---

## 系统环境
- os: kali linux 2023.05
- Kernal: 6.4.0-kali3-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.4.11-1kali1 (2023-08-21) x86_64 GNU/Linux
- vscode: 1.82.2

## 问题描述

在vscode 中打开 markdown 文档，发现右侧不能预览了，同时，右下角给出弹窗：
```bash
Error loading webview: Error: Could not register service worker: InvalidStateError: Failed to register a ServiceWorker: The document is in an invalid state..
```

## 解决方案

直接删除当前用户目录下 vscode 的缓存目录：
```bash
rm -rf ~/.config/Code/Cache
```

然后重新打开即可！


## 参考：
[VS Code 加载 Web 视图时出错的解决方案](https://fengchao.pro/blog/vscode-error-could-not-register-service-workers-invalid-state/#%E6%8A%A5%E9%94%99%E5%8E%9F%E5%9B%A0)

