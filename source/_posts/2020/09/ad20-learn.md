---
title: AD学习——基本简介
category_bar: true
date: 2020-09-22 13:52:08
tags: ['AD', 'EDA']
categories:
- 技术
- Embedded
- Hardware
---

**教程链接：**[地址](https://www.bilibili.com/video/BV16t411N7RD)

## 1.软件安装

设置中文：右上角设置->全局设置->系统->本地化资源

## 2.工程创建及基本结构

**结构：**

1. 原理图文件
   1. 原理图库
   2. PCB文件
   3. PCB库
   4. 电路板生产文件

## 3.电阻电容原理图模型创建

1. 针管方向
2. 引脚长度
3. 栅格大小
4. 颜色选择
5. 旋转快捷键：空格

## 4.IC类元件原理图模型创建

1. 矩形边框
2. 引脚编号
3. 引脚名称
4. 元件封装（后面再加）

## 5.排针类元件原理图模型创建

1. 阵列粘贴
2. 长度的正负可代表引脚方向

## 6.光耦及二极管元件原理图模型创建

1. 多边形工具
2. 针管模型选择

## 7.现有元件模型的调用

1. 生成原理图库（切换到原理图卡片）

## 8.元件的放置

1. 切换到原理图库->选择元件->左下角放置
2. 原理图界面->右下角panels->component->右侧选择原理图库->选择元件->拖拽放置

## 9.器件的复制与对其

1. 镜像快捷键

2. 快捷键A，对齐菜单

3. 镜像对称：

   X对称：拖动+X

   Y对称：拖动+Y

## 10.导线和标签的添加

1. 添加导线 ctrl+w

## 11.value值的校对

1. 修改标注：T+A+A
2. 检查连接性

## 12.封装管理

1. 封装管理器统一管理。工具->封装管理器

## 13.原理图的编译以及检查

1. 工具->工程选项
2. alt+左键：查看标签连接对象
3. 检查单管引脚

## 14.常见CHIP封装的创建

1. 焊盘
2. 管脚序号
3. 阻焊
4. 1脚标识
5. 丝印
6. 元件本体
7. 精准移动快捷键：M

## 15.常见IC类封装的创建

1. 焊盘尺寸计算
2. 阻焊稍微大一点
3. 特殊粘贴

## 16.利用IPC封装创建向导快速创建封装

1. 扩展和更新->IPC Footprint Generator

## 17.常用PCB封装调用

1. 生成已有文件PCB库

## 18.3D模型的创建与导入

1. 绘制范围及丝印范围
2. 整体高度与悬浮高度
3. 放置3D Body可以直接导入
4. 3D元件体可以自定义也可以导入库

## 19.导入常见报错解决办法

1. 元件名
2. 元件管脚缺失
3. 管脚号不匹配

## 20.常见绿色报错的消除

1. 工具->设计规则查看器：删除不需要的规则

## 21.PCB板框评估以及叠层设计

1. 工具->器件摆放->器件阵列排布
2. 确定板框尺寸（稍微大一点）
3. 放置->线性尺寸
4. 快捷键 D+S+D重新定义板框（裁剪）
5. 正片层：见到的线就是铜
6. 负片层：与正片层相反

## 22.PCB常用快捷键设置

1. 找到要设置快捷键的命令
2. ctrl+左键点击，进入快捷键设置窗口
3. 设置快捷键
4. 常用设置：![QQ20200920115227.jpg](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/20/QQ20200920115227.jpg)

## 23.模块化布局规划

1. 工具->交叉选择模式
2. 信号电源分离，先隐藏电源网络调整信号网络。

## 23.实战操作

1. 布局原则：先大后小
2. 以功能模块为单位，按信号流向摆件
3. 丝印格式改变
4. 元件位号位置设置（中心）
5. 元件高亮，方便筛选

## 24.PCB设计规则以及PCB手工布线

1. Class：类。（电源、信号）

2. 电源线加粗。

3. 规则编辑器：

   间距规则：最小线宽、线距。4<x<6最好。（注意规则优先级和使能）

   过孔规则：最好12mil。（2*H（孔径）+-2mil）。推荐过孔盖油。

   铺铜规则： 平面间隙8mil最好。焊盘采用十字连接、过孔采用全连接。

## 25.扇孔的处理

1. 打孔占位，减少回流路径。

## 26.信号线布线

1. 先连线，再优化
2. 能少打孔少打孔

## 27.电源线走线

## 28.信号线优化、GND处理

1. 多边形挖空消除尖角铜皮。

# AD常用快捷键

AD常用快捷键
旋转：Space；

X轴镜像：X；
Y轴镜像：Y；
板层管理：L；
栅格设置：G；
单位进制切换：Q；

对齐-水平：A，D；
对齐-垂直：A，I，I，Enter；
对齐-顶部：A，T；
对齐-底部：A，B；
对齐-左侧：A，L；
对齐-右侧：A，R；

设计-类设置：D，C;
设计-板层管理：D，K；
设计-规则：D，R；
设计-规则向导：D，W；
设计-拷贝ROOM格式：D，M，C；
设计-放置ROOM：D，M，R；
设计-根据选择对象定义板子形状：D，S，D；
设计-编辑网络：D，N，N；

编辑-删除：E，D；
编辑-切断轨迹：E，K；
编辑-设定原点：E，O，S；
编辑-复位原点：E，O，R；

移动-移动：M，M；
移动-拖拽：M，D；
移动-器件：M，C；
移动-打断走线：M，B；
移动-器件翻转板层：M，I；

网络-显示网络：N，S，N；
网络-显示器件：N，S，O；
网络-显示全部：N，S，A；
网络-隐藏网络：N，H，N；
网络-隐藏器件：N，H，O；
网络-隐藏全部：N，H，A；

放置-坐标：P，O；
放置-焊盘：P，P；
放置-字符：P，S；
放置-过孔：P，V； 走线快速添加过孔：ctrl+shift+滚轮；
放置-多边形：P，R；
放置-填充：P，F；
放置-敷铜：P，G；
放置-线性尺寸：P，D，L；
放置-走线：P，T；
放置-差分对布线：P，I；
放置-多根布线：P，M，Enter；

选择-全选：S，A；
选择-线选：S，L；
选择-区域（内部）：S，I；

选择-区域（外部）：S，O；

选择网络：S,P;

工具-交叉探测对象：T，C；(+Ctrl:跳转到目标文件)
工具-泪滴选项：T，E；
工具-设计规则检查：T，D；
工具-复位错误标志：T，M；
工具-从选择元素创建板剪切：T，V，B
工具-网络等长调节：T，R；

取消布线-全部：U，A；
取消布线-网络：U，N；
取消布线-连接：U，C；
取消布线-器件：U，O；
取消布线-ROOM：U，R；

查看-合适区域：V，A；
查看-翻转板子：V，B；
查看-适合文件：V，D；
查看-合适板子：V，F；
查看-合适图纸：V，H；

测距：Ctrl + M；

清除蒙板： Shift + Ｃ；
查找相似对象： Shift +