---
title: hexo 系列004:创建 分类（Categories）导航栏
author: leazhi
tags:
  - hexo
categories:
  - hexo
date: 2023-07-18 14:36:18
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

2.执行命令 `hexo new page categories` ,执行完成之后，会在 source/ 目录下新建一个 categories 的目录并在该目录下自动创建 index.md 文件

3.编辑 source/categories/index.md 文件，在头部配置里面添加 `type: "categories"` ，如下：
```bash
root@ubuntuhome:/data/hexo/blog# cat source/categories/index.md 
---
title: categories
date: 2023-07-09 21:53:53
type: "categories"
---
```
{% note danger simple %}
切记！！！ 这个目录导航一定要创建，后期要为一级导航栏添加子导航栏时需要用到！
{% endnote %}




