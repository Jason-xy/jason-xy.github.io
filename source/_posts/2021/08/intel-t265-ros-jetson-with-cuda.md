---
title: Intel T265 + ROS视觉里程计搭建——Jetson平台
category_bar: true
date: 2021-08-18 9:26:56
tags: ['realsense', 't265', 'ros', 'jetson', 'cuda']
categories:
- 技术
- Robotics
- ENV
---

## OpenCV4 for CUDA安装

OpenCV提供图像处理过程中的基础API，所以首先完成OpenCV4的构建。为了利用Jetson平台拥有CUDA加速的优势需要先安装OpenCV的CUDA版本。（Jetpack默认为非CUDA版本）

> 如果是Jetson Nano平台需要先扩大Swap分区至4G以上，避免因为内存不足终止编译。

可以使用Jtop工具扩展Swap分区。

### 更新安装包

```bash
sudo apt update
sudo apt install -y build-essential cmake git libgtk2.0-dev pkg-config  libswscale-dev libtbb2 libtbb-dev
sudo apt install -y python-dev python3-dev python-numpy python3-numpy
sudo apt install -y curl
```

### 安装视频和图片依赖

```bash
sudo apt install -y  libjpeg-dev libpng-dev libtiff-dev
sudo apt install -y libavcodec-dev libavformat-dev
sudo apt install -y libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev
sudo apt install -y libv4l-dev v4l-utils qv4l2 v4l2ucp libdc1394-22-dev
```

### 克隆OpenCV和OpenCV_Contrib源码

```bash
curl -L https://github.com/opencv/opencv/archive/4.5.3.zip -o opencv-4.5.3.zip
curl -L https://github.com/opencv/opencv_contrib/archive/4.5.3.zip -o opencv_contrib-4.5.3.zip
```

### 解压缩包

```bash
unzip opencv-4.5.3.zip 
unzip opencv_contrib-4.5.3.zip 
cd opencv-4.5.3/
mkdir build
cd build/
```

### 使用Cmake编译工程

```
cmake     -D WITH_CUDA=ON \
        -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-4.5.3/modules \
        -D WITH_GSTREAMER=ON \
        -D WITH_LIBV4L=ON \
        -D BUILD_opencv_python2=ON \
        -D BUILD_opencv_python3=ON \
        -D BUILD_TESTS=OFF \
        -D BUILD_PERF_TESTS=OFF \
        -D BUILD_EXAMPLES=OFF \
        -D CMAKE_BUILD_TYPE=RELEASE \
        -D CMAKE_INSTALL_PREFIX=/usr/local ..
```

### 编译和安装

```bash
make -j6 #根据CPU核心来填数字
sudo make install
```

### 查看是否安装成功

![image-20210817224114622](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/17/image-20210817224114622.png)

## 安装ROS全家桶

### 添加软件源

```bash
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

### 添加密钥

```bash
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```

### 更新软件源

```bash
sudo apt-get update
```

### 安装ROS

```bash
sudo apt install ros-melodic-desktop-full
```

### 配置环境变量

```bash
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### 安装额外依赖

```bash
sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
```

### 安装rosdep

```bash
sudo apt install python-rosdep
```

### 初始化rosdep

```bash
sudo rosdep init
rosdep update
```

注：确保已接入外网环境

## 安装Intel® RealSense™ SDK

感谢Jetsonhacks提供的安装脚本：https://github.com/jetsonhacks/installRealSenseSDK.git

请先克隆仓库，然后进行修改。

```bash
git clone https://github.com/jetsonhacks/installRealSenseSDK.git
```

根据具体情况将版本做了一些修改：

```shell
#!/bin/bash
# Builds the Intel Realsense library librealsense on a Jetson NX
# Copyright (c) 2016-21 Jetsonhacks & Jason_xy
# Jetson NX; L4T 32.4.3[Jetpack 4.4]
#2021-8-17
LIBREALSENSE_DIRECTORY=${HOME}/librealsense
LIBREALSENSE_VERSION=v2.48.0
INSTALL_DIR=$PWD
NVCC_PATH=/usr/local/cuda-10.2/bin/nvcc
```

### 执行脚本

```bash
./buildLibrealsense.sh 
```

### 执行结果

![image-20210818092344805](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/18/image-20210818092344805.png)

## 安装realsense-ros

### 创建工作区

```bash
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src/
```

### 克隆源码仓库

```bash
git clone https://github.com/IntelRealSense/realsense-ros.git
cd realsense-ros/
git checkout `git tag | sort -V | grep -P "^2.\d+\.\d+" | tail -1`
cd ..
```

### 构建模块

```
catkin_init_workspace
cd ..
catkin_make clean
```

#### **解决问题**

##### **cv_bridge**

![image-20210818002132664](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/18/image-20210818002132664.png)

修改cv_bridge配置文件，将API对接到安装的OpenCV4 with CUDA

修改文件`/opt/ros/melodic/share/cv_bridge/cmake/cv_bridgeConfig.cmake`

![image-20210818002116799](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/18/image-20210818002116799.png)

##### ddynamic_reconfigure

![image-20210818002145740](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/18/image-20210818002145740.png)

安装ddynamic_reconfigure：

```bash
sudo apt-get install ros-melodic-ddynamic-reconfigure
```

#### 继续构建

```bash
catkin_make -DCATKIN_ENABLE_TESTING=False -DCMAKE_BUILD_TYPE=Release
catkin_make install
```

### 添加环境变量

```bash
echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### 成功运行

![image-20210818005040499](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/18/image-20210818005040499.png)

![image-20210818010317263](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/18/image-20210818010317263.png)

![image-20210818092410635](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/18/image-20210818092410635.png)

