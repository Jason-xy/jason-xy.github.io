---
title: PC模拟Jetson（Jetpack）运行环境
category_bar: true
date: 2021-01-29 11:24:55
tags: ['ubuntu', 'linux', 'jetpack', 'nvidia']
categories:
- 技术
- Linux
- ENV
---

## 硬件平台

HUAWEI Matebook 14

CPU：Intel® Core™ i5-8265U CPU @ 1.60GHz × 8

GPU：GeForce MX250/PCIe/SSE2 / GeForce MX250/PCIe/SSE2

![img](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/Ubuntu_nvidia/1.png)

## 操作系统

推荐：Ubuntu 18.04 LTS

## Nvidia显卡驱动安装

1. 更换国内的系统源，推荐Aliyun源

2. update, upgrade

   ```bash
   sudo apt update
   sudo apt upgradeCOPY
   ```

3. 加入N卡驱动源

   ```bash
   sudo add-apt-repository ppa:graphics-drivers/ppa
   sudo apt-get updateCOPY
   ```

4. 查看显卡驱动，会列出当前的显卡和可安装的驱动

5. ```bash
   ubuntu-drivers devicesCOPY
   ```

   ```bash
   jason@Jason-Matebook:~$ ubuntu-drivers devices
   == /sys/devices/pci0000:00/0000:00:1c.0/0000:01:00.0 ==
   modalias : pci:v000010DEd00001D13sv000019E5sd00003E0Abc03sc02i00
   vendor   : NVIDIA Corporation
   model    : GP108M [GeForce MX250]
   driver   : nvidia-driver-440 - distro non-free recommended
   driver   : nvidia-driver-435 - distro non-free
   driver   : xserver-xorg-video-nouveau - distro free builtinCOPY
   ```

6. 直接安装推荐的驱动

   ```bash
   sudo ubuntu-drivers autoinstallCOPY
   ```

7. 安装完重启：

   ```bash
   sudo rebootCOPY
   ```

8. 查看一下显卡信息：

   ```bash
   nvidia-smiCOPY
   ```

   ```bash
   Sat Jul 11 11:39:23 2020       
   +-----------------------------------------------------------------------------+
   | NVIDIA-SMI 440.100      Driver Version: 440.100      CUDA Version: 10.2     |
   |-------------------------------+----------------------+----------------------+
   | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
   | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
   |===============================+======================+======================|
   |   0  GeForce MX250       Off  | 00000000:01:00.0 Off |                  N/A |
   | N/A   46C    P0    N/A /  N/A |    228MiB /  2002MiB |      0%      Default |
   +-------------------------------+----------------------+----------------------+
   
   +-----------------------------------------------------------------------------+
   | Processes:                                                       GPU Memory |
   |  GPU       PID   Type   Process name                             Usage      |
   |=============================================================================|
   |    0       976      G   /usr/lib/xorg/Xorg                            61MiB |
   |    0      1540      G   /usr/lib/xorg/Xorg                            70MiB |
   |    0      1784      G   /usr/bin/gnome-shell                          88MiB |
   +-----------------------------------------------------------------------------+
   COPY
   ```

如果有信息显示出来，就说明安装成功

## 环境检查

1.验证自己的电脑是否有一个可以支持CUDA的GPU：

你可以在电脑的配置信息中找到显卡的具体型号，如果你是双系统，在windows下的设备管理器中也可以查到显卡的详细信息;在ubuntu下面的话，你可以通过下面这行命令来简单查看一下NVIDIA GPU版本信息：

```bash
lspci | grep -i nvidia

jason@Jason-Matebook:~$ lspci | grep -i nvidia
01:00.0 3D controller: NVIDIA Corporation GP108M [GeForce MX250] (rev a1)COPY
```

2.验证自己的linux版本是否支持CUDA：

```bash
uname -m && cat /etc/*release

jason@Jason-Matebook:~$ uname -m && cat /etc/*release
x86_64
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04 LTS"
NAME="Ubuntu"
VERSION="20.04 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focalCOPY
```

3.验证系统是否安装了gcc

```bash
gcc --version

jason@Jason-Matebook:~$ gcc --version
gcc (Ubuntu 9.3.0-10ubuntu2) 9.3.0
Copyright (C) 2019 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.COPY
```

其实这个刚装完系统的话应该都装好了，但是为了保险起见，你还是可以查看一下，如果你没有安装的话，你可以采取下面的命令对其进行安装：

```bash
sudo apt-get install build-essentialCOPY
```

4.验证系统是否安装了kernel header和package development:

a.查看正在运行的系统内核版本：

```bash
uname -r

jason@Jason-Matebook:~$ uname -r
5.4.0-40-genericCOPY
```

b.安装对应kernel版本的kernel header和package development：

