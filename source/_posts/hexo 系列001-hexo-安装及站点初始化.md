---
title: hexo 系列001:hexo 安装及站点初始化
author: leazhi
date: 2023-07-15 19:40:45
tags:
  - hexo
categories:
  - hexo
cover: https://hexo.linuser.com/gallery/wallpaper/1044085.png
discription:
keywords: hexo, 博客， blog
  - hexo
---
1.安装好 nodejs：

2.创建 hexo 安装目录：
```bash
mkdir -p /data/hexo
```

3.进入创建目录，执行命令安装 hexo
```base
cd /data/hexo && npm install hexo-cli -g
```

4.初始化站点目录：
```bash
hexo init blog
```

5.进入站点目录，进行安装：
```bash
cd blog && hexo install
```

6.启动 hexo
```bash
hexo server
```

7.查看安装的 hexo 版本：
```bash
root@ubuntuhome:/data/hexo/blog# hexo --version
INFO  Validating config
INFO  
  ===================================================================
      #####  #    # ##### ##### ###### #####  ###### #      #   #
      #    # #    #   #     #   #      #    # #      #       # #
      #####  #    #   #     #   #####  #    # #####  #        #
      #    # #    #   #     #   #      #####  #      #        #
      #    # #    #   #     #   #      #   #  #      #        #
      #####   ####    #     #   ###### #    # #      ######   #
                            4.9.0
  ===================================================================
hexo: 6.3.0
hexo-cli: 4.3.1
os: linux 5.15.0-60-generic Ubuntu 22.04.1 LTS 22.04.1 LTS (Jammy Jellyfish)
node: 18.16.0
acorn: 8.8.2
ada: 1.0.4
ares: 1.19.0
brotli: 1.0.9
cldr: 42.0
icu: 72.1
llhttp: 6.0.10
modules: 108
napi: 8
nghttp2: 1.52.0
nghttp3: 0.7.0
ngtcp2: 0.8.1
openssl: 3.0.8+quic
simdutf: 3.2.2
tz: 2022g
undici: 5.21.0
unicode: 15.0
uv: 1.44.2
uvwasi: 0.0.15
v8: 10.2.154.26-node.26
zlib: 1.2.13
```
