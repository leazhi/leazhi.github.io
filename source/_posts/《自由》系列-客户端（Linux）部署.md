---
title: 《自由》系列-客户端（Linux）部署
author: leazhi
tags:
  - [v2ray]
  - [vmess]
categories:
  - [vpn]
date: 2023-09-28 20:00:36
cover: 
discription: 
keywords: 
password: 
message: 
---

## 系统环境
- OS：kali linux 2023.03
- Kernel：6.4.0-kali3-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.4.11-1kali1 (2023-08-21) x86_64 GNU/Linux

## v2ray 部署

### 安装 v2ray

1.从 [v2ray 项目](https://github.com/v2ray/v2ray-core/releases) 地址下载 v4.28.2 版本的压缩文件到 /usr/local/src/ 目录下：
```bash
sudo wget -O /usr/local/src/v2ray-linux-64.zip https://github.com/v2ray/v2ray-core/releases/download/v4.28.2/v2ray-linux-64.zip
```

2.将下载好的压缩包解压到 /usr/local/src/v2ray 目录：
```bash
sudo unzip /usr/local/src/v2ray-linux-64.zip -d /usr/local/src/v2ray
```

3.将解压处理的 v2ray 移动到 /usr/local/ 目录下：
```bash
sudo mv /usr/local/src/v2ray /usr/local/v2ray
```

4.在 /usr/local/v2ray 目录下创建 bin 目录，并将 v2ray 移动到 bin 目录下：
```bash
sudo mkdir /usr/local/v2ray/bin 
sudo mv /usr/local/v2ray/v2ray* /usr/local/v2ray/bin
sudo mv /usr/local/v2ray/geo* /usr/local/v2ray/bin
```

5.在 /usr/local/v2ray 目录下创建 etc 目录，并在该目录下创建配置文件 config.json ，配置内容如下：
```bash
sudo mkdir /usr/local/v2ray/etc
```

**/usr/local/v2ray/etc/config.json** 配置如下：
```bash
{
  "inbounds": [
    {
      "port": 1080,
      "protocol": "socks",
      "domainOverride": ["tls", "http"],
      "settings": {
        "auth": "noauth"
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "domain.com",
            "port": 443,
            "users": [
              {
                "id": "dcd1111-0237-442b-953f-9ccf84d85678",
                "alterId": 0
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "security": "tls",
        "wsSettings": {
          "path": "/"
        }
      }
    }
  ]
}
```

6.在 /lib/systemd/system/ 目录下创建 v2ray.service 文件，内容如下：
```bash
[Unit]
Description=V2Ray Service
Documentation=https://www.v2fly.org/
After=network.target nss-lookup.target

[Service]
User=nobody
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
NoNewPrivileges=true
ExecStart=/usr/local/v2ray/bin/v2ray -config /usr/local/v2ray/etc/config.json
Restart=on-failure
RestartPreventExitStatus=23

[Install]
WantedBy=multi-user.target
```

7.执行脚本重载命令加载启动脚本，并将服务设置为开机自启动：
```bash
sudo systemctl daemon-reload && sudo systemctl enable --now v2ray.service
```

## 参考：
- 1.[1.2.1. 客户端配置](https://selierlin.github.io/v2ray/basic/vmess.html)