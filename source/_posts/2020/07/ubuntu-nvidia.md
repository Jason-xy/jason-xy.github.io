---
title: Ubuntu20.04下安装Cuda11.0+Nvidia-440+Cudnn7.1.4+Tensorflow1.9
category_bar: true
date: 2020-07-11 13:44:36
tags: ['ubuntu', 'nvidia', 'linux']
categories:
- 技术
- Linux
- ENV
---

## 硬件平台

HUAWEI Matebook 14

CPU：Intel® Core™ i5-8265U CPU @ 1.60GHz × 8 

GPU：GeForce MX250/PCIe/SSE2 / GeForce MX250/PCIe/SSE2

<img align=center src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/Ubuntu_nvidia/1.png" style="zoom:80%;" />

## Nvidia显卡驱动安装

1. 更换国内的系统源，推荐Aliyun源

2. update, upgrade

   ```bash
   sudo apt update
   sudo apt upgrade
   ```

3. 加入N卡驱动源

   ```bash
   sudo add-apt-repository ppa:graphics-drivers/ppa
   sudo apt-get update
   ```

4. 查看显卡驱动，会列出当前的显卡和可安装的驱动

   ```bash
   ubuntu-drivers devices
   ```

   ```bash
   jason@Jason-Matebook:~$ ubuntu-drivers devices
   == /sys/devices/pci0000:00/0000:00:1c.0/0000:01:00.0 ==
   modalias : pci:v000010DEd00001D13sv000019E5sd00003E0Abc03sc02i00
   vendor   : NVIDIA Corporation
   model    : GP108M [GeForce MX250]
   driver   : nvidia-driver-440 - distro non-free recommended
   driver   : nvidia-driver-435 - distro non-free
   driver   : xserver-xorg-video-nouveau - distro free builtin
   ```

5. 直接安装推荐的驱动

   ```bash
   sudo ubuntu-drivers autoinstall
   ```

6. 安装完重启：

   ```bash
   sudo reboot
   ```

7. 查看一下显卡信息：

   ```bash
   nvidia-smi
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
   
   ```

如果有信息显示出来，就说明安装成功

## 环境检查

1.验证自己的电脑是否有一个可以支持CUDA的GPU：

你可以在电脑的配置信息中找到显卡的具体型号，如果你是双系统，在windows下的设备管理器中也可以查到显卡的详细信息;在ubuntu下面的话，你可以通过下面这行命令来简单查看一下NVIDIA GPU版本信息：

```bash
lspci | grep -i nvidia
```

```bash
jason@Jason-Matebook:~$ lspci | grep -i nvidia
01:00.0 3D controller: NVIDIA Corporation GP108M [GeForce MX250] (rev a1)
```

2.验证自己的linux版本是否支持CUDA：

```bash
uname -m && cat /etc/*release
```

```bash
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
UBUNTU_CODENAME=focal
```

3.验证系统是否安装了gcc

```bash
gcc --version
```

