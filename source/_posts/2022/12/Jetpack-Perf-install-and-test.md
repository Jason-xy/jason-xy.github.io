---
title: Jetpack(L4T) Perf性能分析工具安装及测试
category_bar: true
date: 2022-12-07 01:05:46
tags: ['jetson','l4t','ubuntu', 'perf']
categories:
- 技术
- Linux
- Usage
---

不同于普通AMD64的PC，安装perf相关工具一行apt命令即可`sudo apt install linux-tools-common  linux-tools-"$(uname -r)"  linux-cloud-tools-"$(uname -r)"`, L4T版本的perf由于不在arm64的apt源中需要从源码编译。

## 获取源码

1. 可以通过jtop直接查看相应的l4t版本，如图![image-20221206235818326](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/PicGo/202212070000797.png)

2. 去[nvidia下载中心](https://developer.nvidia.com/embedded/downloads)寻找相应版本的Linux Driver![image-20221207000032138](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/PicGo/202212070000620.png)

3. 进入release page找到BSP源码

![image-20221207000212684](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/PicGo/202212070002708.png)

4. 复制链接后直接用`wget`下载至jetson，通常为`public_sources.tbz2`，通过`tar -xjvf public_sources.tbz2`解压后可以得到`Linux_for_Tegra`目录![image-20221207000416790](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/PicGo/202212070004807.png)

5. 进入目录`Linux_for_Tegra/source/public`，解压`tar -xjvf kernel_src.tbz2`，之后可会得到`kernel`目录，进入目录`kernel/kernel-5.10/tools/perf`，即可找到perf相关的源码

## 编译并安装perf

`make && sudo rm /usr/bin/perf && sudo cp perf /usr/bin/perf `

可以根据检查报告安装相应的库，以支持更加丰富的功能

![image-20221207212812351](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/PicGo/202212072128394.png)

检查perf是否安装成功`perf --version`

![image-20221207001524167](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/PicGo/202212070015187.png)

## 生成火焰图

**下载火焰图工具**

`git clone https://github.com/brendangregg/FlameGraph`

**查看并设置系统限制**

```shell
sysctl kernel.kptr_restrict 
sudo sysctl -w kernel.kptr_restrict=0
sudo sysctl -w kernel.perf_event_paranoid=-1
```

**采集特定进程信息**

`perf record -F 100 -p  1157822  -g -- sleep 100`

采样频率100Hz，PID 1157822，包含调用栈，持续100s

**生成火焰图**

```shell
export PATH=<FlameGraph工程目录>:$PATH
perf script  | stackcollapse-perf.pl | flamegraph.pl  > out.svg
```

用浏览器打开即可

![out](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/PicGo/202212070103413.svg)