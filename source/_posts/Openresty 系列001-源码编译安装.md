---
title: Openresty 系列001-源码编译安装
author: leazhi
tags:
  - [lua]
  - [nginx]
  - [tengine]
  - [web]
categories:
  - [nginx]
date: 2023-07-26 15:12:24
cover: https://hexo.linuser.com/gallery/code/11035538.jpg
discription: 
keywords: 
password: 
message: 
---

{% note default simple %}
OpenResty® 是一个基于 Nginx 与 Lua 的高性能 Web 平台，其内部集成了大量精良的 Lua 库、第三方模块以及大多数的依赖项。用于方便地搭建能够处理超高并发、扩展性极高的动态 Web 应用、Web 服务和动态网关。

OpenResty® 通过汇聚各种设计精良的 Nginx 模块（主要由 OpenResty 团队自主开发），从而将 Nginx 有效地变成一个强大的通用 Web 应用平台。这样，Web 开发人员和系统工程师可以使用 Lua 脚本语言调动 Nginx 支持的各种 C 以及 Lua 模块，快速构造出足以胜任 10K 乃至 1000K 以上单机并发连接的高性能 Web 应用系统。

OpenResty® 的目标是让你的Web服务直接跑在 Nginx 服务内部，充分利用 Nginx 的非阻塞 I/O 模型，不仅仅对 HTTP 客户端请求,甚至于对远程后端诸如 MySQL、PostgreSQL、Memcached 以及 Redis 等都进行一致的高性能响应。
{% endnote %}
{% note success simple %}
**系统环境：Debian 12 ,内核版本6.1.0-9-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.27-1 (2023-05-08) x86_64 GNU/Linux**
{% endnote %}


## 安装必要依赖的包：
```bash
apt install -y autoconf automake libtool libmaxminddb-dev libxslt1-dev libgd-dev libgeoip-dev libgoogle-perftools-dev  libunwind-dev        
```

