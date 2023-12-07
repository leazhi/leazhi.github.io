---
title: hexo 系列001:创建标签（tags）导航栏
author: leazhi
tags:
  - hexo
categories:
  - hexo
date: 2023-07-18 14:34:20
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

2.执行命令 `hexo new page tags` ,执行完成之后，会在 source/ 目录下新建一个 tags 的目录并在该目录下自动创建 index.md 文件

3.编辑 source/tags/index.md 文件，在头部配置里面添加 `type: "tags"` ，如下：
```bash
root@ubuntuhome:/data/hexo/blog# cat source/tags/index.md 
---
title: tags
date: 2023-07-09 21:50:44
type: "tags"
---
```