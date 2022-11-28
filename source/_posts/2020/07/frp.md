---
title: frp内网穿透方案
category_bar: true
date: 2020-07-26 12:37:19
tags: ['frp', 'proxy']
categories:
- 技术
- Linux
- Usage
---

## 简介

frp 是一个高性能的反向代理应用，可以帮助您轻松地进行内网穿透，对外网提供服务，支持 tcp, http, https 等协议类型，并且 web 服务支持根据域名进行路由转发。

GitHub项目地址：[frp](https://github.com/fatedier/frp)

### frp 的作用

- 利用处于内网或防火墙后的机器，对外网环境提供 http 或 https 服务。
- 对于 http 服务支持基于域名的虚拟主机，支持自定义域名绑定，使多个域名可以共用一个80端口。
- 利用处于内网或防火墙后的机器，对外网环境提供 tcp 服务，例如在家里通过 ssh 访问处于公司内网环境内的主机。
- 可查看通过代理的所有 http 请求和响应的详细信息。（待开发）

### 开发状态

frp 目前正在前期开发阶段，master 分支用于发布稳定版本，dev 分支用于开发，您可以尝试下载最新的 release 版本进行测试。

目前的交互协议可能随时改变，不能保证向后兼容，升级新版本时需要注意公告说明。

## 使用方法

根据对应的操作系统及架构，从 [Release](https://github.com/fatedier/frp/releases) 页面下载最新版本的程序。

将 frps 及 frps.ini 放到有公网 IP 的机器上。

将 frpc 及 frpc.ini 放到处于内网环境的机器上。

### 通过 ssh 访问内网机器

1. 修改 frps.ini 文件，配置一个名为 ssh 的反向代理：

   ```ini
   # frps.ini
   [common]
   bind_port = 7000 #frp服务端口
   
   [ssh]
   listen_port = 6000 #你转发的服务在外网访问的端口
   auth_token = 123 #连接密码
   ```

2. 启动frps：

   ```bash
   ./frps -c ./frps.ini //debug时候用，直接在终端启动
   nohup ./frps -c ./frps.ini & //后台启动
   ```

3. 修改 frpc.ini 文件，设置 frps 所在服务器的 IP 为 x.x.x.x：

   ```ini
   # frpc.ini
   [common]
   server_addr = x.x.x.x
   server_port = 7000
   auth_token = 123
   
   [ssh]
   local_port = 22 
   ```

4. 启动 frpc：

   ```bash
   ./frpc -c ./frpc.ini //debug时候用，直接在终端启动
   nohup ./frpc -c ./frpc.ini & //后台启动
   ```

5. 通过 ssh 访问内网机器，假设用户名为 test：

   ```bash
   ssh -oPort=6000 test@x.x.x.x
   ```

### 通过指定域名访问部署于内网的 web 服务

有时想要让其他人通过域名访问或者测试我们在本地搭建的 web 服务，但是由于本地机器没有公网 IP，无法将域名解析到本地的机器，通过 frp 就可以实现这一功能，以下示例为 http 服务，https 服务配置方法相同， vhost_http_port 替换为 vhost_https_port， type 设置为 https 即可。

1. 修改 frps.ini 文件，配置一个名为 web 的 http 反向代理，设置 http 访问端口为 8080，绑定自定义域名 www.yourdomain.com:

   ```ini
   # frps.ini
   [common] bind_port = 7000
   vhost_http_port = 8080 #用于http服务的端口
   ```

2. 启动frps：

   ```bash
   ./frps -c ./frps.ini //debug时候用，直接在终端启动
   nohup ./frps -c ./frps.ini & //后台启动
   ```

3. 修改 frpc.ini 文件，设置 frps 所在的服务器的 IP 为 x.x.x.x，local_port 为本地机器上 web 服务对应的端口：

   ```ini
   # frpc.ini
   [common]
   server_addr = x.x.x.x
   server_port = 7000
   auth_token = 123
   
   [web1]
   type = http
   local_port = 80
   custom_domains = www.yourdomain1.com #自定义域名，需要备案且解析到服务器。
   
   [web2] #当有多个web服务时
   type = http
   local_port = 81
   custom_domains = www.yourdomain2.com #自定义域名，需要备案且解析到服务器。
   ```

4. 启动 frpc：

   ```bash
   ./frpc -c ./frpc.ini //debug时候用，直接在终端启动
   nohup ./frpc -c ./frpc.ini & //后台启动
   ```

5. 将 [www.yourdomain.com](http://www.yourdomain.com/) 的域名 A 记录解析到 x.x.x.x，如果服务器已经有对应的域名，也可以将 CNAME 记录解析到服务器原先的域名。

6. 通过浏览器访问 http://www.yourdomain1.com:8080 即可访问到处于内网机器上的 web1 服务。

   通过浏览器访问 http://www.yourdomain2.com:8080 即可访问到处于内网机器上的 web2 服务。