## 下载解压：openssl-1.1.1u
从 [openssl 官方站点](https://www.openssl.org/) 下载源码包到 /usr/local/src/ 并解压到下载目录 /usr/local/src/
```bash
# 下载
wget -O /usr/local/src/openssl-1.1.1u.tar.gz https://www.openssl.org/source/openssl-1.1.1u.tar.gz

# 解压
tar -zxf  /usr/local/src/openssl-1.1.1u.tar.gz -C  /usr/local/src/

# 可以不用执行下面的安装
# cd openssl-1.1.1u
# ./config --prefix=/usr/local/openssl
# make
# make install
```

## 下载解压：pcre（非 pcre2）
从 [pcre 官方站点](http://www.pcre.org/)下载源码包到 /usr/local/src/ 并解压到下载目录 /usr/local/src/
```bash
# 下载
wget -O /usr/local/src/pcre-8.45.tar.gz https://udomain.dl.sourceforge.net/project/pcre/pcre/8.45/pcre-8.45.tar.gz

# 解压
tar -zxf /usr/local/src/pcre-8.45.tar.gz -C /usr/local/src/
```

## 下载解压：zlib
从 [zlib 官方站点](https://www.zlib.net/)下载源码包到 /usr/local/src/ 并解压到下载目录 /usr/local/src/
```bash
# 下载
wget -O /usr/local/src/zlib-1.2.13.tar.gz https://zlib.net/zlib-1.2.13.tar.gz

# 解压
tar -zxf /usr/local/src/zlib-1.2.13.tar.gz -C /usr/local/src/
```

## 安装 luajit2 及其它 lua 组件

{% note warning simple %}
**这里的 luajit2、lua-resty-core 以及 lua-resty-lrucache 都安装到同一个目录
{% endnote %}
### 下载安装：luajit2
```bash
# 这里使用 openresty 提供的 luajit2
git clone https://github.com/openresty/luajit2.git  /usr/local/src/luajit2

# 进入到下载目录：
cd  /usr/local/src/luajit2

# 执行 make install 指定路径安装
make install PREFIX=/usr/local/LuaJIT2
```

### 下载安装：
```bash
# 这里使用 openresty 提供的 lua-resty-core
git clone https://github.com/openresty/lua-resty-core.git /usr/local/src/lua-resty-core

# 进入到下载目录：
cd  /usr/local/src/lua-resty-core

# 执行 make install 指定路径安装
make install PREFIX=/usr/local/LuaJIT2
```

### 下载安装：
```bash
# 这里使用 openresty 提供的 lua-resty-lrucache
git clone https://github.com/openresty/lua-resty-lrucache.git /usr/local/src/lua-resty-lrucache

# 进入到下载目录：
cd  /usr/local/src/lua-resty-lrucache

# 执行 make install 指定路径安装
make install PREFIX=/usr/local/LuaJIT2
```

最后，将 luajit2 模块软链接到 /usr/lib/ 及 /usr/lib64/ 目录：
```bash
ln -s /usr/local/LuaJIT2/lib/libluajit-5.1.so.2.1.0 /usr/lib/libluajit-5.1.so.2
ln -s /usr/local/LuaJIT2/lib/libluajit-5.1.so.2.1.0 /usr/lib64/libluajit-5.1.so.2
```

## 下载安装：jemalloc
从 github 上同步 jemalloc 源码到 /usr/local/src/ 目录下并执行 ./autogen.sh 生成 configure 文件
```bash
# 同步的代码
git clone https://github.com/jemalloc/jemalloc.git /usr/local/src/jemalloc

# 进入同步目录
cd !$

# 生成 configure 文件
./autogen.sh
```

## 下载安装：libatomic:

从 github 上同步 libatomic_ops 源码到 /usr/local/src/ 目录下并执行 ./autogen.sh 生成 configure 文件
```bash
# 同步的代码
git clone https://github.com/ivmai/libatomic_ops.git /usr/local/src/libatomic_ops

# 进入同步目录
cd /usr/local/src/libatomic_ops

# 生成 configure 文件
./autogen.sh

# 做库文件的软链接
ln -s /usr/local/src/libatomic_ops/src/.libs/libatomic_ops.a /usr/local/src/libatomic_ops/src/
```

## 下载目录索引模块：ngx-fancyindex
从 github 上同步 ngx-fancyindex 源码到 /usr/local/src/ 目录下
```bash
git clone https://github.com/aperezdc/ngx-fancyindex.git /usr/local/src/ngx-fancyindex
```

## 下载IP定位模块：ngx_http_geoip2_module
从 github 上同步 ngx_http_geoip2_module 源码到 /usr/local/src/ 目录下
```bash
git clone https://github.com/leev/ngx_http_geoip2_module.git /usr/local/src/ngx_http_geoip2_module
```

## 下载安装：openresty-1.21.4.2

从 [openresty 官方站点](https://openresty.org/cn/download.html) 下载源码包到 /usr/local/src/ 并安装到 /usr/local/openresty 目录下：

```bash
# 下载 openresty 源码包到 /usr/local/src/ 目录下：
wget -O /usr/local/src/openresty-1.21.4.2.tar.gz https://openresty.org/download/openresty-1.21.4.2.tar.gz

# 解压源码包到下载目录：
tar -zxf /usr/local/src/openresty-1.21.4.2.tar.gz -C /usr/local/src/

# 进入解压目录：
cd /usr/local/src/openresty-1.21.4.2

# 添加运行 tengine 的用户：
useradd -s /sbin/nologin nginx

# 配置编译参数：
./configure --prefix=/usr/local/openresty \
--user=nginx \
--group=nginx \
--with-luajit=/usr/local/LuaJIT2 \
--with-http_ssl_module \
--with-http_v2_module \
--with-http_realip_module \
--with-http_addition_module \
--with-http_xslt_module \
--with-http_image_filter_module \
--with-http_geoip_module \
--with-http_sub_module \
--with-http_dav_module \
--with-http_flv_module \
--with-http_mp4_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_auth_request_module \
--with-http_random_index_module \
--with-http_secure_link_module \
--with-http_degradation_module \
--with-http_slice_module \
--with-http_stub_status_module \
--http-log-path=/usr/local/openresty/logs/access.log \
--http-client-body-temp-path=/usr/local/openresty/tmp_client_body \
--http-proxy-temp-path=/usr/local/openresty/temp_proxy \
--with-mail \
--with-mail_ssl_module \
--with-stream \
--with-stream_ssl_module \
--with-stream_realip_module \
--with-stream_geoip_module \
--with-stream_ssl_preread_module \
--with-google_perftools_module \
--with-compat \
--with-pcre=/usr/local/src/pcre-8.45 \
--with-zlib=/usr/local/src/zlib-1.2.13 \
--with-libatomic=/usr/local/src/libatomic_ops \
--with-openssl=/usr/local/src/openssl-1.1.1u \
--add-module=/usr/local/src/ngx-fancyindex \
--add-module=/usr/local/src/ngx_http_geoip2_module 

# 编译
gmake

# 安装 
gmake install

# 在 /lib/systemd/system/ 目录下创建 openresty 启动脚本文件 openresty.Service，内容如下：

[Unit]
Description=openresty
After=syslog.target network.target

[Service]
Type=forking
ExecStart=/usr/local/openresty/nginx/sbin/nginx
ExecReload=/usr/local/openresty/nginx/sbin/nginx -s reload
ExecStop=/usr/local/openresty/nginx/sbin/nginx -s quit
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target

# 将 /usr/local/openresty/nginx/sbin/nginx 软链接到 /usr/sbin/ 目录下
ln -s /usr/local/openresty/nginx/sbin/nginx /usr/sbin/

# 检查 nginx 默认配置是否有误：
root@vultr:/usr/local/src/openresty-1.21.4.2# nginx -t
nginx: the configuration file /usr/local/openresty/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/openresty/nginx/conf/nginx.conf test is successful


# 启动 openresty.service
root@vultr:/usr/local/src/openresty-1.21.4.2# systemctl start openresty.service

# 查看是否有监听 80 端口：
root@vultr:/usr/local/src/openresty-1.21.4.2# ss -lnpt |egrep 80
LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=29473,fd=8),("nginx",pid=29472,fd=8))
```



