---
title: hexo 系列008:开启本地搜索功能
author: leazhi
tags:
  - hexo
categories:
  - hexo
date: 2023-07-18 15:03:05
cover: https://hexo.linuser.com/gallery/wallpaper/1044085.png
discription: 
keywords: hexo, 博客， blog
password: 
message: 
---

1.进入 hexo 博客主目录：
```bash
cd /data/hexo/blog
```

2.安装搜索插件：
```bash
root@ubuntuhome:/data/hexo/blog# npm install hexo-generator-searchdb --save

added 1 package, and audited 448 packages in 3s

28 packages are looking for funding
  run `npm fund` for details

11 vulnerabilities (2 low, 2 moderate, 7 high)

Some issues need review, and may require choosing
a different dependency.

Run `npm audit` for details.
```

3.编辑主题配置文件： `themes/butterfly/_config.yml` ,搜索关键字 `local_search`, 将下面的 `enable: false` 修改成 `enable: true` .

4.重启下 hexo:
```bash
pm2 stop hexo_run.js && pm2 start hexo_run.js
```

5.再次刷新网页，在主页面就可以看到 `Search` 菜单了.
