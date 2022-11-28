---
title: 树莓派的SWAP分区扩展
category_bar: true
date: 2020-06-27 23:12:00
tags: ['raspberry-pi', 'swap']
categories:
- 技术
- Linux
- ARM
---

## SWAP分区简介

Swap分区是磁盘上的一个特殊用途的分区。是当系统的物理内存不够用的时候，把物理内存中的一部分空间释放出来，以供当前运行的程序使用。那些被释放的空间可能来自一些很长时间没有什么操作的程序，这些被释放的空间被临时保存到Swap分区中，等到那些程序要运行时，再从Swap分区中恢复保存的数据到内存中。
分配太多的Swap空间，会浪费磁盘空间，而Swap空间太少，则系统会发生错误。一般在内存小于2G的情况下，交换分区应为内存的2倍。对于树莓派来说，可以设置为2GB。当然也需要考虑 microSD 卡容量和使用情况。



## 扩展方法

### 1.程序安装

如果系统默认没有SWAP，则需要手动建立，不过推荐这个自动设置SWAP的软件——dphys-swapfile。

安装 dphys-swapfile：

```bash
sudo apt-get install dphys-swapfile
```

*此软件会默认设置RAM两倍大小的SWAP分区*。

### 2.程序配置

```bash
sudo nano /etc/dphys-swapfile
```

将 CONF_SWAPSIZE 的值修改成你想要的大小。 一般在内存小于2G的情况下，交换分区应为内存的2倍!

### 3.重启服务

```bash
sudo /etc/init.d/dphys-swapfile restart
```

### 4.查看分区大小

```bash
free -h
```

你会看到以下内容：

```
pi@raspbian:~$ free -h
              total        used        free      shared  buff/cache   available
Mem:          1.9Gi       929Mi       681Mi       0.0Ki       288Mi       877Mi
Swap:         3.0Gi       137Mi       2.9Gi
```

SWAP即为你设置的SWAP分区信息。