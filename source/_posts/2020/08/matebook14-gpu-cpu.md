---
title: 华为Matebook14解除CPU、GPU温度墙与功耗墙
category_bar: true
date: 2020-08-27 22:05:29
tags: ['matebook', 'cpu', 'gpu']
categories:
- 生活
- IT小技巧
---

最近（2020年8月）开学又拿出了我封存半年的Matebook，开机第一件事还是像往常一样更新驱动、更新系统。但是用着用着就发现问题来了。之前全高特效LOL都能无压力运行，但是这下最低画质也卡成PPT，再看一看系统功耗和CPU温度，稳定在了15W/70°。大事不妙！这个版本的驱动加强了功耗和温度限制，性能大幅缩水了！为了确认这一现象我赶快跑了跑AIDA64，果不其然，出大问题！

开始还指望售后帮我搞定，最后emmm，吐槽一波华为售后客服，啥都不懂。真要解决这个墙还是得靠自己。

**BIOS版本：1.16**

**DPTF版本：8.5.10103.7263**

### CPU性能限制解除

查询了多方资料和实验最终确定华为是利用**DPTF**来控制**CPU的功耗**，进而控制**CPU的温度**。

要解决掉DPTF，最简单的方法就是解决掉DPTF驱动

**打开任务管理器**

![dptf.jpg](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/08/27/dptf.jpg)

卸载前，分别打开设备的属性，查看其硬件Id，并将硬件Id的值都复制下来。

三个ID都复制下来。

![id.jpg](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/08/27/id.jpg)

然后Win+R输入gpedit.msc打开组策略编辑器，在计算机配置-管理模板-系统-设备安装-设备安装限制-阻止安装与下列任何设备ID相匹配的设备，选择启用，并将刚才复制出来的硬件Id都填到表格中。

*这一步主要是防止电脑自动安装DPTF导致每次都得重新设置*

**卸载这个DPTF驱动**

**重启电脑**

**安装Intel® Extreme Tuning Utility (Intel® XTU)：**

下载地址：[XTU](https://downloadcenter.intel.com/download/29183/Intel-Extreme-Tuning-Utility-Intel-XTU-)

手动调试CPU

![XTU.jpg](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/08/27/XTU.jpg)

主要调整Core参数：

1.核心电压降低-0.15V，来减少发热（看自己CPU的体制）。

2.瞬时睿频功率拉到30W就够了，问题不是很大。

3.最高功耗限制拉到25W，这本子太高了散热压不住，25W比官方的15W已经提升了很多性能了。

调整好了之后运行**压力测试**，验证一下是否调整成功。

**特别注释**：如果确实不行的话建议卸载华为电脑管家重试。或者还是不行，可以安装纯净版的win10再来操作。我就是直接重新安装的官方版的win10来进行调整的。

### GPU性能限制解除

根据我这几天的研究基本可以确定GPU的限制也是软墙，温度墙设置在了69°

原本MX250标准25W的TDP当撞到温度墙的时候频率降得惨不忍睹，这也是玩游戏出现严重掉帧的主要问题。

修改GPU温度墙就只需要ASUS GPUTweak II就够了。下载地址：[GPUTweak II](https://www.asus.com.cn/supportonly/GPUTweak II/HelpDesk_Download/)

**如果出现安装失败或者BIOS加载失败建议先卸载华为电脑管家，或者安装纯净版win10**

安装好并重启后系统就默认将温度墙设置为了94°，也可以根据自己的需要来自定义设置。

之后就来一把LOL测下帧率哇，我的又飞到快100帧了，出奇流畅。

### 注意事项：

这样的操作可以最大限度地自定义芯片性能，但是伴随的发热和功耗问题就自己权衡，需要性能的时候牺牲一点散热，需要续航的时候就牺牲一点性能。