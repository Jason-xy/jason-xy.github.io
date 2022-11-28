---
title: 树莓派安装raspi-config扩展SD卡空间
category_bar: true
date: 2020-08-13 13:28:54
tags: ['raspberry-pi']
categories:
- 技术
- Linux
- ARM
---

在进行树莓派img文件备份还原的时候往往会由于备份设备的SD卡的分区大小，限制输入新的设备后的SD卡空间大小。一般来说扩展空间到整张SD卡可以自行手动采用fdisk工具进行修改，不过过程相对繁琐。这里不妨借助raspi-config工具进行一键修改。

## 原生Raspbian系统

输入命令

```bash
sudo raspi-config --expand-rootfs
```

然后重启即可

```bash
reboot
```

## 非原生系统

### 安装raspi-config

1. 手动下载deb包

   ```bash
   wget http://mirrors.ustc.edu.cn/archive.raspberrypi.org/pool/main/r/raspi-config/raspi-config_20171201_all.deb
   ```

2. 解决依赖问题

   ```bash
   sudo apt install whiptail parted lua5.1 alsa-utils psmisc
   ```

3. 安装软件

   ```bash
   dpkg -i raspi-config_20171201_all.deb
   ```

### 扩展空间

运行raspi-config，需要sudo或以上权限

```bash
sudo raspi-config --expand-rootfs
```

然后重启即可

```bash
reboot
```

