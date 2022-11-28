---
title: Ras-Pi的VPN设置
date: 2020-06-18 20:38:22
category_bar: true
tags: ['raspberry-pi', 'vpn']
categories:
- 技术
- Linux
- ARM
---

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/rasp-pi_vpn/86d8d8ae30cd6d7043056c6766004a68_waifu2x_4x_2n_jpg-768x699.png)
## 基于PPTP协议的VPN连接

### 前提条件：

1. 拥有一台已经配置好的PPTP服务器
2. 可以正常使用的任意Linux发行版（Debian为例）

### 操作方法：

- 安装PPTP-Linux客户端

```
sudo apt-get install -y pptp-linux
```

- 配置客户端信息

```
sudo vim /etc/ppp/peers/pptpconf

// 添加如下内容：（自行更改IP, name, password）
pty pptp xxx.xxx.xxx.xxx --nolaunchpppd
name xxx
password xxx
remotename PPTP
require-mppe-128
require-mschap-v2
refuse-eap
refuse-pap
refuse-chap
refuse-mschap
noauth
persist
maxfail 0
defaultroute
replacedefaultroute
usepeerdns
```

- 启动和关闭

```
pptp 客户端操作： 
启动：pon pptpconf 
关闭：poff pptpconf
```