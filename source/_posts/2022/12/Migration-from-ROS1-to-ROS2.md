---
title: 将ros package从ros1迁移至ros2
category_bar: true
date: 2022-12-12 03:26:09
tags: ['robotic', 'drone', 'ROS', 'catkin', 'colcon']
categories:
- 技术
- Robotics
- System
---
总体来说从ros1迁移至ros2的过程还是比较简单的，基本上是在ros1的package基础上针对ros2的差异进行一些改动和适配。

catkin package与colcon package的差异主要体现在以下几个方面：

1. 构建系统：catkin->colcon
2. ros组件：roscpp->rclcpp
3. 源码：部分模块命名空间有区别

## 迁移步骤

以vins-fusion中的loop_fusion模块为例

### package.xml

ros2对package.xml的格式有新的要求，不在支持format 1，需要将格式更新到format 2及以上，具体体现如下：

ros1

```
<?xml version="1.0"?>
<package>
  ...
</package>
```

ros2

```
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  ...
</package>
```

buildtool_depend字段从catkin更换为ament_cmake，build_type字段也更换为ament_cmake，具体体现如下：

```
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  ...
  ...
  <buildtool_depend>ament_cmake</buildtool_depend>
  ...
  ...
  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>
```

depend字段中的roscpp也需要更换为rclcpp，rospy需要更换为rclpy,如下：

```
<depend>rclcpp</depend>
<depend>rclpy</depend>
```

ros2对组件中的message package也有一些调整，需要指出对相应的msg的依赖，如下：

```
  <depend>std_msgs</depend>
  <depend>sensor_msgs</depend>
  <depend>visualization_msgs</depend>
  <depend>nav_msgs</depend>
```



完整的package.xml如下：

```
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>loop_fusion</name>
  <version>0.0.0</version>
  <description>loop_fusion package</description>
  <maintainer email="tony-ws@todo.todo">tony-ws</maintainer>
  <license>TODO: License declaration</license>

  <buildtool_depend>ament_cmake</buildtool_depend>

  <depend>rclcpp</depend>
  <depend>ament_index_cpp</depend>
  <depend>std_msgs</depend>
  <depend>sensor_msgs</depend>
  <depend>visualization_msgs</depend>
  <depend>nav_msgs</depend>
  <depend>cv_bridge</depend>
  <depend>camera_models</depend>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>

```


### CMakeLists.txt

cmake最低版本要求需要3.5以上：

```
cmake_minimum_required(VERSION 3.10)
```

C/C++代码需要依照C14标准，否则会报编译错误：

```
set(CMAKE_CXX_STANDARD 14)
```

改变对ros组件依赖的查找方式：

对比ros1与ros2：

```
# ROS1
find_package(catkin REQUIRED COMPONENTS
    roscpp
    std_msgs
    nav_msgs
    camera_models
    cv_bridge
    roslib
    )

# ROS2
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(ament_index_cpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(visualization_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(camera_models REQUIRED)
```

在include_directories中去除对catkin头文件依赖。

通过ament_target_dependencies描述ros2依赖：

```
ament_target_dependencies(loop_fusion_node camera_models rclcpp ament_index_cpp std_msgs sensor_msgs visualization_msgs nav_msgs cv_bridge OpenCV)
```

安装编译产物：

```
install(
  TARGETS loop_fusion_node
  DESTINATION lib/${PROJECT_NAME}
)
install(TARGETS
   loop_fusion_node
   DESTINATION lib/${PROJECT_NAME}
)
install(TARGETS loop_fusion_node
   ARCHIVE DESTINATION lib
   LIBRARY DESTINATION lib
   RUNTIME DESTINATION bin
)
install(TARGETS loop_fusion_node
   RUNTIME DESTINATION lib/${PROJECT_NAME}
)
```

最后把catkin_package换成ament_package:

```
ament_package()
```

完整的CMakeLists.txt如下：

