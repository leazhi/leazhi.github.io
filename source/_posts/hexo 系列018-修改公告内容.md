---
title: hexo 系列018-修改公告内容
author: leazhi
tags:
  - [hexo]
categories:
  - [hexo]
date: 2023-07-23 21:24:39
cover: https://hexo.linuser.com/gallery/wallpaper/1044085.png
discription: 
keywords: hexo, blog, 博客
password: 
message: 
---


1.进入  hexo 站点根目录:
```bash
cd /data/hexo/blog/
```

2.编辑主题配置文件 `themes/butterfly/_config.yml` ,找到关键字 `aside:`, 修改其下面的 `card_author:` 字段下面的 `card_announcement:` 字段,如下：
```bash
aside:
  enable: true
  hide: false
  button: true
  mobile: true # display on mobile
  position: right # left or right
  display:
    archive: true
    tag: true
    category: true
  card_author:
    enable: true
    description:
    button:
      enable: true
      icon: fa-brands fa-gitlab
      text: 私有Gitlab                                      # 将 Follow me 修改成自己想要展示的内容
      link: https://gitlab.linuser.com/users/sign_in        # 我这里修改成了自己私有 gitlab 
    card_announcement:
    enable: true                                            # 启用公告
    content: 该Hexo 记录的是本人日常学习工作中所用到的一些电脑技术，有什么不对的地方，欢迎在留言板中批评指出。本人看到后第一时间修正！！！                      # 公告内容
```