```bash
sudo apt-get install linux-headers-$(uname -r)

jason@Jason-Matebook:~$ sudo apt-get install linux-headers-$(uname -r)
正在读取软件包列表... 完成
正在分析软件包的依赖关系树       
正在读取状态信息... 完成       
linux-headers-5.4.0-40-generic 已经是最新版 (5.4.0-40.44)。
linux-headers-5.4.0-40-generic 已设置为手动安装。
升级了 0 个软件包，新安装了 0 个软件包，要卸载 0 个软件包，有 0 个软件包未被升级。COPY
```

这个的话表示系统中已经有了，不用重复安装。

如果以上各项检查均满足要求的话，接下来就可以正式地进入安装界面了，如果有些地方没有满足要求的话，你需要参考cuda的官方文档，里面有详细的针对每个问题的解决方案。在以下链接中可以找到：https://docs.nvidia.com/cuda/

## 安装CUDA

CUDA11.1安装：[CUDA 工具包 11.1 下载 | NVIDIA Developer](https://developer.nvidia.com/zh-cn/cuda-downloads)

根据自己的**操作系统、构架、发行版本、系统版本**选择

比如我选择的是：Linux->x86_64->Ubuntu->18.04->deb(network)

然后执行：

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cudaCOPY
```

### 配置环境变量

输入gedit ~/.bashrc命令打开文件，在文件结尾输入以下语句，保存。

```bash
export PATH=/usr/local/cuda-11.2/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-11.2/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}COPY
```

至此cuda安装完成，输入nvcc -V命令查看cuda信息。

```bash
jason@Jason-Matebook:~$ nvcc -V
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2020 NVIDIA Corporation
Built on Thu_Jun_11_22:26:38_PDT_2020
Cuda compilation tools, release 11.2, V11.2.194
Build cuda_11.2_bu.TC445_37.28540450_0COPY
```

## 安装cuDNN

cuDNN安装：[cuDNN Download | NVIDIA Developer](https://developer.nvidia.com/rdp/cudnn-download)

根据自己的**系统版本**下载deb安装包直接安装。

例如我下载的是：

[cuDNN Runtime Library for Ubuntu18.04 x86_64 (Deb)](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.0.5/11.1_20201106/Ubuntu18_04-x64/libcudnn8_8.0.5.39-1+cuda11.1_amd64.deb)

[cuDNN Developer Library for Ubuntu18.04 x86_64 (Deb)](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.0.5/11.1_20201106/Ubuntu18_04-x64/libcudnn8-dev_8.0.5.39-1+cuda11.1_amd64.deb)

[cuDNN Code Samples and User Guide for Ubuntu18.04 x86_64 (Deb)](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.0.5/11.1_20201106/Ubuntu18_04-x64/libcudnn8-samples_8.0.5.39-1+cuda11.1_amd64.deb)

安装顺序：

```bash
sudo dpkg -i libcudnn8_8.0.5.39-1+cuda11.1_amd64.deb
sudo dpkg -i libcudnn8-dev_8.0.5.39-1+cuda11.1_amd64.deb
sudo dpkg -i libcudnn8-samples_8.0.5.39-1+cuda11.1_amd64.debCOPY
```

## 安装cuDNN额外依赖

```bash
sudo apt-get update
sudo apt-get upgrade -yCOPY
```

## 安装TensorRT

TensorRT 7安装：[NVIDIA TensorRT 7.x Download | NVIDIA Developer](https://developer.nvidia.com/nvidia-tensorrt-7x-download)

根据自己的**系统版本**和**CUDA版本**下载deb安装包直接安装。

例如我下载的是：

[TensorRT 7.2.2 for Ubuntu 18.04 and CUDA 11.1 & 11.2 DEB local repo package](https://developer.nvidia.com/compute/machine-learning/tensorrt/secure/7.2.2/local_repos/nv-tensorrt-repo-ubuntu1804-cuda11.1-trt7.2.2.3-ga-20201211_1-1_amd64.deb)

安装：

```bash
sudo dpkg -i nv-tensorrt-repo-ubuntu1804-cuda11.1-trt7.2.2.3-ga-20201211_1-1_amd64.debCOPY
```

## 安装TensorRT额外依赖

```bash
sudo apt-get update
sudo apt-get upgrade -yCOPY
```

## 部署jetson-inference

详细教程查看：[jetson-inference](https://github.com/dusty-nv/jetson-inference)

```bash
$ sudo apt-get update
$ sudo apt-get install git cmake libpython3-dev python3-numpy python3-opencv
$ git clone --recursive https://github.com/dusty-nv/jetson-inference
$ cd jetson-inference
$ mkdir build
$ cd build
$ cmake ../
$ make -j$(nproc)
$ sudo make install
$ sudo ldconfigCOPY
```