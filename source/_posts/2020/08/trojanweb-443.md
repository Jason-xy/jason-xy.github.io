---
title: Trojan+web 443端口复用配置
category_bar: true
date: 2020-08-11 15:28:28
tags: ['trojan', 'proxy']
categories:
- 生活
- IT小技巧
---

## 准备

1、VPS一台内存最好1GB左右，重置任意可安装宝塔面板的系统（建站推荐CentOS7X以上，或者是CentOS8X）

2、一级域名一个做好相应的解析（今天作者解析的域名为：jason-xy.online | www.jason-xy.online）

（分别解析主域名和www的二级域名到VPS主机。）

## 预期效果

我们访问 jason-xy.online 及 www.jason-xy.online 均能使用 https 的方式，也就是正常访问我们的博客。

使用Trojan客户端连接 www.jason-xy.online 可以正常科学上网。

## 开始操作

### 1、连接VPS并安装宝塔面板

以下一键安装宝塔面板的代码仅仅局限CentOS系统的用户，其他用户请访问 [这里](https://www.v2rayssr.com/go?url=https://bt.cn/bbs/thread-19376-1-1.html) 查看安装命令

```bash
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.shCOPY
```

安装完毕以后，请自行记录宝塔面板的登录地址，并登录宝塔面板。

### 2、安装建站需要的必要插件

登录以后，在弹出的窗口环境中选择自己需要安装的环境。

（若是使用 WordPress 的博客，请依次安装 Nginx1.18、MySQL5.6、PHP7.4，其他非必要环境作者不想安装就略过了）

### 3、创建网站

因为作者使用 WordPress 博客的 CMS，所以选择宝塔的一键部署。

分别点击 “软件商店” – “一键部署” ，找到 WordPress 并选择一键部署，填写我们刚才解析好的两个域名。

### 4、设置网站数据库并配置网站

部署完毕以后，输入我们绑定的域名进行数据库配置及网站设置。

### 5、修改默认的 Nginx 配置

找到 “软件商店” – “已安装” – “Nginx1.18”，设置 Nginx 的配置信息

在 http 模块前面增加如下代码，按照自己的需求进行更改：

```json
stream {
    # 这里就是 SNI 识别，将域名映射成一个配置名
    map $ssl_preread_server_name $backend_name {
        jason-xy.online web;
        www.jason-xy.online trojan;
    # 域名都不匹配情况下的默认值
        default web;
    }

    # web，配置转发详情
    upstream web {
        server 127.0.0.1:4433;
    }

    # trojan，配置转发详情
    upstream trojan {
        server 127.0.0.1:10110;
    }

    # 监听 443 并开启 ssl_preread
    server {
        listen 443 reuseport;
        listen [::]:443 reuseport;
        proxy_pass  $backend_name;
        ssl_preread on;
    }
}COPY
```

### 6、在线申请 SSL 证书

在网站设置里面，勾选两个绑定的域名，并为其申请 SSL 证书和开启强制 Https 的访问。

### 7、修改网站的Nginx配置文件

在网站设置里面开启 “伪静态” 为 WordPress ，然后转到配置文件设置。

删除在 server 模块下面的 server_name 里面的 二级域名，只保留主域名。

在原有的 server 模块下面增加如下代码（请自行替换域名）

```json
server
{
    listen 10111;
    server_name www.jason-xy.online;
    location / {

        if ($http_host !~ "^jason-xy.online$") {
          rewrite  ^(.*)    https://jason-xy.cn$1 permanent;
        }

       if ($server_port !~ 4433){
        rewrite ^(.*)   https://jason-xy.cn$1 permanent;
    }

        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    }
    access_log logs/aaa.com_access.log;
}COPY
```

**更改完毕以后，请回到 Nginx 设置界面，重启 Nginx 服务。**

### 8、安装官方Trojan服务

```bash
sudo bash -c "$(curl -fsSL https://raw.githubusercontent.com/trojan-gfw/trojan-quickstart/master/trojan-quickstart.sh)"COPY
```

设置Trojan开启自动启动

```bash
systemctl enable trojan   #设置Trojan开启自动启动COPY
```

### 9、修改Trojan配置文件

找到VPS以下文件 /usr/local/etc/trojan/config.json 修改为如下代码：（自行更改密码和域名证书路径）

```json
{
    "run_type": "server",
    "local_addr": "127.0.0.1",
    "local_port": 10110,
    "remote_addr": "127.0.0.1",
    "remote_port": 10111,
    "password": [
        "321321321"
    ],
    "log_level": 1,
    "ssl": {
        "cert": "/www/server/panel/vhost/cert/jason-xy.online/fullchain.pem",
        "key": "/www/server/panel/vhost/cert/jason-xy.online/privkey.pem",
        "key_password": "",
        "cipher": "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384",
        "cipher_tls13": "TLS_AES_128_GCM_SHA256:TLS_CHACHA20_POLY1305_SHA256:TLS_AES_256_GCM_SHA384",
        "prefer_server_cipher": true,
        "alpn": [
            "http/1.1"
        ],
        "alpn_port_override": {
            "h2": 81
        },
        "reuse_session": true,
        "session_ticket": false,
        "session_timeout": 600,
        "plain_http_response": "",
        "curves": "",
        "dhparam": ""
    },
    "tcp": {
        "prefer_ipv4": false,
        "no_delay": true,
        "keep_alive": true,
        "reuse_port": false,
        "fast_open": false,
        "fast_open_qlen": 20
    },
    "mysql": {
        "enabled": false,
        "server_addr": "127.0.0.1",
        "server_port": 3306,
        "database": "trojan",
        "username": "trojan",
        "password": "",
        "key": "",
        "cert": "",
        "ca": ""
    }
}COPY
```

更改完毕以后，上传并保存，然后重启Trojan服务

```bash
systemctl restart trojanCOPY
```

### 10、搭建和设置完毕

现在，你就可以连接你的Trojan节点了，网站也可以正常的访问了。