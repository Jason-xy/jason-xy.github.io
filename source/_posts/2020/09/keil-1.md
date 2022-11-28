---
title: keil上手
category_bar: true
date: 2020-09-11 8:16:11
tags: ['keil', 'stm32', 'embedded']
categories:
- 技术
- Embedded
- Software
---

## 任务目标

1. 如何建工程。
2. 如何配置与设置工程。
3. 如何编译自己开发的程序。
4. 如何调试。
5. 如何用KEIL开发stm32开发底层程序。
6. 如何下载编译过后的代码。
7. 如何查看寄存器。
8. 如何知道生成代码的地址。

......

## keil的安装

默认已经安装完成。

## keil的使用

### 1.如何新建工程（以STM32F1xx为例）

#### 准备工作

1. 新建工程文件夹

![20200910131230.jpg](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910131230.jpg)

2. 下载并解压官方的标准外设库

   <img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910131420.jpg" alt="20200910131420.jpg" style="zoom:67%;" />

3. 在***1.***中新建的文件夹下新建*Libraries*文件夹，用于存放标准外设库。

   <img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910132209.jpg" alt="20200910132209.jpg" style="zoom:67%;" />

4. 在***Libraries***下新建***CMSIS***并将***STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\CMSIS\CM3\CoreSupport***中的文件复制到***CMSIS***中；

   再将***STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\CMSIS\CM3\DeviceSupport\ST\STM32F10x***中的文件全部复制到***CMSIS***中。最终***CMSIS***中的文件如图。

   <img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910132703.jpg" alt="20200910132703.jpg" style="zoom:67%;" />

5. 将***STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\STM32F10x_StdPeriph_Driver***复制进***Libraries***文件夹，并将***STM32F10x_StdPeriph_Driver***改为***FWLIB***。

6. 在工程文件夹目录下新建***User***文件夹，随便写一个main.c放进去也行。

**准备基本完成**

#### 新建工程

在keil中新建一个工程文件，如图：

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910134356.png" alt="20200910134356.png" style="zoom:67%;" />

保存到刚刚新建的文件夹中，如图：

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910134725.jpg" alt="20200910134725.jpg" style="zoom: 50%;" />

随即选择所开发的芯片型号，如图（STM32F103C8为例）：

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910134909.jpg" alt="20200910134909.jpg" style="zoom:67%;" />

  确定之后跳出一个窗口，直接关掉（这是让选择启动引导等的，我们已经下载的库里有，所以跳过这一步）。点箭头指出的图表，然后删除掉SourceGroup1。

### 配置与设置工程

依次添加CMSIS、USER、FWLIB、STARTUP，并把刚刚复制在对应文件夹下的文件添加。注意的是STARTUP要选择NewProject\CMSIS\startup\arm中startup_stm32f10x_hd.s、startup_stm32f10x_ld.s、startup_stm32f10x_md.s**三个中的一个**，根据需要选取。文件类型选择所有。 FWLIB中只需要添加src中的.c文件即可。 CMSIS中不需要添加STARTUP中的文件。 添加完毕点击OK。

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910140251.jpg" alt="20200910140251.jpg" style="zoom:67%;" />

然后点击魔法棒，进入C/C++设置界面，在define一栏输入 USE_STDPERIPH_DRIVER ，并在includepath栏加入头文件路径。

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910142503.jpg" alt="20200910142503.jpg" style="zoom:67%;" />

 至此，工程创建完毕。

### 编译程序

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910144354.jpg" alt="20200910144354.jpg" style="zoom:67%;" />

根据需求选择编译的方式，在下方输出窗口可以看到**报错**和**警告**。

### 调试

#### 硬件仿真调试

进入调试模式：

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910220914.jpg" alt="20200910220914.jpg" style="zoom:50%;" />

调试面板详解：

1. 调试选项

   <img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910221608.jpg" alt="20200910221608.jpg" style="zoom: 33%;" />

2. 面板内容

   <img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910222017.jpg" alt="20200910222017.jpg" style="zoom:33%;" />

#### 软件仿真调试

修改魔法棒中Debug选项卡，如图：

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/12/20200912163606.jpg" alt="20200912163606.jpg" style="zoom: 33%;" />

Dialog DLL改为  DARMSTM.DLL

Parameter改为  -pSTM32F103C8（此项根据具体型号而定）

**以引脚的逻辑分析为例**

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/12/20200912164024.jpg" alt="20200912164024.jpg" style="zoom:33%;" />

设置想要检测的引脚：

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/12/20200912164436.jpg" alt="20200912164436.jpg" style="zoom:50%;" />

点击**run**即可查看引脚电平图像：

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/12/20200912164609.jpg" alt="20200912164609.jpg" style="zoom:33%;" />

### 用KEIL开发stm32开发底层程序

在User文件夹下编写程序。

以点灯为例（使用库函数）：

```c
#include "stm32f10x.h"

int main(void)
{
	GPIO_InitTypeDef GPIO_InitStructure; //定义GPIO初始化结构体
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);//开启对应时钟
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_13;//选择控制引脚
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;//设置引脚模式为通用推挽输出
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;//设置引脚速率为50MHz
	GPIO_Init(GPIOC, &GPIO_InitStructure);//初始化GPIO
	
	GPIO_ResetBits(GPIOC, GPIO_Pin_13);//点灯
	return 0;
}

```



### 下载编译过后的代码

以ST-Link仿真器为例。

1. 安装并更新仿真器驱动。

2. 用仿真器连接开发板。

   <img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/P00910-144702.jpg" alt="P00910-144702.jpg" style="zoom:10%;" />

3. 设置魔法棒

   选择ST-Link进行调试

   <img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910150123.jpg" alt="20200910150123.jpg" style="zoom: 50%;" />

   Flash下载设置

   <img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910150151.jpg" alt="20200910150151.jpg" style="zoom:67%;" />

4. 点击下载

   <img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910150450.jpg" alt="20200910150450.jpg" style="zoom: 50%;" />

5. 查看运行情况

   灯泡点亮，程序正在运行。

   <img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/P00910-1507401.jpg" alt="P00910-1507401.jpg" style="zoom: 10%;" />

### 查看寄存器

在左侧寄存器面板查看：

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/10/20200910222017.jpg" alt="20200910222017.jpg" style="zoom:33%;" />

具体的寄存器含义：

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/11/20200911080847.jpg" alt="20200911080847.jpg" style="zoom:67%;" />

### 查看生成代码的地址

<img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/11/TZ8P67FZ29H9UIXV2YL.png" alt="TZ8P67FZ29H9UIXV2YL.png" style="zoom: 33%;" />

也可以在右下方**Memory1**选项卡中**查看**和**修改**内存中存储的数据。