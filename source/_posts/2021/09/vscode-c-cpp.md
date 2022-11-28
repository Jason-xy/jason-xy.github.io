---
title: VScode C/C++编译及调试环境配置
category_bar: true
date: 2021-09-17 16:12:19
tags: ['vscode', 'c/c++']
categories:
- 生活
- IT小技巧
---

博客原文：[VScode C/C++编译及调试环境配置](https://jason-xy.cn/2021/09/vscode_c_cpp/)

默认已安装了VScode

## MinGW安装（Windows）

[Mingw-w64下载地址](https://sourceforge.net/projects/mingw-w64/files/mingw-w64)

下载该文件（需要科学上网）

![image-20210917152719828](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917152719828.png)

国内COS服务器（作者按流量付费的，大伙省着点用）：[MinGW.zip](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/files/MinGW.zip)

下载后解压到一个可以记住的路径，如下图：

![image-20210917153207551](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917153207551.png)

## 添加环境变量

根据自己刚解压的路径配置环境变量。

如下图所示

![image-20210917153423654](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917153423654.png)

打开cmd检查是否配置成功

```
gcc -v
```

![image-20210917153608825](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917153608825.png)

```
g++ -v
```

![image-20210917153630672](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917153630672.png)

```
gdb -v
```

![image-20210917153717699](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917153717699.png)

出现版本信息则说明环境变量配置成功。

## VScode插件安装

### C/C++

C/C++基础支持

![image-20210917153933546](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917153933546.png)

#### 具体配置

`shift` + `ctrl` + `p`

如下搜索![image-20210917154321265](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917154321265.png)

配置详情

![image-20210917154458003](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917154458003.png)

### Code Runner

在终端运行代码

![image-20210917154014595](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917154014595.png)

#### 具体配置

进入配置界面

![image-20210917154600238](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917154600238.png)

具体配置

找到如下两项并打勾即可

![image-20210917154635439](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917154635439.png)

### Tabnine（可选）

基于机器学习的代码补全插件。

![image-20210917154153820](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917154153820.png)

## 使用测试

**打开一个工作区（文件夹）**

![image-20210917154842544](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917154842544.png)

![image-20210917154855546](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917154855546.png)

随便写一个简单的demo

![image-20210917155045160](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917155045160.png)

### 运行代码

点击右上角小三角即可在终端运行

![image-20210917155155115](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917155155115.png)

### 调试代码

点击左侧小bug创建`launch.json`文件

![image-20210917155304077](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917155304077.png)

选择`GDB/LLDB`

![image-20210917155342454](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917155342454.png)

选择`gcc`

![image-20210917155438680](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917155438680.png)

即可自动生成如下启动配置

```json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "gcc.exe - 生成和调试活动文件",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerPath": "C:\\MinGW\\bin\\gdb.exe",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "C/C++: gcc.exe 生成活动文件"
        }
    ]
}
```

按`F5`进入调试

**断点停留**

![image-20210917155839039](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917155839039.png)

**变量查看**

![image-20210917155854262](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917155854262.png)

**表达式监视**

![image-20210917160132558](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917160132558.png)

**堆栈调用**

![image-20210917160007732](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917160007732.png)

**调试方式选项**

![image-20210917160055651](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917160055651.png)

【Bug】寄存器监视打开后程序崩溃，从而看不到下面的画面

![image-20210917160505954](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/17/image-20210917160505954.png)

目前推测是gcc或者gdb的原因，具体解决方法请私信admin@jason-xy.cn

🥳到这里差不多基本功能都实现了，还有很多别的功能，就留给读者慢慢摸索吧。

