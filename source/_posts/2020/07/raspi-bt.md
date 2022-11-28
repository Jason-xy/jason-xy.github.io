---
title: 树莓派部署宝塔面板+LAMP
category_bar: true
date: 2020-07-21 11:27:59
tags: ['raspberry-pi', 'web']
categories:
- 技术
- Web
---

树莓派4的64位操作系统现在慢慢发展起来了，终于可以安装现在只支持64位的宝塔面板了，同时也解决了部分Web组件无法安装的问题，对于我这种不想总是动手敲命令行的人来说有个可视化的Web管理界面还是非常舒服的！

先上图：

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/raspi_BT/5.png" style="zoom:80%;" />

## 基础软硬件

Raspberry Pi 4b 2G RAM

32GB Class10 TF Card（32GB大小比较合适，后面有用）

OS:[Debian-Pi-Aarch64](https://github.com/openfans-community-offical/Debian-Pi-Aarch64) ，Ubuntu官方也出树莓派4的64位操作系统了，有兴趣可以试试。

## 面板安装

**Debian**安装命令：

```bash
wget -O install.sh http://download.bt.cn/install/install-ubuntu_6.0.sh && bash install.sh
```

更多的操作系统支持请参考官方信息：[宝塔面板安装](https://www.bt.cn/bbs/thread-19376-1-1.html)

经过漫长的等待，如果看到以下内容就说明安装好了：

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/raspi_BT/1.png" style="zoom:100%;" />

如果是在内网安装的话**IP地址是错的**根据自己的真实IP地址进入登录端口。

**成功登陆后**进入面板的设置界面修改以下信息（为安全考虑）：

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/raspi_BT/2.png" style="zoom:100%;" />

服务器部分端口**默认关闭**根据需要可以去安全里面**放行端口**。

到这里面板的安装和设置基本上就完成了。

## LAMP安装

### SWAP分区扩展(划重点！！！)

由于树莓派4的arm64构架，导致没有现成的二进制文件可以运行，所以组件安装都需要自己在本机编译！

如果不修改SWAP分区编译过程中**100%爆内存**！！！

详细步骤参考：[SWAP分区扩展教程](https://jason-xy.cn/2020/06/raspi_swap/)

我给的SWAP空间超级多，上次编译内存直接飙到6G吓到我了。

```bash
root@raspbian:/home/pi# free -h
              total        used        free      shared  buff/cache   available
Mem:          1.9Gi       354Mi       536Mi        34Mi       1.0Gi       1.4Gi
Swap:         7.0Gi        27Mi       7.0Gi
```

### LAMP安装

直接在宝塔面板可视化安装没什么可以讲的，但是一定记得点**编译安装**。

<img align="center" src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/raspi_BT/4.png" style="zoom:80%;" />

另外安装SQL时，如果选择mysql-mariadb_10.4时会要求内存2048MB以上，2G版本的树莓派刚好卡在了门槛，装个mysql-mariadb_10.3也没啥问题。

剩下的就是漫长的等待了，不骗你，真的超级漫长！

祝大家中途别遇到奇奇怪怪的问题🤭