---
title: ROS package发布工作流
category_bar: true
date: 2022-12-15 09:09:59
tags: ['robotic', 'drone', 'ROS', 'catkin', 'colcon']
categories:
- 技术
- Robotics
- Syste
---
对于API相对稳定的ROS package通常会采用二进制发布的方式向社区项目提供支持，这里的二进制发布有许多可选的流程，最终的包形态也不尽相同，不同的包发布方式也各有优劣。当前较为主流的包发布方式还是以ROS社区的bloom+ros_buildfarm为主，但是很多特定场景下开发者们也可能通过snapshot/bundle或者container的形式进行发布。最近为了解决上述发布形式的一些问题也有开发者提出了基于nix的发布方式，以及结合C++包管理工具Conan进行发布的一系列方法。

## 现状分析

### 当前常见包发布流程特点

| bloom+ros_buildfarm                  | snapshot/bundle/container          |
| ------------------------------------ | ---------------------------------- |
| 支持增量构建                         | 环境版本化，可回退                 |
| 强制要求每次发布打tag                | 编译资源、存储资源网络资源消耗巨大 |
| 对于下游项目缺乏系统集成后的版本管理 | 没有统一工具集                     |
| 无回退流程                           |                                    |
| 发布为.deb存入apt源                  |                                    |

上述发布流程中大部分情况只对模块本身进行自测，将集成测试推后，模块自身API的稳定性和向前兼容性是二进制版本发布的前提。

### 可能的包发布流程需求

* 支持增量构建
* 环境版本化，可回退
* 统一的框架和工具集

### 可能难以解决的问题

对于二进制集成的迭代模式，下游项目通常选择可用版本进行集成，放在末端项目级别来考虑的话如果涉及对已发布包的变更和再次发布的需求，往往相比于直接源码集成迭代周期会被出包+验证流程拉长。

## ROS Build Farm

ROS Build Farm 是支持 ROS 生态系统的重要基础设施之一。它提供源代码和二进制包的构建、持续集成、测试和分析。它旨在满足公共 ROS 社区的需求，并且在 [http://build.ros.org/](http://build.ros.org/) 上有一个开源包的托管实例。

ROS Build Farm 还旨在支持部署自定义构建农场。这对于发布闭源包、为官方 ROS 构建农场未提供的平台和架构构建或根据特定需求定制过程的场景很有用。

构建的各项工作主要由 Jenkins 实例完成。当然还有其他人将这些工具与 Build Bot 一起使用。ROS Build Farm 的设计目标是大部分基础设施是模块化的和可重用的。

### release准备

更新源码仓库change log：`b`

更新源码仓库版本、tag信息，并push至release仓库： `catkin_prepare_release`

更新release仓库，准备进行新的一次release：`bloom-release`

bloom会自动向rosdistro仓库提交pr，以vision_opencv的一次release为例：

> ## vision_opencv (foxy) - 3.0.7-1
>
> The packages in the `vision_opencv` repository were released into the `foxy` distro by running `/usr/bin/bloom-release --rosdistro foxy vision_opencv` on `Sat, 15 Oct 2022 13:19:59 -0000`
>
> **These packages were released:**
>
> * `cv_bridge`
> * `image_geometry`
> * `vision_opencv`
>
> **These packages were explicitly ignored:**
>
> * `opencv_tests`
>
> Version of package(s) in repository `vision_opencv`:
>
> * upstream repository: [https://github.com/ros-perception/vision_opencv.git](https://github.com/ros-perception/vision_opencv.git)
> * release repository: [https://github.com/ros2-gbp/vision_opencv-release.git](https://github.com/ros2-gbp/vision_opencv-release.git)
> * rosdistro version: `3.0.6-1`
> * old version: `3.0.6-1`
> * new version: `3.0.7-1`
>
> **Versions of tools used:**
>
> * bloom version:  `0.11.2`
> * catkin_pkg version: `0.5.2`
> * rosdep version: `0.22.1`
> * rosdistro version: `0.9.0`
> * vcstools version: `0.1.42`

### 编译农场

**rosdistro_cache job**

每五分钟同步一次ros/rosdistro的变更

gzip压缩后的yaml文件包含：

* distribution_file：ros/rosdistro中的distribution_file拷贝
* release_repo_package_xmls：当前发布的包版本中的 package.xml 内容
* source_repo_package_xmls：空，获取源package.xml比较棘手
* name：分发包名
* type, version：REP-141 格式的信息

job内容：

1. 生成一个新的 rosdistro 缓存文件并将其上传到存储库主机
2. 每五分钟运行一次
3. 通过HTTPS轮询distribution.yaml 更改
4. 由于 CDN 缓存，更改有时会滞后
5. 触发器为更新的包重新配置作业

**reconfigure job**

rosdistro_cache job拉取变更后将会触发包变更的版本分发配置

* 每次job对应ros_buildfarm_config中的一个构建文件
* 使用发布构建配置和ros_buildfarm模板为每个包生成一个sourcepkg和binarypkg job
* 当由rosdistro缓存触发以更新更改的包时运行
* 每24小时运行一次以保持job配置最新

**release trigger job**

* 每次job对应ros_buildfarm_config中的一个构建文件
* 将构建存储库中的包与基于rosdistro缓存的预期包进行比较，并触发sourcepkg和binarypkg job来实施变更

job参数

missing only – 为存储库中不存在的包触发job

source only – 仅触发源码包的job； 当需要在重建二进制文件之前更新源码包时很有用

packaging job

每15分钟执行一次发生变更的包的打包

**source package job**

* 根据ros2-gbp仓库中的平台元数据拉取包的源码，并创建不同平台的源码包（Ubuntu/Fedora），同时上传至构建仓库
* 不使用colcon、rosdep、bloom 等ROS 工具。 此时所有包信息都已呈现为特定于发行版的格式
* 成功构建将触发二进制包构建，用于从创建的源码包生成的所有二进制包
* 导入新的源码包时删除旧的二进制包

**binary package job**

* 从构建存储库中获取源代码
* 安装构建依赖项
* 将创建的二进制包上传到构建存储库
* 不使用colcon、rosdep、bloom 等ROS 工具。 此时所有包信息都已呈现为特定于发行版的格式
* 成功的构建将使新构建的二进制包导入构建存储库并使其下游的所有包无效

**包失效**

* 删除所有递归依赖于新重建包的包
* 防止生产环境中的ABI被破坏
* API更改时对下游包的即时反馈

**同步任务**

**sync to testing job**

* 从测试存储库中删除所有与job rosdistro、平台版本和体系结构相匹配的 ROS 包，以及相关的源码包
* 从符合相同条件的构建库中导入所有ROS包
* 当不再有出包的job时，包将被删除
* 所有出二进制包和源代码包job都会block测试job，因此这些job只在完全重新构建时执行
* 设置了自动阈值以防止缺陷版本清除测试存储库

**sync to main job**

* 从main存储库中删除所有与job rosdistro、平台版本和体系结构相匹配的 ROS 包，以及相关的源码包
* 只会由ros核心团队手动执行

**发布流程全景图**

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/PicGo/202212182314038.png)
