---
title: 基于PrimoCache的硬盘缓存加速
date: 2020-06-20 18:13:34
category_bar: true
tags: ['cache', 'smr']
categories:
- 生活
- IT小技巧
---
![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/primocache/20200620173204.png)

目前随着硬盘发展的趋势，越来越多的硬盘采用了SMR存储技术，厂商依托这个技术节省了成本，但是用户却常常饱受硬盘掉速的困扰。

**如下图所示👇**

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/primocache/1592645713715.jpeg)希捷酷鱼全线SMR

在进行大文件（10G左右）读写的时候就会有明显的感受，看着这十几万的延迟，不得不说👴要等吐了！

由此可见SMR硬盘是有多么依赖缓存空间！！！

#### 下面有请SMR救星[PrimoCache](https://www.romexsoftware.com/zh-cn/primo-cache/index.html)上场！

先看看加速效果：

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/primocache/1592645718106.jpeg)过于暴力的测试数据

### PrimoCache的使用方法

- **前期准备**

1）前往[PrimoCache官网](https://www.romexsoftware.com/zh-cn/primo-cache/index.html)下载正版付费软件

 注：[用于学习研究的小伙伴可以点这个链接](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/primocache/PrimoCache%20v3.09.zip)进行下载

2）按照流程进行安装（等一些列操作）

- **开始配置PrimoCache**

1）创建加速任务

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/primocache/1.png)

2）选择需要加速的硬盘

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/primocache/2.png)

在这里选择你想要加速度的硬盘，通常选择你想要拯救的SMR盘

3）配置缓存策略

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/primocache/3.png)

**L1级缓存**

通过分配一部分内存空间来作为硬盘缓存分区，根据自己的电脑配置来分配空间大小。

通常来说不必分太大的内存，L1级缓存的作用一般只用于加速碎片文件的读写，体积通常不会太大。

**L2级缓存**

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/primocache/4.png)

先将SSD分配一块空白区域，**单独分为一块磁盘分区**。

选择刚刚分好的分区，并分配用于读和写的缓存空间。

**缓存粒度**

数值越小 | 加速效果越好 | CPU占用越高

数值越大 | 加速效果次之 | CPU占用越低

**预取设置**

最好别开机预取，不然电脑开机得开几十分钟！！！

**注意：**未开启预取，意味着如果在缓存没有完全写入目标磁盘前**关机**、**断电**等情况出现你将会面临**数据丢失**！！！

- **配置完毕点击启动！！！**

### **小技巧：**

如何确定缓存是否完全写入目标磁盘?

1. 打开**任务管理器**
2. 点击**性能**查看磁盘详情
3. 观察目标磁盘**活动时间**和**延迟**，如果活动时间占比长时间小于20%且延迟小于10ms基本可以判断已完成写入！