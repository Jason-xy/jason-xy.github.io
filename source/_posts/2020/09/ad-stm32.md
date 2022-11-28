---
title: AD学习——STM32F103C8T6实战
category_bar: true
date: 2020-09-29 13:16:09
tags: ['AD', 'stm32']
categories:
- 技术
- Embedded
- Hardware
---

#### 资料下载：[STM32F103C8T6硬件资料](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/files/%E5%AD%A6%E4%B9%A0%E6%A8%A1%E6%9D%BF%28%E5%AE%9E%E4%BE%8B%29.zip)

## 实战操作

**新建工程**

![image.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image.png)

**保存（快捷键Ctrl+S）**

![image21155e0ac67076a1.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image21155e0ac67076a1.png)

**添加原理图和pcb**

![imagec6a3ab1a9d299772.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagec6a3ab1a9d299772.png)

**工具面板介绍**

![image200bac138d1066cd.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image200bac138d1066cd.png)

![imageecabb77c4dfae990.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imageecabb77c4dfae990.png)

**原理图结构**

![image5ef1ebe1db23f607.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image5ef1ebe1db23f607.png)

**原理图属性设置**

![image4b055e4fdd2802ce.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image4b055e4fdd2802ce.png)

![imagef6d983d5022bb24c.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagef6d983d5022bb24c.png)

**快捷键：Ctrl+Q 转换单位(mil\<-\>mm)**

**P3**

**再打开一份工程《stm32最小系统》**

![image965457c19ece26ad.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image965457c19ece26ad.png)

**窗口设置**

![image1f735e8f356356b3.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image1f735e8f356356b3.png)

![image267cdd80b87dd846.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image267cdd80b87dd846.png)