```
cmake_minimum_required(VERSION 3.10)
project(loop_fusion)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -g")

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(ament_index_cpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(visualization_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(camera_models REQUIRED)
find_package(OpenCV)
find_package(Ceres REQUIRED)

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
find_package(Eigen3)
include_directories(${CERES_INCLUDE_DIRS}  ${EIGEN3_INCLUDE_DIR})

add_executable(loop_fusion_node
    src/pose_graph_node.cpp
    src/pose_graph.cpp
    src/keyframe.cpp
    src/utility/CameraPoseVisualization.cpp
    src/ThirdParty/DBoW/BowVector.cpp
    src/ThirdParty/DBoW/FBrief.cpp
    src/ThirdParty/DBoW/FeatureVector.cpp
    src/ThirdParty/DBoW/QueryResults.cpp
    src/ThirdParty/DBoW/ScoringObject.cpp
    src/ThirdParty/DUtils/Random.cpp
    src/ThirdParty/DUtils/Timestamp.cpp
    src/ThirdParty/DVision/BRIEF.cpp
    src/ThirdParty/VocabularyBinary.cpp
    )

ament_target_dependencies(loop_fusion_node camera_models rclcpp ament_index_cpp std_msgs sensor_msgs visualization_msgs nav_msgs cv_bridge OpenCV)
target_link_libraries(loop_fusion_node ${OpenCV_LIBS} ${CERES_LIBRARIES})

# Install nodes
install(
  TARGETS loop_fusion_node
  DESTINATION lib/${PROJECT_NAME}
)
install(TARGETS
   loop_fusion_node
   DESTINATION lib/${PROJECT_NAME}
)
install(TARGETS loop_fusion_node
   ARCHIVE DESTINATION lib
   LIBRARY DESTINATION lib
   RUNTIME DESTINATION bin
)
install(TARGETS loop_fusion_node
   RUNTIME DESTINATION lib/${PROJECT_NAME}
)

ament_package()
```

### 源码修改

#### 头文件

ros2使用rclcpp替换了roscpp接口，源码里面的改动如下：

```
//#include "ros/ros.h"
#include "rclcpp/rclcpp.hpp"
```

对于msg的依赖需要加一级msg目录，例如：

```
//#include "std_msgs/String.h"
#include "std_msgs/msg/string.hpp"

//#include "nav_msgs/path.hpp"
#include nav_msgs/msg/path.hpp"

//#include "geometry_msgs/point_stamped.h"
#include "geometry_msgs/msg/point_stamped.h"

//#include "nav_msgs/odometry.h"
#include "nav_msgs/msg/odometry.h"
```

#### 接口调用

rclcpp不是将节点的名称传递给库初始化调用，而是进行初始化，然后将节点名称传递给节点对象的构造器：（auto关键字依赖C14标准编译器）

```
//  ros::init(argc, argv, "talker");
//  ros::NodeHandle n;
    rclcpp::init(argc, argv);
    auto node = rclcpp::Node::make_shared("talker");
```

publisher和rate对象的命名空间发生了变化：

```
//  ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);
//  ros::Rate loop_rate(10);
  auto chatter_pub = node->create_publisher<std_msgs::msg::String>("chatter",
    1000);
  rclcpp::Rate loop_rate(10);
```

msg相关的模块命名空间也发生了变化，相比ros1多了一层msg：

```
//  std_msgs::String msg;
  std_msgs::msg::String msg;
//  nav_msgs::Path;
  nav_msgs::msg::Path
```

还有一些别的ros接口稍微有点小变化：

```
//    while (ros::ok())
    while (rclcpp::ok())
//    ros::spinOnce();
    rclcpp::spin_some(node);
//    ROS_INFO("%s", msg.data.c_str());
    RCLCPP_INFO(node->get_logger(), "%s\n", msg.data.c_str());

```

### Colcon build

```
cd $(PATH_TO_YOUR_ROS2_WS)/src
git clone -b cuda https://github.com/Jason-xy/VINS-Fusion.git
cd ..
colcon build --symlink-install --allow-overriding cv_bridge
```


## 参考文档

[ROS迁移指南官方文档](https://docs.ros.org/en/foxy/The-ROS2-Project/Contributing/Migration-Guide.html)

实例代码：VINS-Fusion [ROS1](https://github.dev/Jason-xy/VINS-Fusion/tree/master/loop_fusion) [ROS2](https://github.dev/Jason-xy/VINS-Fusion/tree/cuda/loop_fusion)
