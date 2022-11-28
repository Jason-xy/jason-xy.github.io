---
title: CUDA on Arm-GPU硬件架构
category_bar: true
date: 2021-08-21 17:40:02
tags: ['jetson','cuda','ubuntu', 'GPU', 'ARM']
categories:
- 技术
- Course
- CUDA
---

🎉感谢来自NVIDIA企业级开发者社区的何琨（Ken）老师提供的资料和细致耐心的讲解

## GPU简介

### GPU计算性能变迁

![image-20210821170245589](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821170245589.png)

### 体系结构层次

![image-20210816110140126](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/16/image-20210816110140126.png)

## 更快的计算机

#### 更多的晶体管

截至今日（2021-8-21）最新的Intel Alder Lake CPU构架如下图所示 ![Alder_Lake](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/Alder_Lake.jpg)

#### 摩尔定律

芯片的集成密度每2年翻翻，成本下降一半。

可是.......

常规传统单核处理器遇到物理约束，时钟频率〔perf/clock]无法保持线型增长。

![image-20210821171147714](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821171147714.png)

现在的CPU系统已经遇到各种瓶颈，只能向多核及并行系统发展。
顺势而生的GPU- Graphics Processing Unit如下图

![image-20210821171339020](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821171339020.png)

#### GPU计算性能与数据带宽

![image-20210821171354030](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821171354030.png)

![image-20210821171416620](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821171416620.png)

## GPU设计思想

### 内核设计

**CPU类型的内核**

![image-20210821171532969](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821171532969.png)

实际上正真用于计算的单元在内核面积中占比并不算高，更多的都是用于控制和存储的机构，例如对于指令流水线、乱序执行、分支预测等支持部件。而GPU内核就在此基础上精简掉不必要的结构，先安排计算核心而后考虑别的方法去添加控制与存储部件。

**精简后的CPU内核**

![image-20210821171852540](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821171852540.png)

将多个这样精简后的内核组合到一块就得到了GPU内核的雏形。

![image-20210821172130738](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821172130738.png)

### 芯片结构

![image-20210821172246899](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821172246899.png)

**GA-100结构**

![image-20210821200425778.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821200425778.png)

### GPU on Arm

![image-20210821172457800](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821172457800.png)

## GPU应用场景

### 快递员送披萨问题

**CPU处理**

![image-20210821172656932](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821172656932.png)

**GPU处理**

![image-20210821172722721.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821172722721.png)

### CUDA并行计算模式

- 并行计算是同时应用多个计算资源解决一个计算问题。
- 涉及多个计算资源或处理器。
- 问题被分解为多个离散的部分，可以同时处理(并行)，每个部分可以由一系列指令完成。
- 最好是计算密集的任务，通信和计算开销比例合适，不要受制于访存带宽。

### 程序并行化加速比

**程序可能的加速比取决于可以被并行化的部分**

- 如果没有可以并行化的， `P = 0` and the `speedup = 1` [no speedup]。
- 如果全部都可以并行化，`P = 1` and the speedup is infinite [in theory]。
- 如果50%可以并行化，`maximum speedup = 2`。

**如果有N个处理器并行处理**

![image-20210821173515481](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821173515481.png)

P = 并行部分，N = 处理器数量，s  = 串行部分。

**并行化的可扩展性有极限**

For example, at P = .50, .90 and .99  [50%，90% and 99% of the code isparallelizable]

![image-20210821173722760](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/21/image-20210821173722760.png)

