---
title: 树莓派安装OpenCV-4.3.0+Python
category_bar: true
date: 2020-06-28 12:06:47
tags: ['raspberry-pi', 'opencv']
categories:
- 技术
- Linux
- ARM
---

如果您曾经在Raspberry Pi（或之前的任何其他平台）上安装过OpenCV，那么您知道该进程可能非常耗时且需要安装许多依赖项和先决条件。本教程的目标是引导您逐步完成编译和安装过程。

# 前期准备

**注：Raspbian-lite所需要的额外操作将单独标注。**

1. 一台装好系统的Raspbarry Pi（建议不低于16GB micro-SD卡）

   我手上的是Raspberry Pi 4b，安装的操作系统是[Debian-Pi-Aarch64](https://github.com/openfans-community-offical/Debian-Pi-Aarch64) - 为全新Raspberry Pi 4B适配的64位操作系统。

   系统镜像：2020-06-17-OPENFANS-Debian-Buster-Desktop-Aarch64-ext4-v2019-2.0-U3-Release.img

   <div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_opencv/4.png" style="zoom:80%;" /></div>

   ```
   链接：https://pan.baidu.com/s/1DfE2V_F2q5Uz0nTzy8G9Cg 
   提取码：ocrd
   ```

   理论上带GUI的操作系统都可以，比如Raspbian-full。 | ***揭秘了揭秘了***  Raspbian-lite操作成功！！！ 

   镜像烧写看这篇教程——[树莓派系统镜像烧写](https://uestcpi.cn/2020/06/24/raspi_os/)

   如果是Raspbian的话，换源教程在这里——[Linux源的基础操作](https://uestcpi.cn/2020/06/27/linux_source/)

   至于这里为什么选择带GUI的桌面系统，总归还是太菜了，后面手动CMake了n次全部失败，所以不得不用到CMake-GUI来协助。

2. 扩展SWAP分区，否则大概率会在编译过程中因为内存不足而爆掉。因为你编译到后面基本上看到的是下面的景象：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_opencv/3.png" style="zoom:80%;" /></div>

```bash
pi@raspbian:~$ free -h
              total        used        free      shared  buff/cache   available
Mem:          1.8Gi       1.3Gi       232Mi       112Mi       258Mi       277Mi
Swap:         3.0Gi       2.2Gi       843Mi
```

SWAP扩展分区教程——[树莓派的SWAP分区扩展](https://uestcpi.cn/2020/06/27/raspi_swap/)

根据我的编译过程来看RAM+SWAP最少需要5GB，建议给足一点，防止中途爆掉。

3. 网络配置（可选）

   建议有软路由的小伙伴先把树莓派接入软路由，因为可能会在国外服务器拉取源代码文件，网络不通畅的话会导致这一过程十分的漫长。

4. 资源预下载

   OpenCV4.3.0和opencv_contrib-4.3.0的安装包大家可从GitHub上搜索下载，如果网络有障碍，可从我的百度网盘下载（同时网盘内也上传了一些CMake易下载失败的文件）：

```
链接：https://pan.baidu.com/s/1DtWG-BlsfSmTypd_2pEcSg 
提取码：p46j 
```

## 开始操作

### Step1 扩展文件分区（Raspbian-lite等）

如果你是新安装的Raspbian操作系统，你应该将文件系统扩展至整张micro-SD卡

```bash
sudo raspi-config
```

你会看到：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_opencv/5.png" style="zoom:100%;" /></div>

选择***“Advanced Options”***

接着：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_opencv/6.png" style="zoom:100%;" /></div>

选择“***A1. Expand File System***”

完成以后重启树莓派

```bash
sudo reboot
```

重启之后使用

```bash
df -h
```

可以看到当前的分区情况：

```bash
pi@raspbian:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            788M     0  788M   0% /dev
tmpfs           185M   20M  166M  11% /run
/dev/mmcblk0p2   29G   12G   16G  43% /
tmpfs           925M  1.4M  924M   1% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           925M     0  925M   0% /sys/fs/cgroup
/dev/mmcblk0p1  252M   89M  163M  36% /boot
tmpfs           185M   20K  185M   1% /run/user/1000
```

### Step2 清理不必要文件和程序

```bash
sudo apt-get purge wolfram-engine
sudo apt-get purge libreoffice*
sudo apt-get clean
sudo apt-get autoremove
```

你可以获得更大的磁盘空间（毕竟安装OpenCV真的非常消耗空间，更何况还有SWAP分区）

### Step3 安装依赖

首先更新源和程序

```bash
sudo apt-get update && sudo apt-get upgrade
```

安装CMake相关构建工具

```bash
sudo apt-get install build-essential cmake pkg-config
sudo apt-get install cmake-gui #（Raspbian-lite不用）
```

图像I/O

```bash
sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
```

视频I/O

```bash
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libxvidcore-dev libx264-dev
```

之后构建会用到的一个模块：

```bash
sudo apt-get install libgtk2.0-dev libgtk-3-dev
```

安装Python2.7和Python3

```bash
sudo apt-get install python2.7-dev python3-dev
```

### Step4 下载/拷贝OpenCV源码

##### （带GUI的操作系统）

如果你在百度云下载了相关文件则把提前下载好的OpenCV文件拷贝到/home/pi文件夹里，并用

```bash
unzip
```

命令解压。

如果网络条件不错当然也可以直接下载：

```bash
cd ~
wget -O opencv.zip https://github.com/Itseez/opencv/archive/4.3.0.zip
unzip opencv.zip
wget -O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/4.3.0.zip
unzip opencv_contrib.zip
```

后面的4.3.0是版本号，可以根据实际的最新版本情况修改。

##### Raspbian-lite

获取源码：

```
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
```

这里需要注意，opencv和contrib必须对应否则编译会失败，因此我此处clone了最新的

### Step5 配置Python

安装Python的包管理器pip

```bash
wget https://bootstrap.pypa.io/get-pip.py
sudo python get-pip.py
sudo python3 get-pip.py
```

安装NumPy库

网络条件好用这个命令：

```bash
pip install numpy
```

网络条件不好用这个命令：

```
pip install numpy -i https://pypi.douban.com/simple
```

-i后面是服务器地址，可以根据实际情况切换速度比较快的国内服务器。

### Step6 编译和安装OpenCV

**构建项目**

先创建build文件夹

```
cd ~/opencv-3.3.0/
mkdir build
cd build
```

然后开始构建：

***以下是错误的构建命令***

***以下是错误的构建命令***

***以下是错误的构建命令***

```bash
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D OPENCV_EXTRA_MODULES_PATH=/home/pi/opencv_contrib-4.3.0/modules \
    -D BUILD_EXAMPLES=ON ..
```

```bash
cmake -D CMAKE_BUILD_TYPE=Release \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D OPENCV_EXTRA_MODULES=/home/pi/opencv_contrib-4.3.0/modules \
    PYTHON3_EXECUTABLE = /usr/bin/python3 \
    PYTHON_INCLUDE_DIR = /usr/include/python3.7 \
    PYTHON_INCLUDE_DIR2 = /usr/include/arm-linux-gnueabihf \
    -D BUILD_EXAMPLES=ON ..
```

```bash
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D OPENCV_EXTRA_MODULES_PATH=/home/pi/opencv_contrib-4.3.0/modules \
    -D ENABLE_NEON=ON \
    -D ENABLE_VFPV3=ON \
    -D WITH_GTK=ON \
    -D BUILD_TESTS=OFF \
    -D INSTALL_C_EXAMPLES=OFF \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D BUILD_EXAMPLES=OFF ..
```

以上命令要么构建失败，要么编译到一半报错，具体原因由于水平有限我也不清楚。

##### （带GUI的操作系统）

无奈之下只好选择CMake-GUI来完成构建：

用**VNC**连接树莓派，在远程桌面的终端中输入：

```
cmake-gui
```

启动cmake-gui如下：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_opencv/2.png" style="zoom:100%;" /></div>

在Where is the source code:选择opencv-4.3.0目录位置(我的位置为/home/pi/opencv-4.3.0)，在Where to build the binaries:选择build位置(我的位置为/home/pi/opencv-4.3.0/build)。
**选择完毕点击Configure，配置完毕会出现很多 *红色* 项目**

- 找到BUILD_opencv_world，后面的方框点上勾；
- 找到在CMAKE_BUILD_TYPE 值处输入RELEASE，其他保持不变；
- 找到OPENCV_ENABLE_NONFREE，在后面的方框点上勾（有的算法有专利，不点这个不能用，比如sift）
- 找到OPENCV_EXTRA_MODULES_PATH，选择opencv_contrib文件夹中的modules文件夹，注意是modules文件夹(我的路径是/home/pi/opencv_contrib/modules)。

完成以上工作再点击Configure，耐心等一等，如果上面还有红色的再点击Configure，直到没有红色为止。

Configure完成之后点击Generate，等一等即可。

正常情况下，等Generate完成之后，我们可以直接在build目录下输入命令：

```bash
make -j4
```

***-j4***命令是多核编译参数，这里的4就是4核的意思，CPU4核拉满能够提高一些效率。

##### （Raspbian-lite手动构建）

之前的cmake代码不成功大概是以下原因：

Python的libraries，numpy，packages Path都没有，说明Python环境不正常，不会生成Python的opencv库，到时候会报ImportError: No module named cv2的错误。

1. 指定参数的时候有些没加-D。 
2. 没有启用BUILD_opencv_python3。 
3. 部分变量名错了

打开opencv的CMAKELists.txt,发现许多变量名是错的，下面是opencv的CMAKELists.txt部分代码。

环境Python3.7 & Python2.7

```cmake
cmake -D BUILD_opencv_python3=YES \
-D CMAKE_BUILD_TYPE=Release \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D OPENCV_EXTRA_MODULES=../../opencv_contrib/modules \
-D PYTHON3_LIBRARIES=/usr/lib/arm-linux-gnueabihf/libpython3.7m.so \
-D PYTHON3_NUMPY_INCLUDE_DIRS=/usr/lib/python3/dist-packages/numpy/core/include/numpy/ ..
```

构建成功如图：

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_opencv/cmake.png" style="zoom:100%;" /></div>

接着我们可以在build目录下输入命令：

```bash
make -j4
```

***-j4***命令是多核编译参数，这里的4就是4核的意思，CPU4核拉满能够提高一些效率。

随即开始编译。

**请做好准备，这是一个，非常，非常，非常漫长的过程！！！！！！！！**

**请做好准备，这是一个，非常，非常，非常漫长的过程！！！！！！！！**

**请做好准备，这是一个，非常，非常，非常漫长的过程！！！！！！！！**

有Building CXX object modules/python2/CMakeFiles/opencv_python2.dir/_/src2/cv2.cpp.o

和Building CXX object modules/python3/CMakeFiles/opencv_python3.dir/_/src2/cv2.cpp.o两部分编译得有20分钟，一度以为又死机了，实际上不是，耐心等待，树莓派编译特别慢。


**在这个过程当中你可能会遇到不少问题，*常见问题写在了末尾。***

编译不出错完成之后，在build目录下输入命令：

```bash
sudo make install
```

### Step7 配置环境

安装完需要配置环境，树莓派可以使用vim，nano等命令进行编辑，也可以安装gedit进行编辑，本菜鸟能力不强，还是喜欢用gedit，就安装了个gedit。输入命令如下：

```bash
sudo gedit /etc/ld.so.conf.d/opencv.conf
```

打开文件之后是空的，输入以下内容：

```
/usr/local/lib
```

保存后退出，再输入命令：

```bash
sudo ldconfig
```

更改环境变量，输入命令：

```bash
sudo gedit /etc/bash.bashrc
```

打开之后，在文件最后面输入以下内容：

```
PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig 
export PKG_CONFIG_PATH
```

保存退出，opencv基本上就安装完成了。

### Step8 检测安装是否成功

#### Python2.7检查

终端按照如下操作：

```bash
pi@raspbian:~/opencv-4.3.0/build$ python
Python 2.7.16 (default, Oct 10 2019, 22:02:15) 
[GCC 8.3.0] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'4.3.0'
```

如果看到了版本号则表示成功。

#### Python3.7检查

终端按照如下操作：

```bash
pi@raspbian:~/opencv-4.3.0/build$ python3
Python 3.7.3 (default, Dec 20 2019, 18:57:59) 
[GCC 8.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'4.3.0'
```

如果看到了版本号则表示成功。

**到此为止安装基本成功**

## 后续工作

1. 删除安装文件，清理空间。

2. **别忘记把SWAP分区改回来！！！**

   如果你跳过这一步的话，你的SD卡大概率过不了多久就会被写废！！！

   调整dphys-swapfile：

   ```bash
   sudo nano /etc/dphys-swapfile
   ```

   修改文件中以下项目为：

   ```
   # set size to absolute value, leaving empty (default) then uses computed value
   # you most likely don't want this, unless you have an special disk situation
   CONF_SWAPSIZE=100
   # CONF_SWAPSIZE=4096
   # CONF_MAXSWAP=4096
   ```

```
   
重启服务：
   
   ```bash
   sudo /etc/init.d/dphys-swapfile restart
```

   你的SWAP分区将从4096MB变回100MB





## opencv_config安装问题

### 报错1：没有那个文件或目录

**编译OpenCV提示opencv_contrib缺少boostdesc_bgm.i等文件**

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_opencv/1.png" style="zoom:80%;" /></div>



解决方法：

查看 build 文件夹下的日志文件 CMakeDownloadLog.txt，在日志文件CMakeDownloadLog.txt中搜索 boostdesc_bgm.i 关键词。日志文件里就有它的下载地址，到指定位置下载即可。https://github.com/opencv/opencv_contrib/issues/1301，点开上面这个网址往下拉，有人提供了缺失的各个文件的链接，点击保存. 或者直接在这个网页里搜索 BenbenIO 这个用户的回答。
下载后，直接拷贝源码并生成同名文件，放在 **opencv_contrib/modules/xfeatures2d/src/** 路径下即可。

### **报错2：**编译时头文件不对

**fatal error: features2d/test/test_detectors_regression.impl.hpp: 没有那个文件或目录**

**#include "features2d/test/test_detectors_regression.impl.hpp"**

<div align=center><img src="https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/raspi_opencv/7.png" style="zoom:80%;" /></div>



**头文件include地址不对，解决方法如下：**

将opencv-4.1.0/modules/**features2d**/test/文件下的

```
test_descriptors_invariance.impl.hpp
test_descriptors_regression.impl.hpp
test_detectors_invariance.impl.hpp
test_detectors_regression.impl.hpp
test_invariance_utils.hpp
```

拷贝到opencv_contrib-4.1.0/modules/**xfeatures2d**/test/文件下。

同时，将opencv_contrib-4.1.0/modules/**xfeatures2d**/test/test_features2d.cpp文件下的

```c
#include "features2d/test/test_detectors_regression.impl.hpp"
#include "features2d/test/test_descriptors_regression.impl.hpp"
```

改成：

```
#include "test_detectors_regression.impl.hpp"
#include "test_descriptors_regression.impl.hpp"
```

将opencv_contrib-4.1.0/modules/**xfeatures2d**/test/test_rotation_and_scale_invariance.cpp文件下的

```
#include "features2d/test/test_detectors_invariance.impl.hpp" 
#include "features2d/test/test_descriptors_invariance.impl.hpp"
```

改成：

```
#include "test_detectors_invariance.impl.hpp"
#include "test_descriptors_invariance.impl.hpp"
```

