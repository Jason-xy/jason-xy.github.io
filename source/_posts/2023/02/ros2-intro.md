---
title: Introduction of ROS2
category_bar: true
date: 2023-02-08 03:40:37
tags: ['ROS2', 'colcon']
categories:
- 技术
- Robotics
- System
---
当前ROS2已经成为了应用最为广泛的机器人应用程序开发SDK之一，它拥有先进的分布式架构、更好的可靠性、实时性以及嵌入式设备支持。ROS2的诞生主要源于以下三个方面：

1. ROS1的历史遗留问题
2. 机器人技术经过多年的发展产生了新的需求
3. 在ROS发展的时间里产生了很多新的技术

# History from ROS to ROS2

![](https://robots.ieee.org/robots/pr2/Photos/SD/pr2-photo1-full.jpg)

ROS最初的设计是为了给使用Willow Garage PR2机器人进行研究的项目或实验室提供便于使用的软件开发工具。为了提高系统的通用性，开发团队在抽象层花了很多功夫，特别的消息接口的设计与实现上。因此ROS具有了极高的复用性，大部分组件也可以用在其他大部分的机器人应用程序中。但是在当时ROS诞生的时期，也主要是面向学术研究使用，研究人员的主要需求也就如下一些内容：

1. 应用于单个机器人
2. 拥有工作站级别的计算资源
3. 基本没有实时性要求（有实时性要求的组件会有特定的解决方案）
4. 高性能的网络连接能力（大带宽的有线或近距离无线连接）
5. 主要应用于学术研究，最大化灵活性，没有任何限制和约束

这些特性在ROS中都有着不错的实现，能比较完美地满足之前所提出的需求。但是ROS得发展远远超出了开发团队得预期，无论是轮式机器人、足式机器人甚至是工业机械臂等商业产品和项目都在应用ROS框架进行软件开发。随着应用场景的日益增加，也就伴随了新需求和新技术。

如今新的场景与需求：

1. 集群机器人开发（当前也有许多开发者使用ROS进行集群开发，但是业内目前没有统一的标准，大部分开发者也只是在ROS当前的单master节点上进行hack）
2. 对于嵌入式设备的支持（让裸板的嵌入式设备原生接入ROS，而不是通过上位机的驱动接入）
3. 实时性要求（在ROS上原生支持实时控制，而不用像之前一样通过特定的组件）
4. 非理想网络条件（用来应对延迟、丢包、断连的场景）
5. 应对生产环境（更好的稳定性和真实环境中的鲁棒性）
6. 构建系统的规范与限制（在保持灵活性的同时让系统或配置更有条理）

# New technologies

ROS2依然是围绕着最核心的匿名消息发布和订阅机制进行起草的，在ROS1的基础上引入了许多新技术来满足新的需求，例如：

* Zeroconf（零网络配置，用于快速组网）
* Protocol Buffers（跨平台序列化数据结构的协议）
* [ZeroMQ](https://design.ros2.org/articles/ros_with_zeromq.html) (and the other MQs)（异步消息传递库，应用于分布式或并发应用程序）
* Redis（分布式高性能缓存服务）
* WebSockets
* [DDS](https://design.ros2.org/articles/ros_on_dds.html) (Data Distribution Service)（实时数据分发服务）

# Build Tool

在ROS生态中，应用程序通常被分割为许多软件包，开发人员同时处理多个软件包的场景也是很常见的。这与传统的软件开发流程中开发者只面对一个软件包，并一次性提供所有需要依赖的软件包（这部分依赖通常没有迭代）的场景形成了鲜明对比。

构建一组软件包的“手动”方法包括逐一构建所有软件包。对于每个软件包如何管理相互之间的依赖关系，如何设置环境以构建软件包，以及如何在之后设置环境以使用软件包。如果没有自动化该过程的工具，这样的工作流程在规模化开发时是不切实际的。

在ROS1中有多个工具提供以上流程的自动化支持，即 `catkin_make`、`catkin_make_isolated`和 `catkin_tools`。对于ROS 2，提供此功能的构建工具称为 `ament_tools`。

## Build tool & build system

构建工具在一组软件包上运行，它确定依赖关系图，并按拓扑顺序为每个包调用特定的构建系统。ROS所使用的构建工具有 `catkin_make`、`catkin_make_isolated`、`catkin_tools`和 `ament_tools`。

构建系统在单个软件包上运行。例如 `Make`、`CMake`、Python `Python setuptools`。例如cmake软件包通过以下流程完成构建：`cmake`，`make`，`make install`。

## Goals of ament_tools

统一构建工具的目标是构建一组具有单向依赖的软件包。它应该适用于ROS 1软件包以及在其清单文件（package.xml）中提供必要信息的ROS 2软件包。它还应该适用于本身不提供清单文件的软件包，因为必要的元信息可以推断或外部提供。这将允许将构建工具用于非ROS软件包，例如Gazebo的某些插件和SDFormat格式的地图信息。

## Build pipeline of ament_tools

初始化构建环境⬇️

识别并组织不同的构建系统⬇️

计算软件包依赖拓扑⬇️

调用构建系统完成构建⬇️

## Related topics

超出构建工具之外的能力

- 应对包含多种构建系统的工程
- 拉取源代码 [rosinstall_generator](http://wiki.ros.org/rosinstall_generator)
- 安装依赖[rosdep](http://wiki.ros.org/rosdep)
- 创建和发布二进制包 [bloom](http://wiki.ros.org/bloom)

# DDS

## What is DDS

DDS提供了一种发布-订阅传输，这与 ROS 的发布-订阅传输非常相似。DDS使用对象管理组 (OMG)定义的“接口描述语言(IDL)”来进行消息定义和序列化。
DDS默认提供了发现系统，以支持任意两个节点的通讯而不需要ROS master这样的工具。这样使得系统拥有分布式特性，具有更好的容错性。当前DDS已经应用在了战舰、航空器、空间站等重要的设施中，有良好的可靠性和灵活性。

## DDS in ROS2

但据开发者而言DDS的可靠性和灵活性是以复杂性为代价的，ROS2的主要工作就是解决API和DDS配置的复杂性。ROS2的目标就是隐藏DDS的实现细节以及特定的接口和消息定义，提供DDS的节点发现、发布-订阅，以及底层消息序列化。这使得从用户的角度而言隐藏了DDS的绝大部分复杂性，与ROS1的接口调用方式相似。同时ROS2也为部分具有特殊需求的用户提供了对底层DDS接口的访问路径。
![](https://design.ros2.org/img/ros_on_dds/api_levels.png)

## Discovery system

DDS通过发现系统完全取代了ROS master，ROS本身只需要通过DDS API来获取节点列表，而不依赖于master。因此在默认情况下DDS是完全分布式的，不存在系统各部分相互通信时出现中心故障点。

## More

* DDSI-RTPS（实时消息通讯协议）
* shared-memory transport
* .msg -> .idl
  ...

# Compared with ROS1

|                | ROS1                                                                                                                         | ROS2                                                                                                                                             |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| 操作系统       | 最初仅支持应用于Linux操作系统（之后官方也有提供Windows的版本）                                                               | 完全跨平台，支持Linux、Windows、MacOS甚至RTOS                                                                                                    |
| C++标准        | C++03                                                                                                                        | 主要是C++11和14，之后会逐步迁移到C++17                                                                                                           |
| 构建系统       | catkin                                                                                                                       | colcon（ament_cmake）原生支持除cmake外的一些构建系统                                                                                             |
| isolated build | 默认整体构建，可选是否单独构建某一个软件包（每个包需要保证依赖完整，同时共享命名空间容易造成命名冲突）                       | 仅支持单独构建每一个包                                                                                                                           |
| 中间件         | 采用TCP/IP协议作为通讯标准                                                                                                   | 使用了实时的通讯框架DDS以满足实时性要求                                                                                                          |
| 应用框架       | 采用单master架构设计，一旦master节点出现问题，整个系统将无法工作![](https://www.circuspi.com/wp-content/uploads/2022/09/1.png) | 采用了分布式的发现机制组网，这样其中的任何一个节点出现异常都不会导致整个系统停止工作![](https://www.circuspi.com/wp-content/uploads/2022/09/2.png) |

# Migrate to ROS2

## Prerequisites & Steps

- [Prerequisites](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing/Migration-Guide.html#prerequisites)
- [Migration steps](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing/Migration-Guide.html#migration-steps)
  - [Package manifests](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing/Migration-Guide.html#package-manifests)
  - [Metapackages](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing/Migration-Guide.html#metapackages)
  - [Message, service, and action definitions](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing/Migration-Guide.html#message-service-and-action-definitions)
  - [Build system](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing/Migration-Guide.html#build-system)
  - [Update source code](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing/Migration-Guide.html#update-source-code)
- [Parameters](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing/Migration-Guide.html#parameters)
- [Launch files](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing/Migration-Guide.html#launch-files)

## Migration example

ROS1: https://github.com/Jason-xy/VINS-Fusion/tree/master

ROS2: https://github.com/Jason-xy/VINS-Fusion/tree/cuda

# Reference

* [Why ROS 2?](https://design.ros2.org/articles/why_ros2.html)
* [A universal build tool](https://design.ros2.org/articles/build_tool.html)
* [Changes between ROS 1 and ROS 2](https://design.ros2.org/articles/changes.html)
* [ROS on DDS](https://design.ros2.org/articles/ros_on_dds.html)