```bash
jason@Jason-Matebook:~$ gcc --version
gcc (Ubuntu 9.3.0-10ubuntu2) 9.3.0
Copyright (C) 2019 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

其实这个刚装完系统的话应该都装好了，但是为了保险起见，你还是可以查看一下，如果你没有安装的话，你可以采取下面的命令对其进行安装：

```bash
sudo apt-get install build-essential
```

4.验证系统是否安装了kernel header和package development:

a.查看正在运行的系统内核版本：

```bash
uname -r
```

```bash
jason@Jason-Matebook:~$ uname -r
5.4.0-40-generic
```

b.安装对应kernel版本的kernel header和package development：

```bash
sudo apt-get install linux-headers-$(uname -r)
```

```bash
jason@Jason-Matebook:~$ sudo apt-get install linux-headers-$(uname -r)
正在读取软件包列表... 完成
正在分析软件包的依赖关系树       
正在读取状态信息... 完成       
linux-headers-5.4.0-40-generic 已经是最新版 (5.4.0-40.44)。
linux-headers-5.4.0-40-generic 已设置为手动安装。
升级了 0 个软件包，新安装了 0 个软件包，要卸载 0 个软件包，有 0 个软件包未被升级。
```

这个的话表示系统中已经有了，不用重复安装。

如果以上各项检查均满足要求的话，接下来就可以正式地进入安装界面了，如果有些地方没有满足要求的话，你需要参考cuda的官方文档，里面有详细的针对每个问题的解决方案。在以下链接中可以找到：https://docs.nvidia.com/cuda/

## 安装CUDA

我们首先进入nvidia的[官方cuda9.0下载网页](https://developer.nvidia.com/cuda-90-download-archive)。https://developer.nvidia.com/cuda-90-download-archive

<img align=center src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/Ubuntu_nvidia/2.png" style="zoom:80%;" />

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget http://developer.download.nvidia.com/compute/cuda/11.0.2/local_installers/cuda-repo-ubuntu2004-11-0-local_11.0.2-450.51.05-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2004-11-0-local_11.0.2-450.51.05-1_amd64.deb
sudo apt-key add /var/cuda-repo-ubuntu2004-11-0-local/7fa2af80.pub
sudo apt-get update
sudo apt-get -y install cuda
```

按照指引安装CUDA。

### 配置环境变量

输入gedit ~/.bashrc命令打开文件，在文件结尾输入以下语句，保存。

```bash
export PATH=/usr/local/cuda-11.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-11.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

至此cuda安装完成，输入nvcc -V命令查看cuda信息。

```bash
jason@Jason-Matebook:~$ nvcc -V
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2020 NVIDIA Corporation
Built on Thu_Jun_11_22:26:38_PDT_2020
Cuda compilation tools, release 11.0, V11.0.194
Build cuda_11.0_bu.TC445_37.28540450_0
```

## **安装cuDNN**

从https://developer.nvidia.com/cudnn下载相应版本的cudnn，需要登陆。

<img align=center src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202007/Ubuntu_nvidia/3.png" style="zoom:80%;" />

下载解压之后，将cuda/include/cudnn.h文件复制到usr/local/cuda/include文件夹，将cuda/lib64/下所有文件复制到/usr/local/cuda/lib64文件夹中，并添加读取权限：

```bash
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

至此cuDNN安装完成

## 安装Tensorflow

1. 安装python（如果没有的话）

   ```bash
   sudo apt-get install python python3
   ```

2. 之后安装一下pip：

   ```bash
   sudo apt-get install python3-pip
   ```

   Python 2的 pip 没有被包含在 Ubuntu 20.04源仓库中。我们将会使用`get-pip.py`脚本来为 Python 2 安装 pip。

   启用 universe 源仓库：

   ```bash
   sudo add-apt-repository universe
   ```

   更新软件包索引，并且安装 Python 2：

   ```bash
   sudo apt update 
   sudo apt install python2
   ```

   使用`curl`命令来下载`get-pip.py`脚本：

   ```bash
   curl https://bootstrap.pypa.io/get-pip.py --output get-pip.py
   ```

   一旦源仓库被启用，以 sudo 用户身份使用 `python2`运行脚本来为 Python 2 安装 pip：

   ```bash
   sudo python2 get-pip.py
   ```

   Pip 将会被全局安装。如果你仅仅想为当前用户安装，不要使用`sudo`运行上面的命令。这个脚本还将会安装`setuptools` 和 `wheel`,它们允许你安装源码版本。

   打印 Pip 版本号，验证安装过程：

   ```bash
   pip2 --version
   ```

   输出如下：

   ```bash
   pip 20.0.2 from /usr/local/lib/python2.7/dist-packages/pip (python 2.7)
   ```

3. 安装tensorflow-gpu 1.9.0：

   ```bash
   sudo pip install tensorflow-gpu==1.9.0
   ```

   之后进入python输入以下命令检查安装是否完成：

   ```
   import tensorflow as tf
   print(tf.__version__)
   ```