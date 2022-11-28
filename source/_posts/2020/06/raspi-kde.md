---
title: 树莓派的KDE桌面安装
category_bar: true
date: 2020-06-27 23:54:47
tags: ['raspberry-pi', 'desktop']
categories:
- 技术
- Linux
- ARM
---

觉得Raspbian的桌面太丑了，换一个KDE Plasma上去，赏心悦目。

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_kde/ked.webp" style="zoom:80%;" /></div>

## 方案一：Ubuntu测试成功

### (可选)添加PPA源

```bash
sudo add-apt-repository ppa:kubuntu-ppa/backports
```

### 更新源并更新程序

```bash
sudo apt-get update && sudo apt-get upgrade -y
```

### 修改lightdm配置

```bash
sudo nano /etc/lightdm/lightdm.conf
```

内容修改为：

```
[Seat:*]
autologin-guest=false
#autologin-user=pi
#autologin-user-timeout=0
greeter-session=lightdm-kde-greeter
user-session=kde-plasma-kf5
```

### 重启即为Plasma桌面

## 方案二：Debian 10(桌面版)测试成功

从Debian 10（Buster）安装KDE桌面，安装期间添加了官方上游存储库，运行：

```bash
sudo apt -y install task-kde-desktop
```

安装完成，注销或重启后开始使用Debian 10 Buster上的KDE桌面环境：

```bash
logout 或者 reboot
```

使用登录屏幕上的桌面环境切换器选择KDE Plasma：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_kde/kde1.png" style="zoom:80%;" /></div>