---
title: linux 命令行下配置 wlan 无线网卡
date: 2020-06-18 20:47:28
category_bar: true
tags: ['linux', 'wifi']
categories:
- 技术
- Linux
---

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2022/11/28/1024px-WiFi_Logo.svg.png)
Linux 命令行下配置连接 wlan 无线网卡具体步骤参考。

工作的大体思路如下：

- 用iwconfig开启无线网卡的电源，并查找区域内的无线网络
- 连接到相应的无线网络
- 通过ifconfig启用无线网卡，并获取IP（如果使用DHCP的话）

**注意：**

假设无线被识别为 wlan0，如果您的网卡没有被识别为 wlan0，可以在操作时做相应的修改。

**具体步骤**：

- 打开无线网卡电源

```
iwconfig wlan0 txpower on
```

- 列出区域内的无线网络

```
iwlist wlan0 scan
```

- 假设要连接到网络MyHome（即essid为MyHome的网络），那么输入命令

```
iwconfig wlan0 essid “MyHome”
```

- 如果网络是加密的，密码是0123456789，那么就输入命令

```
iwconfig wlan0 essid “MyHome” key 0123-4567-89
```

- 如果正常的话，输入

```
iwconfig wlan0
```

就可以看到连接正常的各项参数了。

- 启用无线网卡

```
ifconfig wlan0 up
```

- 如果是用 DHCP 获取 IP 的，那么用 dhclient 或 dhcpcd 获取 ip

```
dhclient wlan0
```

或

```
dhcpcd wlan0
```

- 现在无线网卡应该可以正常使用了

**注意：一定要把 NetworkManager 服务停掉**