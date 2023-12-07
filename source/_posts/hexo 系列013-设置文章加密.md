---
title: hexo 系列013-设置文章加密
author: leazhi
tags:
  - [hexo]
categories:
  - [hexo]
date: 2023-07-19 15:22:33
cover: https://hexo.linuser.com/gallery/wallpaper/1044085.png
discription: 
keywords: hexo, 博客， blog
password: 
message: 
---

1.进入 hexo 站点根目录：
```bash
cd /data/hexo/blog
```

2.安装加密插件：
```bash
npm install --save hexo-blog-encrypt
```

3.重启 hexo ：
```bash
pm2 stop hexo_run.js && hexo start hexo_run.js
```

4.打开需要加密的文章，在顶部信息配置中输入：
```bash
---
title: 爬取大乐透开奖数据
date: 2023-07-13 20:15:13
author: leazhi
description: 这是一个爬取大乐透开奖数据的爬虫
tags:
  - python
  - spider
categories:
  - [python]
  - [spider]
cover:
password: 123456                            # 设置的密码
message: 抱歉，需要输入正确密码方可查看内容       # 提示信息   
---
```

5.打开文章，效果如下图：
![](/Images/vscode-20230716105113.png)