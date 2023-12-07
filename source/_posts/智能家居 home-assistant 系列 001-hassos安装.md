---
title: 智能家居 home-assistant 系列 001-hassos安装
author: leazhi
tags:
  - [hassos]
  - [homeassistant]
categories:
  - 
date: 2023-10-08 11:31:03
cover: 
discription: 
keywords: 
password: 
message: 
---

{% note warning simple %}
**重要！重要！重要！ --- 想顺利的部署好 Homeassistant OS，必须提前做好网络设置（要求能翻墙，因为 homeassistant 安装过程中会到国外的站点下载所需的文件<docker 镜像>）**
{% endnote %}

{% note default simple %}
**homeassistant 官方安装链接地址：https://www.home-assistant.io/installation/**
{% endnote %}

**本教程采用 Windows 10 Pro Hyper-V 的方式安装。关于 Windows 10 Pro 下如何安装 Hyper-V 请参考：https://hexo.linuser.com/2023/10/08/c081c0fce623/**


## 重要！！！必读

在开始搞 Home Assistant 之前，希望后来的读者先着重关注下这部分的内容！毕竟，这是经过本人实践获之不易的经验！！！

**首先**：必须要有畅通无阻的网络环境（不仅仅是能上网，哪怕你是 1000M 也不行），这里的畅通无阻不是单纯的网络访问；   

**其次**：要能明白下图中各安装方式的区别，不懂的去网络搜：
![](../static/img/20231008/20231008126.jpg)

**最后**：必须要有足够强的耐心，哪怕你是小白，只要你有耐心，也可以做出漂亮的 Homeassistant!

## 创建 Hyper-V 虚拟机

1.打开 Hyper-V 管理器，点击右侧操作菜单栏下的新建，如同：
![](../static/img/20231008/20231008020.jpg)

2.在弹出的新建虚拟机向导窗口输入虚拟机名称，及指定虚拟机存放位置：
![](../static/img/20231008/20231008021.jpg)

3.根据官方安装文档说明，使用 Hyper-V 进行安装的话，需要使用第二代虚拟机：
![](../static/img/20231008/20231008022.jpg)

4.根据官方安装文档说明，为 Hyper-V 虚拟机要分配大于等于 2 G 的内存：
![](../static/img/20231008/20231008023.jpg)

5.由于我想在局域网内其它机器也能访问，所以我这里选择 WAN 网络类型（关于 WAN 网络类型的创建也可以参考：[Windows 10 Pro 启用（安装）Hyper-V](https://hexo.linuser.com/2023/10/08/c081c0fce623/#%E5%88%9B%E5%BB%BA%E6%A1%A5%E6%8E%A5%E7%BD%91%E7%BB%9C)）：
![](../static/img/20231008/20231008024.jpg)

6.虚拟机硬盘选择我们从[官方下载 Windows Hyper-V  镜像](https://github.com/home-assistant/operating-system/releases/download/10.5/haos_ova-10.5.vhdx.zip)并解压出来的 .vhdx 虚拟硬盘 
![](../static/img/20231008/20231008025.jpg)

7.到此，虚拟机创建完成！
![](../static/img/20231008/20231008026.jpg)

8.在启动虚拟机之前，根据官方提供的安装文档说明，还需要关闭虚拟机的安全启动，具体步骤如下：

8.1.在 Hyper-V 管理器窗口右键点击新建好的虚拟机，在弹出的属性列表中点击 `设置`：
![](../static/img/20231008/20231008027.jpg)

8.2.在弹出的虚拟机设置窗口中取消右侧 `安全` 属性下的 `启用安全启动` 前面的勾：
![](../static/img/20231008/20231008028.jpg)

9.启动虚拟机！


## Homeassistant 安装

1.虚拟机启动就意味着 Homeassistant 开始安装（为什么？因为这是 Homeassistant 官方封装好的镜像），安装过程全程自动化，无须手动干预，直到出现：
![](../static/img/20231008/20231008029.jpg)

2.出现这个页面，并不代表 homeassistant 安装完成，此时，我们可以打开浏览器，按照上图提示，访问 homeassistant 界面：
![](../static/img/20231008/20231008030.jpg)

**这个安装过程非常缓慢，需要等待！电脑不能休眠，更不能重启！只能等其自动安装完成，直到出现下图**
![](../static/img/20231008/20231008101.jpg)


## Homeassistant 初始化

1.点击上图中的创建爱女我的只能家居，准备开始初始化 Homeassistant:
![](../static/img/20231008/20231008102.jpg)

2.输入初始化管理员帐号密码（该帐号密码就是超级管理员帐号了）：
![](../static/img/20231008/20231008103.jpg)

3.设置家庭位置（这个后期设置离家头像有用），输入你所在城市拼音进行搜索，如：
![](../static/img/20231008/20231008104.jpg)

4.跳过帮助：
![](../static/img/20231008/20231008105.jpg)

5.到此，homeassistant 初始化完成！
![](../static/img/20231008/20231008106.jpg)

6.初始化完成后，自动登录到了 homeassistant:
![](../static/img/20231008/20231008107.jpg)


























Config Template Card Card
mini-graph-card
Simple Weather Card
weather card
Xiaomi Vacuum Map Card
Mini Media Player
Colorfulclouds Weather Card
彩云天气