**原理图库的导入(版本不同名称不同，20版叫components,也有叫libraries的**

![image6dbcbec929e44317.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image6dbcbec929e44317.png)

![image2c6878d73c2e1775.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image2c6878d73c2e1775.png)

![image403efaa75f05aa68.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image403efaa75f05aa68.png)

![image98f7d2459081aa89.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image98f7d2459081aa89.png)

**选择导入的原理图库**

![image0c3b0f1c979648d3.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image0c3b0f1c979648d3.png)

**放置主芯片(双击选择也行)**

![imaged38693b1eee18734.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imaged38693b1eee18734.png)

![imagee8c1141911c55323.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagee8c1141911c55323.png)

**按Esc或鼠标右键取消放置**

**按Ctrl + 鼠标滚轮 缩放图纸**

**按鼠标右键移动图纸**

**原件标号**

![image0e7f68ea28e5b999.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image0e7f68ea28e5b999.png)

**给原件自动标号步骤**

![imagea3426ff57d3d88db.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagea3426ff57d3d88db.png)

**网络编号(作用在于将引脚连起来)**

![image7bcf81ab98a3f540.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image7bcf81ab98a3f540.png)

**放置排针**

![image922493b764bfe498.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image922493b764bfe498.png)

![imagee27b4eb6ebb569e2.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagee27b4eb6ebb569e2.png)

**放置线**

![image8419f53fb7b7f08e.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image8419f53fb7b7f08e.png)

**放置5根线后选择并批量复制(Ctrl+C复制，Ctrl+V黏贴)**

![imagea2852ebfaee5eafa.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagea2852ebfaee5eafa.png)

![imageffffdd407d828c5c.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imageffffdd407d828c5c.png)

**快捷键 delete删除元件**

**过滤器介绍**

![imaged7b7801f4f8fc1e1.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imaged7b7801f4f8fc1e1.png)

**网络编号放置**

![imagee644dc63e73a060f.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagee644dc63e73a060f.png)

**按Tab弹出网络编号设置面板**

![imageb4254423c7374810.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imageb4254423c7374810.png)

**放置元件时**
**空格进行90度旋转,X键进行水平翻转，Y键进行垂直翻转，也可以按tab在面板中设置角度**

**放置线**

![image650867884e4ce479.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image650867884e4ce479.png)

**放线的时候空格设置线条的放置模式**

**放置文本(按tab设置属性)**

![imagef08bae886a23142d.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagef08bae886a23142d.png)

![image8abd73ecb5c27afc.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image8abd73ecb5c27afc.png)

**P5**

**设置元件标号**

**添加PCB并保存**

![image86ab6156c5a71abc.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image86ab6156c5a71abc.png)

**将原理图导出为PCB**

![imagedc51bc563edf524b.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagedc51bc563edf524b.png)

**从原理图导入PCB**

![imagee10e2a1b43f765ad.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagee10e2a1b43f765ad.png)

**添加封装**

![image12c8a5269db9836f.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image12c8a5269db9836f.png)

![image00a4c6c0fa98f3db.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image00a4c6c0fa98f3db.png)

**批量添加封装**

![imagebd4c34076ce9e01c.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagebd4c34076ce9e01c.png)

![image2ae5e9f04433bfbe.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image2ae5e9f04433bfbe.png)

**快捷键Shift+C取消选择**

**编译工程**

![image9f255401c932063f.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image9f255401c932063f.png)

**绿色表明编译成功。**

**出现Error说明有问题。通过编译信息可以快速找到那些地方有错误，比如没封装之类的。**

**编译完成后导入PCB**

![image4c01746349529967.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image4c01746349529967.png)

![image6b71fa9c48947068.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image6b71fa9c48947068.png)

![imageba2e29ccac2f4b31.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imageba2e29ccac2f4b31.png)

![imagedc739aa164cace91.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagedc739aa164cace91.png)

**封装管理器查看**

![image0ff2fff350662e3a.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image0ff2fff350662e3a.png)

![image1ddeff9633826d2d.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image1ddeff9633826d2d.png)

**PCB元件布局**

![image08de76e64545ac3d.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image08de76e64545ac3d.png)

![imagec0c7066b5d50799d.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagec0c7066b5d50799d.png)

![image4ef52257f1d1adab.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image4ef52257f1d1adab.png)

**快捷键：按住Shift进行多选**

**选择元件后点击就能集中元件**

![image9b9ead63495e4557.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image9b9ead63495e4557.png)

![image3e4c35e734749687.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image3e4c35e734749687.png)

**按N键后点击"隐藏链接"在点击隐藏链"全部"来隐藏线条**

**先挑出两个排针来确定板子形状**

![image063e5a9e25548313.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image063e5a9e25548313.png)

**设置原点**

![imageed633c75e988f77b.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imageed633c75e988f77b.png)

**设置板子宽度**

![image353743f3f321f93a.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image353743f3f321f93a.png)

![image6f7ee5484b1f8f20.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image6f7ee5484b1f8f20.png)

![image1c85452a0080a8d3.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image1c85452a0080a8d3.png)

**画好线条后精确设置宽度**

![image4490bfd88f993da5.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image4490bfd88f993da5.png)

**将排针进行放到线上并进行底对齐**

![image155aa7ee75c18fcd.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image155aa7ee75c18fcd.png)

**然后将线围绕边框**

![imagee825b80ffcb8132c.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagee825b80ffcb8132c.png)

**按照画好的框图确定板子最终形状(要先选择紫色的线条才行)**

![image8954e9aa0258c27e.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image8954e9aa0258c27e.png)

![image0bd799a28e17a870.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image0bd799a28e17a870.png)

**微调快捷键：选择元件按住鼠标左键并按方向键**

**点击锁定后防止误移元件**

![image877536c3db491ab5.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image877536c3db491ab5.png)

**设置辅助线**

![image095557e2dae3b1af.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image095557e2dae3b1af.png)

**集中元件**

![image173153192855d8ae.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image173153192855d8ae.png)

**点击快捷键L让元件放在底层(蓝色)**

![imageee5e151b9e1d9414.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imageee5e151b9e1d9414.png)

**有连线关系的元件应该放尽量紧密**

**快捷键L切除可见面板隐藏元件名称**

![image25c8f0af97bfe11f.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image25c8f0af97bfe11f.png)

**元件摆放规则设置**

![imagee10b0444e19b3be3.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagee10b0444e19b3be3.png)

![image7e687ed1f5e9bd63.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image7e687ed1f5e9bd63.png)

**参数设置：**

**1.  6mil**

**2.  0mil**

**3.  0mil**

**4.  0mil**

**5.  0mil**

**切换三维模式**

![image8f10a5015854d0b0.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image8f10a5015854d0b0.png)

![image-20200929132904788](C:\Users\Jason\AppData\Roaming\Typora\typora-user-images\image-20200929132904788.png)

**所有元件摆放完成后效果**

![imagec091857084b97063.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagec091857084b97063.png)

**手动布线**

**将不必要的层隐藏(顶层和底层)**

![imagee0dac2a29d6a36c3.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagee0dac2a29d6a36c3.png)

![image5f6bd7b8443bb595.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image5f6bd7b8443bb595.png)

![imaged3da0ff1b614e5e0.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imaged3da0ff1b614e5e0.png)

**布线**

![imagebc1d6faa488d784c.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagebc1d6faa488d784c.png)

**规则设置**

![image0f4664309bc56998.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image0f4664309bc56998.png)

**放置过孔(用于连接两层不同的元件)，快捷键\*键**

![image04d0d30b043d5329.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image04d0d30b043d5329.png)

**设置线宽上下限**

![imagef294f18140ef604a.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/imagef294f18140ef604a.png)![image9b191add0a8d65c5.png](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2020/09/29/image9b191add0a8d65c5.png)