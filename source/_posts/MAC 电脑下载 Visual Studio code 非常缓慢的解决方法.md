---
title: Mac 电脑下载 Visual Studio code 非常缓慢的解决方法
author: leazhi
tags:
  - [Mac]
  - [vscode]
categories:
  - [mac]
date: 2023-07-22 08:09:37
cover: https://hexo.linuser.com/gallery/wallpaper/36582.jpeg
discription: 
keywords: 
password: 
message: 
---

## 概述


### 环境： MacBook Pro

### 经过：

今天携带 MAC 电脑在外面出差，本想通过 VSCODE 写些开发文档通过自己配置的 gitlab 自动发布到自己的网站。结果打开 MAC ，发现，原来 MAC 上没有安装 VSCODE。

几经尝试，原本想通过 Visual Studio Code 官方站点：https://code.visualstudio.com/ 下载最新版本的 VSCODE 进行安装。结果发现，那个下载速度是非常的缓慢，只有几 KB ，甚至是下着下着突然就停止下载，最后提示下载失败。窝火！！！

## 解决步骤

1.点击下载项，鼠标右键，拷贝下载项真实地址：如：https://az764295.vo.msecnd.net/stable/c3f126316369cd610563c75b1b1725e0679adfb3/VSCode-darwin-universal.zip

2.修改上面地址的域名：把 az764295.vo.msecnd.net 替换成 vscode.cdn.azure.cn，这是国内的镜像，再把下面的地址copy到浏览器，再按回车，如： https://vscode.cdn.azure.cn/stable/c3f126316369cd610563c75b1b1725e0679adfb3/VSCode-darwin-universal.zip

现在的下载速度非常快！！！