---
title: hexo 系列006:创建 关于（About）导航栏
author: leazhi
tags:
  - hexo
categories:
  - hexo
date: 2023-07-18 14:38:32
cover: https://hexo.linuser.com/gallery/wallpaper/1044085.png
discription: 
keywords: hexo, 博客， blog
password: 
message: 
---

1.进入  hexo 站点根目录:
```bash
cd /data/hexo/blog/
```

2.执行命令 `hexo new page about` ,执行完成之后，会在 source/ 目录下新建一个 link 的目录并在该目录下自动创建 index.md 文件

{% note danger simple %}
注意：该文件不需要在头部信息中添加 `type: "about"`
{% endnote %}




