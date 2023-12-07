---
title: hexo 系列014-设置文章标题模板
author: leazhi
tags:
  - [hexo]
categories:
  - [hexo]
date: 2023-07-19 15:25:05
cover: https://hexo.linuser.com/gallery/wallpaper/1044085.png
discription: 
keywords: hexo, 博客， blog
password: 
message: 
---

1.进入 hexo 工作目录（也就是博客的根目录）：
```bash
cd /data/hexo/blog
```

2.编辑 `scaffolds/post.md ` 文件（前提是：在 hexo 站点配置文件<非主题配置文件>中，设置 `default_layout: post`），将其修改为：
```bash
---
title: {{ title }}
date: {{ date }}
author: leazhi
tags:                               # 标签
  -  
categories:                         # 分类
  - 
cover:                              # 设置文章顶部图片
discription:                        # 文章描述信息
keywords:                           # 文章关键字
password:                           # 文章加密（不设置密码表示不加密）
message:                            # 文章加密提示
---
```

3.后面使用命令 `hexo new xxxx` 新建文章时,就会自带上面的配置信息了！