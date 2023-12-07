---
title: 智能家居 home-assistant 系列 002-安装 HACS 商店
author: leazhi
tags:
  - [hassos]
  - [homeassistant]
categories:
  - 
date: 2023-10-08 13:09:21
cover: 
discription: 
keywords: 
password: 
message: 
---

**HACS 项目地址**：https://github.com/hacs/integration   
**HACS 官方地址**：https://hacs.xyz/docs/setup/download  

## 基本设置

### 解锁高级模式

在 Home Assistant 主页点击左下角 `UserID` （比如我创建的用户明为 leazhi）, 然后在右侧的 User 属性菜单列表中找到 `高级模式` 并启用：
![](../static/img/20231008/20231008108.jpg)


### 安装 Terminal & SSH

1.点击左侧菜单栏中的配置，然后在右侧的属性列表中点击 `加载项` ([官方提供的安装说明](https://www.home-assistant.io/installation/#compare-installation-methods)：非 OS 和 Supervised <比如 Docker 和 Core>安装的 home assistant 没有该属性 )
![](../static/img/20231008/20231008109.jpg)

2.进入 `加载项` 页面后，点击右下角的 `加载项商店` :
![](../static/img/20231008/20231008110.jpg)

3.在 `加载项商店` 的搜索栏中输入关键字 `ssh` , 然后点击匹配的 `Terminal & SSH`:
![](../static/img/20231008/20231008111.jpg)

4.进入到 `Terminal & SSH` 页面后，点击下面的 `安装` 对其安装，安装过程有点小慢，等待即可！：
![](../static/img/20231008/20231008112.jpg)

5.安装完成后，将守护属性和在侧边栏显示属性打开，然后点击下面的启动：
![](../static/img/20231008/20231008113.jpg)

6.接下来，我们点击左侧菜单栏中的 Terminal 就可以直接进入 Home Assistant Terminal 环境了：
![](../static/img/20231008/20231008114.jpg)


## 安装 HACS

{% note warning simple %}
**必备！必备！必备！ --- 安装 HACS 集成之前，请先打开 https://github.com/ 注册个帐号并登录（有帐号的直接使用帐号登录即可！）。后面集成 HACS 的时候需要使用 github 授权**
{% endnote %}
### 安装方法一：通过 SSH 安装 HACS
根据官方提供的安装说明 [OS/Supervised 安装方式](https://hacs.xyz/docs/setup/download/) ， 直接将命令 `wget -O - https://get.hacs.xyz | bash -` 复制到终端回车：
![](../static/img/20231008/20231008115.jpg)

### 安装方法二：通过本地解压

这种方式可以参考：[兼职单 005-R4S 刷 openwrt + docker 部署 homeassistant](https://hexo.linuser.com/2023/10/07/690a3b219bc3/) 中的安装 HACS 下的安装方法一

## 集成 HACS

安装完成后，点击左侧菜单栏中的配置，然后点击右上角的 三个点，点击重启 Home Assistant. 重启西 Homeassistant:
![](../static/img/20231008/20231008116.jpg)

1.点击左侧菜单栏中的配置，然后点击中间属性列表中的 设备于服务
![](../static/img/20231008/20231008117.jpg)

2.进入到设备于服务页面后，顶部选择集成，然后点击右下角的 添加集成：
![](../static/img/20231008/20231008118.jpg)

3.在弹出的选择品牌窗口搜索栏中输入关键字 hacs ，然后点击与之匹配的 HACS ：
![](../static/img/20231008/20231008119.jpg)

4.在 HACS 弹出的告知说明窗口，将其全部勾选， 然后点击提交：
![](../static/img/20231008/20231008120.jpg)

5.进入 Waiting for device activation 页面后，点击给出的激活地址，然后复制给出的激活 KEY：
![](../static/img/20231008/20231008121.jpg)

5.1.打开认证链接，输入激活 KEY：
![](../static/img/20231008/20231008122.jpg)

5.2.授权：
![](../static/img/20231008/20231008123.jpg)

![](../static/img/20231008/20231008124.jpg)

6.授权完成后，HACS 页面会有 成功的提示：
![](../static/img/20231008/20231008125.jpg)

**至此，HACS 安装集成完成！**