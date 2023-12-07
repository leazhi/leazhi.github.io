---
title: 《自由》系列-Caddy2 + Vmess 部署
author: leazhi
tags:
  - [v2ray]
  - [vmess]
categories:
  - [vpn]
date: 2023-08-11 19:03:06
cover: 
discription: 
keywords: 
password: 
message: 
---

该方案是使用 Claude AI 生成的。本人没有亲测，但是大致步骤跟我自己手动配置的差不多。应该是没什么问题

## Caddy2 配置 

### Caddyfile

```json
xx.damo.com:443 {
    root * /tmp

    log {
        output file /usr/local/caddy/log/access.log
        #format single_field common_log
    }

    reverse_proxy ws 127.0.0.1:26609
    tls vnxxx@gmail.com
    tls {
        protocols tls1.2 tls1.3
    }
}

```

### Caddy2.service

启动脚本: /lib/systemd/system/caddy2.service
```bash
[Unit]
Description=Caddy 2 HTTP/2 web server
Documentation=https://caddyserver.com/docs/ 
After=network-online.target

[Service]
User=caddy
Group=caddy
ExecStart=/usr/bin/caddy run --environ --config /etc/caddy/Caddyfile
ExecReload=/usr/bin/caddy reload --config /etc/caddy/Caddyfile
TimeoutStopSec=5s
LimitNOFILE=1048576
LimitNPROC=512
PrivateTmp=true
ProtectSystem=full
AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
```

## V2ray

### Server 的配置

config.json:
```json
{
  "inbounds": [
    {
      "port": 10000,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "uuid", 
            "alterId": 64
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
          "path": "/v2ray"
        }  
      }
    }
  ],

  "outbounds": [
    {
      "protocol": "freedom"
    }
  ]
}
```

### Client 端配置：

config.json
```json
{

  "inbounds": [
    {
      "port": 1080,
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"] 
      }
    }
  ],

  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "xxx.domain.com",
            "port": 443,
            "users": [
              {
                "id": "uuid", 
                "alterId": 64
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "security": "tls",
        "wsSettings": {
          "path": "/v2ray" 
        }
      }
    }
  ]
}
```

### v2ray.service

启动脚本: /lib/systemd/system/v2ray.service
```bash
[Unit]
Description=V2Ray Service
After=network.target
Wants=network.target

[Service]
Type=simple
User=nobody
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
ExecStart=/usr/bin/v2ray -config /etc/v2ray/config.json
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```
