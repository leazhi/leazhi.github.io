---
title: Openresty 系列002-测试加载 lua 脚本
author: leazhi
tags:
  - [lua]
  - [nginx]
  - [tengine]
  - [web]
categories:
  - [nginx]
date: 2023-07-26 16:55:08
cover: https://hexo.linuser.com/gallery/code/11035538.jpg
discription: 
keywords: 
password: 
message: 
---

1.编辑 /usr/local/openresty/nginx/conf/nginx.conf 文件，配置内容为：
```bash
worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    
    # 加载lua 包和 lua 的 c 包（可以不用添加的）
    # lua_package_path "/usr/local/openresty/lualib/?.lua;;";
    # lua_package_cpath "/usr/local/openresty/lualib/?.so;;";

    server {
        listen       80;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
	location /lua2 {
	    default_type "text/html";
        content_by_lua_file lua/test.lua;
	}
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```


2.在/usr/local/openresty/nginx 目录下新建 lua 目录，并在该目录下创建测试脚本文件 test.lua, 内容为：
```lua
ngx.say("This is a lua scripts")
```

3.执行命令检查 openresty 配置是否有误，无误的话直接重载 openresty 服务：
```bash
nginx -t && nginx -s reload
```

4.本地测试访问：
```bash
root@vultr:/usr/local/openresty/nginx# curl http://localhost/lua2
This is a lua scripts
```