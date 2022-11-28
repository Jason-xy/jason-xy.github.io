---
title: CUDA on Arm - L4t ubuntu基础
category_bar: true
date: 2021-08-21 16:42:53
tags: ['jetson','cuda','ubuntu', 'GPU', 'ARM']
categories:
- 技术
- Course
- CUDA
---

🎉感谢来自NVIDIA企业级开发者社区的李奕澎老师提供的资料 

## 应用需求

![image-20210816093522065.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/16/image-20210816093522065.png)

## Linux基础

### X86 Ubuntu与L4T Ubuntu的区别

**X86 Ubuntu ：**是指运行在X86架构CPU的linux ubuntu版本的操作系统。

**L4T Ubuntu：**L4T 是linux for tegra的缩写，Tegra是集成了ARM架构的CPU和NVIDIA的GPU的处理器芯片，所以L4T Ubuntu就是为运行在基于arm架构的Tegra芯片上的linux ubuntu版本的操作系统，它是专门为Tegra定制的Ubuntu特殊版本。

**X86 Ubuntu与L4T Ubuntu主要区别：**

- 应用场景：ARM处理器定位于嵌入式平台，应用在开发板、边缘设备、智能设备上；X86定位于桌面PC和服务器。

- ARM是为了低功耗高效率设计的，而X86是为了追求高性能。

- 设计架构：ARM是精简指令集（RISC）架构；x86是复杂指令集(CISC)架构。

- ARM几乎都采用Linux的操作系统；X86多为window系统也可采用linux操作系统。

![image-20210821160532816](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821160532816.png)

### UBUNTU的文件管理 - 目录结构

![image-20210821160942100](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821160942100.png)

![image-20210821160947338](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821160947338.png)

![image-20210821161000414](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821161000414.png)

### UBUNTU的权限管理

**超级用户：**

```bash
//进入超级用户终端
su
//退出当前用户
exit
//使用户在自己的环境下执行需要超级用户权限的任务
sudo <command>
```

**用户组：**

![image-20210821161302930](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821161302930.png)

- （User）文件所有者。

- （Group）群组 ，我们可以在Ubuntu系统中为团    队建立一个群组。

- （Others）其他人，不属于文件所有者或文件所属群组成员的用户便都是其他人。

![image-20210821161321190](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821161321190.png)

![image-20210821161356482](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821161356482.png)

### UBUNTU基本操作命令 

![image-20210821161443187](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821161443187.png)

![image-20210821161456335](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821161456335.png)

![image-20210821161518808](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821161518808.png)

### UBUNTU网络管理

![image-20210821161927164](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821161927164.png)

## JupyterLab

**JupyterLab ：**数据科学生产工具，它作为一种基于web的集成开发环境，你可以使用它编写代码的notebook、操作终端、编辑markdown文本、打开交互模式、查看csv文件及图片等功能。

**安装方法：**

```bash
#pip
pip install jupyterlab

#conda
conda install -c conda-forge jupyterlab
```

**启动方法：**

```bash
jupyterlab
```

![image-20210821160840882](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821160840882.png)

## Makefile基本介绍与编写规则

### 什么是Makefile

当一个工程中的源文件不计数，其按类型、功能、模块分别放在若干个目录中，makefile定义了一系列的规则，可以来指定哪些文件需要先编译，哪些文件需要后编译，哪些文件需要重新编译。makefile就像一个Shell脚本一样，可以实现“自动化编译”，一旦写好，只需要一个make命令，整个工程完全自动编译，极大的提高了软件开发的效率。

### Makefile编写规则

![image-20210821162204257](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821162204257.png)

**多个C工程文件：**

![image-20210821162236572](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821162236572.png)

