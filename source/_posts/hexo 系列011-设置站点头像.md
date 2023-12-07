---
title: hexo 系列011:设置站点头像
author: leazhi
tags:
  - [hexo]
categories:
  - [hexo]
date: 2023-07-19 15:17:54
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

2.在 source/static/ 目录下新建目录 img 目录：
```bash
mkdir source/static/img/
```

3.上传要做图像的图片到新建的 img 目录：

4.编辑 主题主配置文件 `themes/butterfly/_config.yml` ,找到关键字： `avatar:` ,将 img 指向 source 目录下的 `/static/img/Touxiang.jpg`
```bash
# Avatar (頭像)
avatar:
  img: /static/img/Touxiang.jpg
  effect: false
```

5.打开浏览器，输入 hexo 网址，即可看到图像已经改过来了！