---
title: 树莓派系统镜像烧写
category_bar: true
date: 2020-06-24 18:14:24
tags: ['raspberry-pi', 'linux']
categories:
- 技术
- Linux
- ARM
---
<div align=center>
<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_os/raspi1.jpeg" style="zoom: 120%;" />
</div>

## 镜像准备

可以适用于树莓派的系统镜像资源十分丰富，根据个人的需求有不同的系统镜像选择，正如我们之前提到的*树莓派资源大全*里面丰富的镜像资源一样！所以先选一个你喜欢的镜像吧！[Let's Go!](https://jason-xy.cn/2020/06/21/raspi_source/)
<div align=center>
<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_os/raspi_debian.jpg" style="zoom: 25%;" />
</div>
## 烧录工具

之前看到不少教程都推荐[Win32 Disk Imager](https://sourceforge.net/projects/win32diskimager/)但是限于平台限制和“极具年代感”的UI设计，我们今天推荐另一款**开源、全平台**烧录工具——**[BalenaEtcher](https://github.com/balena-io/etcher)**
<div align=center>
<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_os/etcher.png" style="zoom: 50%;" />
</div>
有条件的小伙伴可以到[官网](https://www.balena.io/etcher/)下载。

没条件的话可以在本页面下载（博主要按流量付费的😭）：[Windows](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_os/balenaEtcher-Portable-1.5.100.exe)、[Linux](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_os/balena-etcher-electron-1.5.100-linux-ia32.zip)、[macOS](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_os/balenaEtcher-1.5.100.dmg)

## 烧录流程

1. TF卡插读卡器接上电脑

2. **管理员身份**运行Etcher

3. 选择镜像（img格式文件）=>选择烧写磁盘(会格式化掉所有内容)=>Flash!

## 后续技巧

1. 如果采用的的是Raspbain,在boot分区下事先添加ssh和wpa_supplicant.conf ,可以在开机之前连接WiFi并开启ssh。

2. wpa_supplicant.conf 配置详情

   ```
   country=CN
   ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
   update_config=1
    
   network={
   ssid="WiFi-A"
   psk="12345678"
   key_mgmt=WPA-PSK
   priority=1
   }
    
   network={
   ssid="WiFi-B"
   psk="12345678"
   key_mgmt=WPA-PSK
   priority=2
   scan_ssid=1
   }
   ```

   说明以及不同安全性的 WiFi 配置示例：
   **#ssid:网络的ssid**
   **#psk:密码**
   **#priority:连接优先级，数字越大优先级越高（不可以是负数）**
   **#scan_ssid:连接隐藏WiFi时需要指定该值为1**

   如果你的 WiFi 没有密码

   ```
   network={
   ssid="你的无线网络名称（ssid）"
   key_mgmt=NONE
   }
   ```

   如果你的 WiFi 使用WEP加密

   ```
   network={
   ssid="你的无线网络名称（ssid）"
   key_mgmt=NONE
   wep_key0="你的wifi密码"
   }
   ```

   如果你的 WiFi 使用WPA/WPA2加密

   ```
   network={
   ssid="你的无线网络名称（ssid）"
   key_mgmt=WPA-PSK
   psk="你的wifi密码"
   }
   ```

3. ssh配置详情

   如果通过 ssh 连接树莓派出现 Access denied 这个提示则说明 ssh 服务没有开启。要手动开启的话，和 WiFi 配置相似，同样在 boot 分区新建一个文件，空白的即可，文件命名为 ssh。注意要小写且不要有任何扩展名。
   树莓派在启动之后会在检测到这个文件之后自动启用 ssh 服务。随后即可通过登录路由器找到树莓派的 IP 地址，通过 ssh 连接到树莓派了。