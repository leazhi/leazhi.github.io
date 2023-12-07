---
title: Tengine 系列001：源码编译安装 tengine-3.0.0
author: leazhi
tags:
  - [lua]
  - [nginx]
  - [tengine]
  - [web]
categories:
  - [nginx]
date: 2023-07-25 20:06:37
cover: https://hexo.linuser.com/gallery/code/11035538.jpg
discription: 
keywords: web,nginx,tengine,linux,ubuntu
password: 
message: 
---

{% note success simple %}
**系统环境：Ubuntu 22.10 ,内核版本 5.19.0-45-generi**
{% endnote %}

## 依赖环境下载安装：

### 安装必要依赖的包：
```bash
apt install -y autoconf automake libtool libmaxminddb-dev libxslt1-dev libgd-dev libgeoip-dev libgoogle-perftools-dev  libunwind-dev        
```

### 下载解压：openssl-1.1.1u
从 [openssl 官方站点](https://www.openssl.org/) 下载源码包到 /usr/local/src/ 并解压到下载目录 /usr/local/src/
```bash
# 下载
wget -O /usr/local/src/openssl-1.1.1u.tar.gz https://www.openssl.org/source/openssl-1.1.1u.tar.gz

# 解压
tar -zxf  /usr/local/src/openssl-1.1.1u.tar.gz -C  /usr/local/src/

# 可以不用执行下面的安装
cd openssl-1.1.1u
# ./config --prefix=/usr/local/openssl
# make
# make install
```

### 下载解压：pcre（非 pcre2）
从 [pcre 官方站点](http://www.pcre.org/)下载源码包到 /usr/local/src/ 并解压到下载目录 /usr/local/src/
```bash
# 下载
wget -O /usr/local/src/pcre-8.45.tar.gz https://udomain.dl.sourceforge.net/project/pcre/pcre/8.45/pcre-8.45.tar.gz

# 解压
tar -zxf /usr/local/src/pcre-8.45.tar.gz -C /usr/local/src/
```

### 下载解压：zlib
从 [zlib 官方站点](https://www.zlib.net/)下载源码包到 /usr/local/src/ 并解压到下载目录 /usr/local/src/
```bash
# 下载
wget -O /usr/local/src/zlib-1.2.13.tar.gz https://zlib.net/zlib-1.2.13.tar.gz

# 解压
tar -zxf /usr/local/src/zlib-1.2.13.tar.gz -C /usr/local/src/
```

### 下载安装：lua-5.4.6
从 [lua 官方站点](http://www.lua.org/)下载 lua 的源码包到 /usr/local/src/ 目录下并安装到 /usr/local/lua 目录：
```bash
# 下载源码文件：
wget -O /usr/local/src/lua-5.4.6.tar.gz http://www.lua.org/ftp/lua-5.4.6.tar.gz

# 解压到下载目录：
tar -zxf /usr/local/src/lua-5.4.6.tar.gz -C /usr/local/src/

# 进入解压目录：
cd /usr/local/src/lua-5.4.6/

# 安装前的测试：
make all test

# 安装到 /usr/local/lua 目录里
make install INSTALL_TOP=/usr/local/lua
```

### 安装 luajit2 及其它 lua 组件

{% note warning simple %}
**这里的 luajit2、lua-resty-core 以及 lua-resty-lrucache 都安装到同一个目录**
{% endnote %}
#### 下载安装：luajit2
```bash
# 这里使用 openresty 提供的 luajit2
git clone https://github.com/openresty/luajit2.git  /usr/local/src/luajit2

# 进入到下载目录：
cd  /usr/local/src/luajit2

# 执行 make install 指定路径安装
make install PREFIX=/usr/local/LuaJIT2
```

#### 下载安装：
```bash
# 这里使用 openresty 提供的 lua-resty-core
git clone https://github.com/openresty/lua-resty-core.git /usr/local/src/lua-resty-core

# 进入到下载目录：
cd  /usr/local/src/lua-resty-core

# 执行 make install 指定路径安装
make install PREFIX=/usr/local/LuaJIT2
```

#### 下载安装：
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

### 下载安装：jemalloc
从 github 上同步 jemalloc 源码到 /usr/local/src/ 目录下并执行 ./autogen.sh 生成 configure 文件
```bash
# 同步的代码
git clone https://github.com/jemalloc/jemalloc.git /usr/local/src/jemalloc

# 进入同步目录
cd !$

# 生成 configure 文件
./autogen.sh
```

### 下载安装：libatomic:

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

### 下载目录索引模块：ngx-fancyindex
从 github 上同步 ngx-fancyindex 源码到 /usr/local/src/ 目录下
```bash
git clone https://github.com/aperezdc/ngx-fancyindex.git /usr/local/src/ngx-fancyindex
```

### 下载IP定位模块：ngx_http_geoip2_module
从 github 上同步 ngx_http_geoip2_module 源码到 /usr/local/src/ 目录下
```bash
git clone https://github.com/leev/ngx_http_geoip2_module.git /usr/local/src/ngx_http_geoip2_module
```

## 下载安装：Tengine-3.0.0

1.从 [tengine 官方站点](http://tengine.taobao.org/) 下载 tengine 源码包到 /usr/local/src/ 目录下：
```bash
wget -O /usr/local/src/tengine-3.0.0.tar.gz http://tengine.taobao.org/download/tengine-3.0.0.tar.gz
```

2.将下载好的源码包解压到下载目录：
```bash
tar -zxf /usr/local/src/tengine-3.0.0.tar.gz -C /usr/local/src/
```

3.进入解压目录：
```bash
cd /usr/local/src/tengine-3.0.0
```

4.添加运行 tengine 的用户：
```bash
useradd -s /sbin/nologin nginx
```

5.配置编译参数：
```bash
./configure   \
--prefix=/usr/local/nginx \
--modules-path=/usr/local/nginx/modules \
--pid-path=/usr/local/nginx/logs/nginx.pid \
--error-log-path=/usr/local/nginx/logs/error.log \
--with-poll_module \
--with-threads \
--with-file-aio \
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
--with-http_lua_module \
--http-log-path=/usr/local/nginx/logs/access.log \
--http-client-body-temp-path=/usr/local/nginx/body_tmp/ \
--http-proxy-temp-path=/usr/local/nginx/proxy_tmp/ \
--with-stream \
--with-stream_ssl_module \
--with-stream_realip_module \
--with-stream_geoip_module \
--with-stream_ssl_preread_module \
--with-stream_sni \
--with-google_perftools_module \
--with-cpp_test_module \
--with-luajit-lib=/usr/local/LuaJIT2/lib \
--with-luajit-inc=/usr/local/LuaJIT2/include/luajit-2.1 \
--with-lua-lib=/usr/local/lua/lib \
--with-lua-inc=/usr/local/lua/include \
--with-pcre=/usr/local/src/pcre-8.45/ \
--with-zlib=/usr/local/src/zlib-1.2.13/ \
--with-openssl=/usr/local/src/openssl-1.1.1u \
--with-jemalloc=/usr/local/src/jemalloc \
--with-libatomic=/usr/local/src/libatomic_ops \
--with-debug \
--without-http_upstream_keepalive_module \
--with-mail=dynamic \
--with-mail_ssl_module \
--add-module=./modules/ngx_backtrace_module \
--add-module=./modules/ngx_http_concat_module \
--add-module=./modules/ngx_http_footer_filter_module \
--add-module=./modules/ngx_http_proxy_connect_module \
--add-module=./modules/ngx_http_reqstat_module \
--add-module=./modules/ngx_http_slice_module \
--add-module=./modules/ngx_http_sysguard_module \
--add-module=./modules/ngx_http_trim_filter_module \
--add-module=./modules/ngx_http_upstream_check_module \
--add-module=./modules/ngx_http_upstream_consistent_hash_module \
--add-module=./modules/ngx_http_upstream_keepalive_module \
--add-module=./modules/ngx_http_upstream_session_sticky_module \
--add-module=./modules/ngx_http_upstream_vnswrr_module \
--add-module=./modules/ngx_http_user_agent_module \
--add-module=./modules/ngx_multi_upstream_module \
--add-dynamic-module=/usr/local/src/ngx_http_geoip2_module \
--add-module=/usr/local/src/ngx-fancyindex
```

6.编译：
```bash
make -j $(nproc)
```

7.安装：
```bash
make install
```
8.将 nginx 可执行文件软链接到 /usr/sbin/ 目录下：
```bash
ln -s /usr/local/nginx/sbin/nginx /usr/sbin/
```

9.创建 tengine 启动脚本文件 /lib/systemd/system/nginx.service ，内容为：
```bash
[Unit]
Description=Nginx
After=syslog.target network.target

[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s quit
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

10.启动 nginx 并设置为开机启动
```bash
systemctl enable --now nginx.service
```

## 安装过程中的报错：

### 错误一：nginx -t 报：

```bash
root@vultr:/usr/local/src/tengine-3.0.0# nginx -t
nginx: [warn] could not build optimal variables_hash, you should increase either variables_hash_max_size: 1024 or variables_hash_bucket_size: 64; ig
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```

**解决方法：**

编辑 nginx 主配置文件，在 http{...} 中添加(注意：不能添加到 server{...})：
```bash
    variables_hash_max_size 4096;
    variables_hash_bucket_size 2048;
```

### 错误二：无法启动 nginx ,报：

```bash
2023/07/26 10:25:01 [alert] 345323#345323: failed to load the 'resty.core' module (https://github.com/openresty/lua-resty-core); ensure you are using an OpenResty release from https://openresty.org/en/download.html (reason: module 'resty.core' not found:
	no field package.preload['resty.core']
	no file '../lua-resty-core/lib/resty/core.lua'
	no file '../lua-resty-lrucache/lib/resty/core.lua'
	no file './resty/core.lua'
	no file '/usr/local/share/luajit-2.1.0-beta3/resty/core.lua'
	no file '/usr/local/share/lua/5.1/resty/core.lua'
	no file '/usr/local/share/lua/5.1/resty/core/init.lua'
	no file './resty/core.so'
	no file '/usr/local/lib/lua/5.1/resty/core.so'
	no file '/usr/local/lib/lua/5.1/loadall.so'
	no file './resty.so'
	no file '/usr/local/lib/lua/5.1/resty.so'
	no file '/usr/local/lib/lua/5.1/loadall.so') in /usr/local/nginx/conf/nginx.conf:137
```

**解决方法：**

根据提示，
```bash
cp -r /usr/local/LuaJIT2/lib/lua/resty /usr/local/share/luajit-2.1.0-beta3/
```