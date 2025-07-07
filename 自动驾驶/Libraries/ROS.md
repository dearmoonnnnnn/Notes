- [零、问题和概念](#零问题和概念)
  - [资料](#资料)
        - [1. Autolabor教程](#1-autolabor教程)
  - [问题](#问题)
        - [1. 如何知道一个 bag 文件中的数据结构](#1-如何知道一个-bag-文件中的数据结构)
        - [2. 话题和消息的关系](#2-话题和消息的关系)
        - [3. node 和 nodelet 的关系](#3-node-和-nodelet-的关系)
        - [4. 一个节点可以发布或者接收多个主题吗](#4-一个节点可以发布或者接收多个主题吗)
        - [5. nodelet 节点在 launch 中配置的时候，节点所在的包 (pkg) 为什么是 nodelet](#5-nodelet-节点在-launch-中配置的时候节点所在的包-pkg-为什么是-nodelet)
        - [6. 将 melodic 版本下能够跑通的项目迁移到 noetic 版本中](#6-将-melodic-版本下能够跑通的项目迁移到-noetic-版本中)
        - [7. 将`sensor_msg::PointCloud`的数据转为`pcl::PointCloud<pcl::PointXYZ>`的数据，但是前者没有width、height和is\_dense，该怎么填充后者的这些数据字段?](#7-将sensor_msgpointcloud的数据转为pclpointcloudpclpointxyz的数据但是前者没有widthheight和is_dense该怎么填充后者的这些数据字段)
        - [8. 如何修改ROS包的名称](#8-如何修改ros包的名称)
        - [9. 在roslaunch文件中，`<node>`标签中的`name`参数和`type`参数的区别是什么，为什么它们的值一样](#9-在roslaunch文件中node标签中的name参数和type参数的区别是什么为什么它们的值一样)
        - [10. 在 node 节点内设置参数，为什么读取不到?](#10-在-node-节点内设置参数为什么读取不到)
        - [11.  bag 文件中的 %time 和 filed.header.stamp 有什么区别](#11--bag-文件中的-time-和-filedheaderstamp-有什么区别)
  - [概念](#概念)
        - [1、节点](#1节点)
        - [2、ROS Master](#2ros-master)
        - [3、bags](#3bags)
- [一、安装 ROS](#一安装-ros)
  - [1.1 安装](#11-安装)
        - [官方安装方法：](#官方安装方法)
        - [香鱼ros一键安装](#香鱼ros一键安装)
  - [1.2 VScode集成开发环境搭建](#12-vscode集成开发环境搭建)
        - [c\_cpp\_properties.json](#c_cpp_propertiesjson)
        - [settings.json](#settingsjson)
        - [tasks.json](#tasksjson)
- [二、ROS 文件系统](#二ros-文件系统)
- [三、ROS的通信方式](#三ros的通信方式)
  - [2.1 话题通信](#21-话题通信)
    - [2.1.1 理论模型](#211-理论模型)
        - [三个角色](#三个角色)
        - [流程](#流程)
    - [2.1.2 具体示例](#212-具体示例)
        - [发布话题的三个步骤(python)](#发布话题的三个步骤python)
        - [发布话题(C++)](#发布话题c)
        - [订阅话题(C++)](#订阅话题c)
  - [2.2 服务通信](#22-服务通信)
  - [2.3 参数服务器](#23-参数服务器)
        - [举例：](#举例)
- [四、launch文件](#四launch文件)
  - [4.1 概述](#41-概述)
        - [动机](#动机)
        - [概念](#概念-1)
        - [作用](#作用)
  - [4.2 调用 launch 文件](#42-调用-launch-文件)
        - [对于 roscore 的处理](#对于-roscore-的处理)
  - [4.3 常见标签](#43-常见标签)
        - [4.3.1 节点（node）](#431-节点node)
            - [4.3.2 包含其他 launch文件（include）](#432-包含其他-launch文件include)
            - [4.3.3 环境变量（env）](#433-环境变量env)
            - [4.3.4 重新映射（remap）](#434-重新映射remap)
            - [4.3.5 组（group）](#435-组group)
            - [4.3.6 参数服务器参数（param）](#436-参数服务器参数param)
            - [4.3.7 参数文件加载（rosparam）](#437-参数文件加载rosparam)
            - [4.3.8 启动文件参数（arg）](#438-启动文件参数arg)
  - [4.4 可执行文件读取 launch 中的参数](#44-可执行文件读取-launch-中的参数)
- [五、rosbag](#五rosbag)
        - [动机：](#动机-1)
        - [概念](#概念-2)
        - [本质](#本质)
  - [5.1 写 bag 文件(C++)](#51-写-bag-文件c)
  - [5.2 读 bag 文件(C++)](#52-读-bag-文件c)
  - [5.3 rosbag play](#53-rosbag-play)
    - [5.3.1 `--clock`](#531---clock)
    - [5.3.2 `-s`](#532--s)
    - [5.3.3 `--rate`](#533---rate)
    - [5.3.4 `--duration`](#534---duration)
    - [5.3.5 `--topic`](#535---topic)
  - [5.4 bag 文件相关操作](#54-bag-文件相关操作)
    - [5.4.1 融合两个 bag 文件中的特定话题](#541-融合两个-bag-文件中的特定话题)
    - [5.4.2 两个 bag 文件合并为一个，并保留需要的所有话题](#542-两个-bag-文件合并为一个并保留需要的所有话题)
    - [5.4.3 删除/保留 bag 文件中的特定话题](#543-删除保留-bag-文件中的特定话题)
  - [5.5 rosbag record](#55-rosbag-record)
  - [5.6 rosbag filter 提取特定帧](#56-rosbag-filter-提取特定帧)
- [六、PCL库](#六pcl库)
  - [相关资料](#相关资料)
  - [问题：](#问题-1)
        - [1、PCL 的 fromROSMsg() 和 toROSMsg() 不能正确处理 xyz 之外其他 field 的数据长度](#1pcl-的-fromrosmsg-和-torosmsg-不能正确处理-xyz-之外其他-field-的数据长度)
        - [2、将 `pcl::PointCloud<pcl::PointXYZI>` 的数据转换为sensor\_msgs::PointCloud2 后，怎么获取点的强度信息？](#2将-pclpointcloudpclpointxyzi-的数据转换为sensor_msgspointcloud2-后怎么获取点的强度信息)
  - [6.1 表示点云数据的四种方式](#61-表示点云数据的四种方式)
        - [6.1.1、sensor\_msgs::PointCloud —— ROS message，已弃用](#611sensor_msgspointcloud--ros-message已弃用)
        - [6.1.2、sensor\_msgs::PointCloud2 —— ROS message](#612sensor_msgspointcloud2--ros-message)
        - [6.1.3、pcl::PointCloud2 —— PCL 数据结构，主要是为了与 ROS 兼容](#613pclpointcloud2--pcl-数据结构主要是为了与-ros-兼容)
        - [6.1.4、pcl::PointCloud\<T\> —— 标准的 PCL 数据结构](#614pclpointcloudt--标准的-pcl-数据结构)
  - [6.2 同时使用 ROS 和 PCL 时典型的工作流](#62-同时使用-ros-和-pcl-时典型的工作流)
        - [示例代码：](#示例代码)
        - [pcl::fromROSMsg()](#pclfromrosmsg)
        - [pcl::toROSMsg()](#pcltorosmsg)
- [七、从零创建 ROS 工程](#七从零创建-ros-工程)
        - [1、在 ROS 的工作目录下使用 catkin\_create\_pkg 命令创建一个新的 ROS 包。](#1在-ros-的工作目录下使用-catkin_create_pkg-命令创建一个新的-ros-包)
        - [2、编写 C++ 程序（节点）](#2编写-c-程序节点)
        - [3、编辑 CMakeLists.txt 文件](#3编辑-cmakeliststxt-文件)
        - [4、构建工程](#4构建工程)
        - [5、运行 ROS 核心](#5运行-ros-核心)
        - [6、运行节点](#6运行节点)
  - [遇到的问题：](#遇到的问题)
        - [1、执行 rosrun 时显示包找不到](#1执行-rosrun-时显示包找不到)
        - [2、找到了包，但是找不到包中的可执行文件](#2找到了包但是找不到包中的可执行文件)
  - [自定义消息格式](#自定义消息格式)
  - [一个自定义包依赖另一个包](#一个自定义包依赖另一个包)
- [八、nodelet](#八nodelet)
  - [问题](#问题-2)
        - [1、nodelet 定义的三种句柄有什么区别](#1nodelet-定义的三种句柄有什么区别)
- [九、catkin\_make](#九catkin_make)
  - [在一个系统上并行管理和运行同一个项目的不同版本](#在一个系统上并行管理和运行同一个项目的不同版本)
    - [步骤](#步骤)
    - [注意事项](#注意事项)
    - [示例目录结构](#示例目录结构)
- [十、格式转换](#十格式转换)
  - [1、bag 包转 png](#1bag-包转-png)
  - [2、bag 转 pcd](#2bag-转-pcd)
    - [2.1 自定义节点](#21-自定义节点)
    - [2.2 使用 pcl\_ros 提供的节点](#22-使用-pcl_ros-提供的节点)
  - [3、bag 转 txt](#3bag-转-txt)
- [十一、使用参数服务器](#十一使用参数服务器)
  - [1、设置参数](#1设置参数)
        - [通过 launch 文件：](#通过-launch-文件)
        - [通过命令行：](#通过命令行)
  - [2、获取参数](#2获取参数)
- [十二、定时器](#十二定时器)


# 零、问题和概念

## 资料

##### 1. Autolabor教程

 http://www.autolabor.com.cn/book/ROSTutorials/

2、官方文档

## 问题

##### 1. 如何知道一个 bag 文件中的数据结构

1. 使用 rosbag 命令行工具

   1. 查看 bag 文件中的话题和消息数

      ```bash
      rosbag info <your_bag_file.bag>
      ```
   
   
      2. 查看特定话题的消息的内容
   
         ```bash
         rosbag echo -n 1 <your_bag_file.bag> <topic_name>
         ```
   


   3. 查看消息的定义

      ```bash
      rosmsg show <message_type>
      ```

2. 将 bag 文件转换为 txt 文件，用文本编辑器查看，见“十、格式转换
3. 使用 `Foxglove Studio` 工具直接打开

##### 2. 话题和消息的关系

1. **定义**:
   - **话题（Topic）**：是一个命名的通道，节点可以发布消息到这个通道，或从这个通道订阅消息。话题为不同的节点提供了一种进行数据通信的方式。
   - **消息（Message）**：是传输在话题上的数据单元。消息有一个特定的类型，定义了数据的结构，例如整数、浮点数、数组或更复杂的数据结构。

2. **关系**:
   - 每个话题都有一个与之关联的消息**类型**。例如，一个可能的话题是 `/camera/image_raw`，其消息类型可能是 `sensor_msgs/Image`，表示图像数据。
   - 节点可以**发布**一个特定类型的消息到一个话题。
   - 同时，其他节点可以**订阅**这个话题，以接收该类型的消息。
   - 虽然一个话题只能有一个消息类型，但多个不同的节点可以发布和订阅这个话题，允许多对多的通信。

3. **示例**:
   - 一个摄像头节点可能会从摄像头硬件获取图像数据，并发布一个类型为 `sensor_msgs/Image` 的消息到 `/camera/image_raw` 话题。
   - 同时，一个图像处理节点可以订阅 `/camera/image_raw` 话题，接收图像消息并进行处理。
   
4. **工具与命令**:
   - 使用 `rostopic list` 可以列出当前活跃的所有话题。
   - 使用 `rostopic type <topic_name>` 可以获取一个话题的消息类型。
   - 使用 `rosmsg show <message_type>` 可以查看一个特定消息类型的结构。

**总结**：话题是数据传输的通道，而消息是在这些通道上实际传输的数据

##### 3. node 和 nodelet 的关系

1. **Node（节点）**:
   - 在ROS中，节点是一个可执行的过程，它与其他节点使用 ROS 通信机制（如话题、服务和行动）交互。
   - 节点运行在其自己的进程中，有其自己的内存空间。
   - 节点之间的通信涉及进程间通信（IPC），这可能会引入一些延迟。

2. **Nodelet**:
   - `nodelet` 的目标是提供一种方式，允许多个算法在同一个进程中共享资源，尤其是大数据类型（如图像或点云）的资源。
   - `nodelet` 运行在一个共享的进程中，称为`nodelet manager`。
   - 因为`nodelet`们共享相同的进程和内存空间，所以它们之间的数据传递可以是零拷贝的，这大大减少了传输和处理大型数据结构（例如图像）时的延迟。

3. **关系和差异**:
   - `nodelet` 实质上是设计为一个高效的节点版本，特别是当需要频繁交换大量数据时。
   - 一个常见的例子是图像处理管道，其中一系列的处理步骤（例如，从原始传感器数据到特征提取）需要在不同的算法之间传递图像。使用`nodelet`，这些步骤可以在一个共享的进程中运行，避免了每一步都要拷贝整个图像。
   - 而使用传统的节点，每次数据在节点之间传输时，都需要进行序列化、发送、接收和反序列化的过程，这可能导致延迟和额外的计算开销。

4. **选择指南**:
   - 当 `ROS` 应用需要高效地处理和传输大型数据时（如在机器人的感知和图像处理任务中），使用`nodelet` 。
   - 对于更通用的、不涉及大型数据处理的任务，使用普通节点
5. **代码实现**：
   
   - `node` 可以直接在 `main` 中实现。
      - 也可以用类的方法，在构造函数中启动 `sub`。
   - `nodelet` 最好以类的方式实现，并在插件的形式注册到 `Nodelet` 管理器中，并通过 `Nodelet` 管理器来加载和实例化 `Nodelet`
      - 不推荐在 main 函数中实例化 Nodelet 对象，并调用它的 onInit 函数来初始化。这可能会导致不可预料的问题。
   
   - 
     总结，`nodelet` 更高效，特别是当需要在算法之间交换大型数据时。

##### 4. 一个节点可以发布或者接收多个主题吗

- 一个节点可以发布多个主题
- 一个节点也可以订阅多个主题。

例如，一个机器人的移动控制节点：
- 发布到一个 `/cmd_vel` 主题，发送速度命令给机器人的驱动器。
- 订阅一个 `/laser_scan` 主题，从激光雷达获取障碍物信息。
- 发布到一个 `/robot_status` 主题，提供关于机器人当前状态的更新。

##### 5. nodelet 节点在 launch 中配置的时候，节点所在的包 (pkg) 为什么是 nodelet

```xml
<node pkg="nodelet" type="nodelet" name="radar_graph_slam_nodelet" args="load radar_graph_slam/RadarGraphSlamNodelet $(arg nodelet_manager)" output="screen">
```

- 节点 `RadarGraphSlamNodelet` 虽然是在包 `radar_graph_slam` 中定义，但是所属的包为 `nodelet`


原因如下：

- `nodelet` 是一个专门设计的框架，允许多个节点在**同一个进程**中运行，**避免跨进程通信**的开销。
  - 为了让一个节点成为 `nodelet` ，它必须作为共享库编译，并链接到`nodelet`库中。
- 运行一个 `nodelet` 时，不是直接运行该 `nodelet`。运行的是 **`nodelet`可执行文件**，并且告诉它要加载哪个特定的 **`nodelet`插件**。
  - 而 **`nodelet` 可执行文件**存在于 **`nodelet` 包 **中，所以节点所在的包为 `nodelet` 包。
  - 上例`load radar_graph_slam/RadarGraphSlamNodelet $(arg nodelet_manager)` 相当于加载nodelet插件

##### 6. 检查话题是否发布成功

假设检查的话题为 `/cloud_topic`、

```bash
rostopic list | grep /cloud_topic
```

- 检查是否发布

```
rostopic echo /cloud_topic
```

- 检查数据是否为空

##### 7. 将 `sensor_msg::PointCloud` 的数据转为 `pcl::PointCloud<pcl::PointXYZ>` 的数据，但是前者没有 width、height 和 is_dense，该怎么填充后者的这些数据字段?

可以根据点云数据的特点来设置这些字段。

1. **width 和 height**:
   - 如果知道点云数据的组织形式(例如,来自深度相机或激光雷达的**有序**点云), 
     - 根据点云的尺寸设置 `width` 和 `height`。
   - 如果点云数据是**无序**的
     -  `width` 设置为点云中点的数量
     -  `height` 设置为 1。
   
2. **is_dense**:
   - **确定**点云数据中**没有无效点**(NaN 或 Inf)
     -  `is_dense` 设置为 `true`。
   - **不确定**点云数据是否包含无效点
     - 可以在转换过程中检查每个点的坐标值,如果发现无效点,则将 `is_dense` 设置为 `false`。

示例代码：

```cpp
#include <ros/ros.h>
#include <sensor_msgs/PointCloud.h>
#include <pcl/point_types.h>
#include <pcl/point_cloud.h>

void cloudCallback(const sensor_msgs::PointCloud::ConstPtr& cloud_msg)
{
    pcl::PointCloud<pcl::PointXYZ> cloud;

    // 假设点云数据是无序的
    cloud.width = cloud_msg->points.size();
    cloud.height = 1;
    cloud.is_dense = true; // 初始化为 true

    // 将 sensor_msgs::PointCloud 转换为 pcl::PointCloud<pcl::PointXYZ>
    for (const auto& point : cloud_msg->points)
    {
        pcl::PointXYZ pcl_point;
        pcl_point.x = point.x;
        pcl_point.y = point.y;
        pcl_point.z = point.z;

        // 检查是否有无效点
        if (!std::isfinite(pcl_point.x) || !std::isfinite(pcl_point.y) || !std::isfinite(pcl_point.z))
        {
            cloud.is_dense = false; // 如果发现无效点,将 is_dense 设置为 false
        }

        cloud.points.push_back(pcl_point);
    }

    // 现在可以使用 cloud 对象进行后续处理
    // ...
}
```

- 点云数据是无序的,因此将 `width` 设置为点的数量,将 `height` 设置为 1。
- 初始化 `is_dense` 为 `true`,并在转换过程中检查每个点的坐标值是否有效。如果发现无效点,将 `is_dense` 设置为 `false`。

##### 8. 如何修改 ROS 包的名称

修改以下几个地方：

1. **包文件夹名称(经过实践，这一步可忽略)：**
   修改你的ROS包的文件夹名称。假设你的ROS包名称为`my_package`，那么你可以使用以下命令修改文件夹名称：

   ```bash
   mv my_package new_package_name
   ```

2. **CMakeLists.txt 文件：**
   在你的ROS包的 CMakeLists.txt 文件中，将 `project()` 命令里的包名修改为新的包名。比如，原来是这样的：

   ```cmake
   project(my_package)
   ```

   修改为：

   ```cmake
   project(new_package_name)
   ```

3. **package.xml 文件：**
   同样，在你的ROS包的 package.xml 文件中，将 `<name>` 标签的内容修改为新的包名。比如，原来是这样的：

   ```xml
   <name>my_package</name>
   ```

   修改为：

   ```xml
   <name>new_package_name</name>
   ```

4. **修改其他文件引用：**
   如果在代码或者启动文件中有引用到包名的地方，也需要将引用的包名修改为新的包名。

修改完成后，保存文件并重新编译

##### 9. 在 `roslaunch` 文件中，`<node>` 标签中的 `name` 参数和 `type` 参数的区别是什么，为什么它们的值一样

在 `ROS` 的 `roslaunch` 文件中，`name` 参数和 `type` 参数的作用有所不同。

1. `name` 参数：
   - `name` 参数用于指定将要启动的节点（Node）的名称。这个名称必须是唯一的，并且与 ROS 图中的其他节点名称不冲突。
   - 如果 `name` 参数未指定，则默认使用 `type` 参数指定的节点名称。
   - 如果在同一个 `roslaunch` 文件中启动了多个相同类型的节点，可以通过 `name` 参数为每个节点指定不同的名称，以便它们能够区分开来。

2. `type` 参数：
   - `type` 参数用于指定要启动的节点（Node）的类型或者**可执行文件的名称**。它通常是在 `CMakeLists.txt` 文件中定义的可执行文件的名称。
   - 如果 `type` 参数未指定，则默认使用 `name` 参数指定的节点名称。
   - `type` 参数还用于告诉 `roslaunch` 工具要启动哪个包中的节点。

在很多情况下，`name` 参数和 `type` 参数可以使用相同的值，特别是当你只启动一个节点时。

但是如果启动多个相同类型的节点，需要使用 `name` 参数为它们分配不同的名称，以免冲突。

为了避免混淆，建议在 `roslaunch` 文件中明确指定 `name` 参数和 `type` 参数的值。

##### 10. 在 node 节点内设置参数，为什么读取不到?

`launch`

```xml
<node pkg="cloud_merging" name="12345" type="cloud_merging"  output="screen">

    <param name="output_bag_path"  value = "bbbbbbb" />

</node>
```

`cloud_merging.cpp`

```cpp
std::string output_bag_path = nh.param<std::string>("/output_bag_path", "aaaaaaa");  
```

**解决方案1**: 加上参数前缀

因为在节点内设置参数，参数存在前缀，使用 `rosparam list` 命令查看，可得

```
/12345/output_bag_path
/rosdistro
/roslaunch/uris/host_dearmoon_ubuntu__41435
/rosversion
/run_id
```

因此 `cloud_merging.cpp` 中应该加上前缀

```cpp
std::string output_bag_path = nh.param<std::string>("/12345/output_bag_path", "aaaaaaa"); 
```

**解决方案2**：

将节点中的句柄设置为私有

```cpp
ros::NodeHandle nh("~");
```

##### 11.  bag 文件中的 %time 和 filed.header.stamp 有什么区别

- %time 表示 bag 文件的时间戳，指消息被记录到 `bag` 文件中的时间

- `filed.header.stamp` 表示消息的时间戳，是传感器采集时的时间戳。

## 概念

##### 1、节点

节点主要执行计算处理 。ROS 被设计为细粒度的模块化的系统;一个机器人控制系统通常有很多节点组成 。例如，一个节点控制激光测距仪，一个节点控制轮电机，一个节点执行定位，一个节点执行路径规划，一个节点提供系统图形界面，等等。一个ROS节点通过ROS客户端库 [client library](http://wiki.ros.org/Client Libraries)编写，例如 [roscpp](http://wiki.ros.org/roscpp) o或[rospy](http://wiki.ros.org/rospy)

##### 2、ROS Master

ROS Master 提供命名和注册服务，以便其余的节点能够找到彼此并进行通信。下面是有关 `ROS Master` 的一些详细信息：

1. **命名和注册服务**：
   - 当一个节点启动时，它会向 `ROS Master` 注册自己，说明它提供了哪些话题、服务等。
   - 同样地，当一个节点想要找到某个特定的话题或服务时，它会询问 `ROS Master` 来获得相关的信息。

2. **参数服务器**：
   - `ROS Master` 还包含一个参数服务器，它允许数据在节点之间被存储和共享。这些数据通常是配置参数，例如机器人的某些固定参数或启动时的配置设置。

3. **不进行实际的数据通信**：
   - 虽然节点通过 `ROS Master` 找到彼此，但当它们开始通信（例如，通过发布和订阅话题）时，数据流不会经过 `ROS Master`。换句话说，`ROS Master` 只是提供了一个查找服务，不负责节点之间的实际数据传输。

4. **单点故障**：
   - 在ROS系统中，`ROS Master`是一个关键的组件，如果它停止工作，节点之间将无法找到彼此，导致整个系统的通信断裂。
   - 在实际的机器人系统中，需要确保 `ROS Master` 是可靠和稳定的，以确保整体系统的稳定性。

5. **启动和终止**：
   - `roscore` 命令用于启动`ROS Master`，以及其他几个核心的ROS背景进程。
   - 当`roscore` 运行时，你可以开始启动其他节点，并让它们通过`ROS Master`进行通信。
   - 关闭`roscore`将会终止`ROS Master`和相关的进程。

总之，`ROS Master`在ROS中扮演着中央目录的角色，允许节点发现彼此并建立通信连接。

##### 3、bags



# 一、安装 ROS

## 1.1 安装

##### 官方安装方法：

官方文档：http://wiki.ros.org/ROS/Installation

其他教程：https://blog.csdn.net/sea_grey_whale/article/details/132023522

Autolabor：http://www.autolabor.com.cn/book/ROSTutorials/chapter1/12-roskai-fa-gong-ju-an-zhuang/124-an-zhuang-ros.html

1. 添加ros可用源

   ```bash
   sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
   ```

2. 设置密钥

   ```bash
   sudo apt install curl # if you haven't already installed curl
   curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
   ```

3. 安装

   - 更新Debian安装包的源

     ```bash
     sudo apt update
     ```

   - 安装Desktop-Full版本

     XXX为`melodic`或者`noetic`

     ```bash
     sudo apt install ros-XXX-desktop-full
     ```

   - 查找可用的包

     XXX为`melodic`或者`noetic`

     ```bash
     apt search ros-XXX
     ```

4. 环境设置

   XXX为`melodic`或`noetic`

   ```bash
   echo "source /opt/ros/XXX/setup.bash" >> ~/.bashrc
   source ~/.bashrc
   ```

5. 安装必要的包，以便于创建和管理自己的ROS工作空间

   - `noetic`版本为`python3`
   - `melodic`版本为`python`

   ```bash
   sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential
   ```

6. 安装并初始化rosdep，能够让你轻易安装编译一些源所需要的系统依赖。运行ROS一些核心组件也需要rosdep。

   ```bash
   sudo apt install python3-rosdep
   ```

   初始化

   ```bash
   sudo rosdep init //可能会出错，看autolabor版的教程
   rosdep update	//需要挂梯子
   ```

7. 测试

   ```bash
   roscore
   ```

   打开第二个终端，运行完后出现静止海龟

   ```bash
   rosrun turtlesim turtlesim_node
   ```

   打开第三个终端，启动turtlesim的键盘控制节点，

   ```bash
   rosrun turtlesim turtle_teleop_key
   ```

##### 香鱼ros一键安装

```bash
wget http://fishros.com/install -O fishros && bash fishros
```

根据提示选择相应的数字。



## 1.2 VScode集成开发环境搭建

有些版本的 VScode 可能与 ros 不兼容，报一些莫名其妙的错误，这时可以下载历史版本。

相关资料：

https://blog.csdn.net/abanchao/article/details/130632181

##### c_cpp_properties.json

c++配置文件

##### settings.json



##### tasks.json



# 二、ROS 文件系统

![img](http://www.autolabor.com.cn/book/ROSTutorials/assets/文件系统.jpg)WorkSpace --- 自定义的工作空间

    |--- build:编译空间，用于存放CMake和catkin的缓存信息、配置信息和其他中间文件。
    
    |--- devel:开发空间，用于存放编译后生成的目标文件，包括头文件、动态&静态链接库、可执行文件等。
    
    |--- src: 源码
    
        |-- package：功能包(ROS基本单元)包含多个节点、库与配置文件，包名所有字母小写，只能由字母、数字与下划线组成
    
            |-- CMakeLists.txt 配置编译规则，比如源文件、依赖项、目标文件
    
            |-- package.xml 包信息，比如:包名、版本、作者、依赖项...(以前版本是 manifest.xml)
    
            |-- scripts 存储python文件
    
            |-- src 存储C++源文件
    
            |-- include 头文件
    
            |-- msg 消息通信格式文件
    
            |-- srv 服务通信格式文件
    
            |-- action 动作格式文件
    
            |-- launch 可一次性运行多个节点 
    
            |-- config 配置信息
    
        |-- CMakeLists.txt: 编译的基本配置



# 三、ROS 通信方式

ROS 是进程(也称Nodes)的分布式框架。ROS 中的每一个功能点都是一个单独的进程，每一个进程单独运行。

不同节点之间的通信方式有三种

- 基于同步 RPC 样式通信的**服务(services)**机制：请求响应模式
- 基于异步流媒体数据的**话题(topics)**机制：发布订阅模式
- 用于数据存储的**参数服务器(Parameter Server)**：参数共享模式

## 1. 话题通信

适用于不断更新的、少逻辑处理的数据传输场景，比如各种使用传感器的场景。

### 1.1 理论模型

![image-20241216100837058](https://raw.githubusercontent.com/dearmoonnnnnn/typoraImage/main/img/image-20241216100837058.png)

##### 三个角色

- ROS Master (管理者)
- Talker(发布者)
- Listener(订阅者)

##### 流程

0) advertise("bar",foo:1234)

   **发布者**在Master中注册自身信息，其中包含所发消息的话题名称：bar, 和==RPC地址==:1234

1. subscribe("bar")

   **订阅者**在Master中注册自身信息，包含需要订阅消息的话题名称：bar

2. {foo:1234}

   Master比对**发布者**和**订阅者**关注的话题，如果一致，将**发布者**的RPC地址发送给**订阅者**

3. connect("scan", TCP)

   **订阅者**根据RPC地址，远程访问**发布者**。

4. TCP server: foo:2345

   发布者接收到订阅者的请求后，也通过RPC向订阅者确认连接信息，并发送自身TCP地址信息：2345

5. 订阅者和发布者建立连接

   订阅者根据上一步返回的消息，使用TCP协议与发布者建立连接

6. 发布者向订阅者发送消息

   发布者通过TCP协议向订阅者发布消息。

   **note**：`前五步使用RPC协议，后两步使用TCP协议`

​				`发布者和订阅者没有先后顺序`

​				`发布者和订阅者都可以有多个`

​				`发布者和订阅者建立连接后，不再需要ROS Master。即关闭ROS Master后，两者也能照常通信。`



### 1.2 具体示例

##### 发布话题的三个步骤(python)

1、建立 `publisher`

```python
pcl_pub = rospy.Publisher('kitti_point_cloud', PointCloud2, queue_size = 10)
```

2、读取数据 (如果要发布 `Marker` 类型，可能不需要这一步)

点云数据被存在 `pont_cloud` 数组中

```python
import numpy as np

# kitti的点云数据有4列，分别为xyz和反射率
from sensor_msgs.msg import PointCloud2
point_cloud = np.fromfile(os.path.join(DATA_PATH, 'velodyne_points/data/%010d.bin'%frame), dtype = float32).reshape(-1, 4)
```

3、发布

```python
import sensor_msg.point_cloud2 as pcl2
from std_msgs.msg import Header

# 使用pcl2的create_cloud_xyz32将点云转换为pcl2格式，但是不保存反射率信息
# 使用该函数之前，需要Header
header = Header()
header.stamp = rospy.Time.now()  # stamp	：时间戳
header.frame_id = 'map'			 # frame_id ：当前坐标系的名称

# 使用publish函数发布消息
pcl_pub.publish(pcl2.create_cloud_xyz32(header, pooint_cloud[:, :3]))
```

由此，成功向kitti_point_cloud这个topic发布PointCloud2消息类型的点云数据。

##### 发布话题(C++)

```c++
#include "ros/ros.h"
#include "std_msgs/String.h" //普通文本类型的消息
#include <sstream>

int main(int argc, char  *argv[])
{   
    //设置编码
    setlocale(LC_ALL,"");

    // 初始化 ROS 节点:命名(唯一)
    // 参数1和参数2 后期为节点传值会使用
    // 参数3 是节点名称，是一个标识符，需要保证运行后，在 ROS 网络拓扑中唯一
    ros::init(argc,argv,"talker");
    // 实例化 ROS 句柄
    ros::NodeHandle nh;//该类封装了 ROS 中的一些常用功能

    /**************** 第一步***************/
    //实例化 发布者 对象
    //泛型: 发布的消息类型
    //参数1: 要发布到的话题
    //参数2: 队列中最大保存的消息数，超出此阀值时，先进的先销毁(时间早的先销毁)
    ros::Publisher pub = nh.advertise<std_msgs::String>("chatter",10);

    //组织被发布的数据，并编写逻辑发布数据
    //数据(动态组织)
    std_msgs::String msg;
    // msg.data = "你好啊！！！";
    std::string msg_front = "Hello 你好！"; //消息前缀
    int count = 0; //消息计数器

    //逻辑(一秒10次)
    ros::Rate r(1);

    //节点不死
    while (ros::ok())
    {
        /**************** 第二步***************/
        //使用 stringstream 拼接字符串与编号
        std::stringstream ss;
        ss << msg_front << count;
        msg.data = ss.str();
        
        /**************** 第三步***************/
        //发布消息
        pub.publish(msg);
        
        //加入调试，打印发送的消息
        ROS_INFO("发送的消息:%s",msg.data.c_str());

        //根据前面制定的发送贫频率自动休眠 休眠时间 = 1/频率；
        r.sleep();
        count++;//循环结束前，让 count 自增
        //暂无应用
        ros::spinOnce();
    }


    return 0;
}

```



##### 订阅话题(C++)

在ROS中，一个节点订阅话题的步骤通常如下：

1. **初始化ROS节点**：使用 `ros::init()` 函数初始化节点。
2. **创建节点句柄**：使用 `ros::NodeHandle` 创建句柄。
3. **定义消息回调函数**：创建一个函数，该函数会在消息到达时被调用。
4. **订阅话题**：使用 `node_handle.subscribe()` 函数订阅一个话题，并关联回调函数。
5. **进入事件循环**：使用 `ros::spin()` 或 `ros::spinOnce()` 进入事件循环，等待并处理消息。

以下是一个简单的示例，该节点订阅名为 `/chatter` 的话题，该话题的消息类型为 `std_msgs::String`：

```cpp
#include <ros/ros.h>
#include <std_msgs/String.h>

// 定义回调函数
void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
    ROS_INFO("I heard: [%s]", msg->data.c_str());
}

int main(int argc, char **argv)
{
    // 初始化ROS节点
    ros::init(argc, argv, "listener");

    // 创建节点句柄
    ros::NodeHandle n;

    // 订阅话题
    ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);

    // 进入事件循环
    ros::spin();

    return 0;
}
```

在这个例子中：

- 当收到 `/chatter` 话题的新消息时，`chatterCallback` 函数会被调用。
- `ros::spin()` 会保持你的节点在事件循环中，直到节点被关闭或 ROS 被关闭。

确保你已经为 `std_msgs` 和其他必要的包设置了依赖，并且在你的 `CMakeLists.txt` 中正确地设置了编译选项。

## 2. 服务通信

## 3. 参数服务器

在ROS中，通常所说的参数服务器指的是 ROS 参数服务器，它是一个分布式的参数存储系统，允许你在运行时存储和检索参数。

##### 举例：

1. **从参数服务器获取参数值**：

```cpp
#include <ros/ros.h>

int main(int argc, char** argv) {
    ros::init(argc, argv, "param_example_node");
    ros::NodeHandle nh;

    /** 
     * 从参数服务器获取整数参数值
     * "/my_namespace/my_param_int":要获取的参数名称，指定了参数的命名空间和参数名
     * my_param_int:存储参数值的变量。如果参数存在，它的值会被存在这个变量中
     * 1：参数的默认值。如果参数服务器中找不到指定的参数，将会使用这个默认值
     */
    int my_param_int;
    nh.getParam("/my_namespace/my_param_int", my_param_int, 1);

    // 从参数服务器获取浮点数参数值
    double my_param_double;
    nh.getParam("/my_namespace/my_param_double", my_param_double);

    // 从参数服务器获取字符串参数值
    std::string my_param_string;
    nh.getParam("/my_namespace/my_param_string", my_param_string);

    // 从参数服务器获取布尔值参数值
    bool my_param_bool;
    nh.getParam("/my_namespace/my_param_bool", my_param_bool);

    ROS_INFO("my_param_int: %d", my_param_int);
    ROS_INFO("my_param_double: %f", my_param_double);
    ROS_INFO("my_param_string: %s", my_param_string.c_str());
    ROS_INFO("my_param_bool: %s", my_param_bool ? "true" : "false");

    return 0;
}
```

2. **将参数设置到参数服务器**：

```cpp
#include <ros/ros.h>

int main(int argc, char** argv) {
    ros::init(argc, argv, "param_example_node");
    ros::NodeHandle nh;

    // 设置整数参数到参数服务器
    nh.setParam("/my_namespace/my_param_int", 42);

    // 设置浮点数参数到参数服务器
    nh.setParam("/my_namespace/my_param_double", 3.14);

    // 设置字符串参数到参数服务器
    nh.setParam("/my_namespace/my_param_string", "hello");

    // 设置布尔值参数到参数服务器
    nh.setParam("/my_namespace/my_param_bool", true);

    ROS_INFO("Parameters set.");

    return 0;
}
```

**ROS 参数服务器和 ROS 节点的参数服务器的区别：**

在 ROS 中，通常所说的“ ROS 参数服务器”是指 ROS 提供的一个分布式参数存储系统，它允许 ROS 节点在运行时动态地存储和检索参数。这些参数可以在不同的 ROS 节点之间共享，允许节点之间进行配置和通信。

而“ ROS 节点的参数服务器”则指的是每个 ROS 节点内部的参数服务器实例。每个运行的 ROS 节点都有自己的参数服务器，它用于存储节点本身的参数，以及该节点可能需要使用的其他节点的参数。

因此，两者的区别在于：
1. ROS 参数服务器是一个全局的、分布式的参数存储系统，用于整个ROS系统中的节点之间共享参数。
2. ROS 节点的参数服务器是每个节点内部的参数存储实例，用于存储该节点本身的参数以及可能需要的其他节点的参数。





# 四、launch 文件

## 1. 概述

##### 动机

一个程序可能需要启动多个节点，比如 ROS 内置的小乌龟案例，控制小乌龟运动需要分别启动 roscore ,乌龟页面节点，键盘控制节点。如果每次都调用 rosrun 逐一启动，效率底下。

解决方案：使用 roslaunch 命令，集合 launch 文件启动管理节点。

##### 概念

launch 文件是一个 XML 格式的文件，可以启动本地和远程的多个节点，还可以在服务器参数中设置参数。

##### 作用

简化节点的配置和启动，提高 ROS 程序的启动效率。

## 2. 调用 launch 文件

```bash
roslaunch 包名 xxx.launch
```

##### 对于 roscore 的处理

`roslaunch` 命令执行 `launch` 文件时，首先会判断是否启动了 `roscore`，如果启动了则不再启动，否则自动启动  `roscore`。

## 3. 常见标签

### 3.1 节点（node）

- 使用 `<node>` 标签来启动一个 ROS 节点。<!-- roslaunch命令不能保证按照node的声明顺序来启动节点，因为节点的启动是多进程的 -->

- 可以为节点指定名称、所属的包、运行的二进制文件、命名空间等。

- 示例：

  ```xml
  <!-- 必要属性 -->
  <!-- name：节点名称; pkg：节点所属的包; type：节点类型，即可执行文件名称-->
  <!-- type 值需要与 CMakeList.txt 中 add_executable 的第一个参数匹配，它们都表示可执行文件名称 -->
  <!-- name 和 type 经常使用同一个值，它们的区别见问题 9 -->
  
  <!-- 可选属性 -->
  <!-- args = "XXX XXX XXX"：将参数传递给节点; output = "log | screen": 日志发送目标(log日志文件或者屏幕) -->
  <!-- machine = "机器名"： 启动另一台机器上的节点 -->
  <!-- respan = "ture | false" : 如果节点退出，是否自动重启，传感器节点、GUI节点一般会设置 -->
  <!-- respan_delay = "N": 当respan为true时，重启节点时会延时N秒 -->
  
  <node name="talker" pkg="rospy_tutorials" type="talker" />
  ```

### 3.2 包含其他 launch文件（include）

- 使用 `<include>` 标签包含另一个 `.launch` 文件，使得 `.launch` 文件可以模块化。

- 是不能包含被 `include` 文件的 `arg` 参数 

- 示例：

  ```xml
  <!-- $(find 包名)：可以定位到～/catkin_ws/src/包名 -->
  <include file="$(find another_package)/launch/another_file.launch"/>
  ```



##### include 不能向上暴露 arg/param 参数

- 若在被 `include` 的 `launch` 文件中定义 `arg` 参数，不能直接在主 `launch` 文件中找到该参数

  - `<arg>` 参数只能在当前 `launch` 文件圣像

- 解决方法：在主 `launch` 文件定义 arg 参数，并通过 `<include>` 的 `<arg>` 子标签传入

  - 注意 `launch_a.launch` 中的参数传递到 `launch_b.launch` 中的参数

  ```xml
  <!-- launch_a.launch -->
  
  <launch>
  
    <!-- 主 launch 文件中定义或传递参数 -->
    <arg name="path" default="/home/dearmoon/datasets/NWU/"/>
    <arg name="scene" default="日雪不颠簸高速21/"/>
    <arg name="region_growing_version" default="test/"/>
    <arg name="output_group" default="_10/"/>
    <arg name="radar_preprocessing_group" default="origin/"/>
  
    <!-- 传递参数给 include 的 launch2.launch -->
    <include file="$(find region_growing)/launch/launch_b.launch">
      <arg name="path" value="$(arg path)"/>
      <arg name="scene" value="$(arg scene)"/>
      <arg name="region_growing_version" value="$(arg region_growing_version)"/>
      <arg name="output_group" value="$(arg output_group)"/>
      <arg name="radar_preprocessing_group" value="$(arg radar_preprocessing_group)"/>
    </include>
  
    <node name="region_growing" pkg="region_growing" type="region_growing" output="screen" required="true">
        <param name="outputFile" value="$(arg path)$(arg scene)enhancing_v2/$(arg output_group)regionGrowingResult.bag"/>
    </node>
  
    <node name="player_region_growing" pkg="rosbag" type="play"
        args="--clock --rate=1
        $(arg path)$(arg scene)radar_preprocessing/$(arg radar_preprocessing_group)regionGrowing_input.bag"
        >
        <remap from="/filtered_points" to="/ars548_process/detectioin_PointCloud2"/>
        <remap from="/livox/lidar" to="/livox/lidar_PointCloud2"/>
    </node>
  
    <node pkg="rviz" type="rviz" name="rviz" args="-d $(find region_growing)/rviz_config/region_growing.rviz"/>
  </launch>
  ```

- param 也不能向上暴露参数，且不能直接在 `launch` 中通过 `$(param ...)` 使用



### 3.3 环境变量（env）

使用 `<env>` 标签设置环境变量，这些环境变量对于启动的节点是可见的。

- 示例：

  ```xml
  <env name="ROBOT_INITIAL_POSE" value="-x 0 -y 0 -Y 0"/>
  ```

### 3.4 重新映射（remap）

- 使用 `<remap>` 标签将一个话题名或服务名从一个名称重映射到另一个名称。

- 使得订阅者可以与发布者关注同一个话题，建立通信。

- 示例：

  ```xml
  <node name="talker" pkg="rospy_tutorials" type="talker" >
    <remap from="chatter" to="new_chatter" />
  </node>
  ```

### 3.5 组（group）

- 对节点分组，具有 ns 属性，让节点归属于某个命名空间

- 示例：

  ```xml
  <group ns="robot1">
    <node ... />
    <node ... />
  </group>
  ```

### 3.6 参数服务器参数（param）

- **作用：**向参数服务器设置参数。

- **参数源：**可以在标签中通过 value 指定，也可以通过外部文件加载。

- **使用位置：**可以添加在 launch 内，node 外；也可以添加在 node 内。`<node>` 标签中作为子标签时，相当于私有命名空间。

- 示例： 

  ```xml
  <!-- name = "命名空间/参数名" ：参数名称，可以包含命名空间-->
  <!-- value = "XXX" : 可选，定义参数值，如果省略，必须指定外部文件作为参数源 -->
  <!-- type = "str | int | double | yaml" ：可选，指定参数类型，如果未指定，roslaunch会根据一定规则尝试确定参数类型 -->
  
  <launch>
      <!-- 两种情况-->
      <!-- 1. 在launch内，node外 -->
      <!-- 向参数服务器设置一个名称为param_A, 类型为int， 值为100的参数-->
  	<param name="param_A" type = "int" value = "100" />
  	
      <!-- 2. 在node内-->
      <!-- 也会在参数服务器中设置参数，但参数存在前缀-->
      <node name="talker" pkg="rospy_tutorials" type="talker" />>
      	<param name="param_B" type="double" value="3.14" /> 
      </node>
      
  </launch>
  ```
  
  启动 launch 文件后，在cmd中查看参数服务器中的参数
  
  ```bash
  rosparm list 
  ```
  
  显示：
  
  ```bash
  /param_A
  /talker/param_B
  ```

### 3.7 参数文件加载（rosparam）

- 从 `YAML` 文件导入参数，或者将参数导出到 `YAML` 文件，也可以用来删除参数

- 参数文件示例：

  ```yaml
  use_sim_time: true
  
  path: "/home/dearmoon/datasets/NWU/"
  
  scene_0: "日晴不颠簸低速3/"
  scene_1: "日雪不颠簸高速21/"
  scene_2: "夜雪不颠簸高速23/"
  scene_3: "日晴不颠簸高速4/"
  scene: "日雪不颠簸高速21/"  # 默认选择
  
  region_growing_v0: "enhancing_v2/"
  region_growing_v1: "region_growing_dynamic_align/"
  region_growing_version: "test/"
  
  output_group: "_10/"
  radar_preprocessing_group: "origin/"
  ```

- **使用位置**：同 `param`

- 示例：

  ```xml
  <!-- command="load | dump |delete "： 加载、导出或删除参数 -->
  <!-- param="参数名称" -->
  <!-- ns="命名空间"： 可选-->
  <rosparam file="$(find my_package)/param/config.yaml" command="load" />
  
  <node >
  	...
  </node>
  ```

- `launch` 文件不能直接引用 `param`
  -  不能使用 `$(param ...)`
- `launch` 文件包含的 node 节点源代码中可以调用 `param` 参数

### 3.8 启动文件参数（arg）

- 用于动态传参，两种用途。

- **示例1**：传递给 `launch` 文件中的其他部分

  ```xml
  <!-- name="参数名称" -->
  <!-- defalut="默认值"，可选 -->
  <!-- value="数值"，可选，不可以与default并存 -->
  <!-- doc="描述"：参数说明 -->
  <arg name="car_length" default="0.5"/>
  
  <!-- 设置参数时可以调用使用 $(arg car_length)调用 -->
  <!-- 修改arg的值，参数A、B、C的值都会被修改，更加灵活 -->
  <param name="A" value="$(arg car_length)" />
  <param name="B" value="$(arg car_length)" />
  <param name="C" value="$(arg car_length)" />
  
  ```

- **示例2**：可以在命令行中传递参数的值

  ```bash
  roslaunch 程序名 XXX.launch car_length:=0.6
  ```

  这时候参数 car_length 不再是默认的 0.5, 而是 0.6

## 4. 可执行文件读取 launch 中的参数

- 通过 `ros::NodeHandle` 来访问参数
- 可用 `nh.getParam` 或 `nh.param<typename T>`

```cpp
	ros::init(argc, argv, "imu_txt_to_bag");
    ros::NodeHandle nh("~");  // "~" 表示私有命名空间，限定为当前节点的参数

    // 定义变量用于存储参数
    std::string input_file, output_bag;

    // 从参数服务器中读取参数
    if (!nh.getParam("input_file", input_file)) {
        ROS_ERROR("Failed to get 'input_file' parameter");
        return -1;
    }
    if (!nh.getParam("output_bag", output_bag)) {
        ROS_ERROR("Failed to get 'output_bag' parameter");
        return -1;
    }

	std::string topic = private_nh.param<std::string>("topic", "/default_topic");

    ROS_INFO("Input file: %s", input_file.c_str());
    ROS_INFO("Output bag: %s", output_bag.c_str());
```

## 5. 控制节点启动顺序

##### 动机

- `launch` 启动多个节点，节点的顺序是非确定性的。
- 有时需要指定节点启动顺序，确保播放 `bag` 的节点在启动节点启动完成后再启动。

### 5.1 `<group>` 标签 + 延迟启动

```xml
<launch>
    <group>
        <!-- 优先启动第一个节点 -->
        <node name="node_1" pkg="pkg_1" type="node_1" output="screen" />
    </group>
    <group>
        <!-- 延迟启动第二个节点，等待前一个节点完成初始化 -->
        <node name="node_2" pkg="pkg_2" type="node_2" output="screen" />
        <param name="required_param" value="value" />
    </group>
</launch>
```

### 5.2 设置 `required` 属性

```bash
<launch>
    <!-- 第一个节点 -->
    <node name="node_1" pkg="pkg_1" type="node_1" output="screen" required="true" />

    <!-- 第二个节点（此节点会等待 node_1） -->
    <node name="node_2" pkg="pkg_2" type="node_2" output="screen" />
</launch>
```

### 5.3 添加延迟

```bash
<launch>
    <!-- 优先启动第一个节点 -->
    <node name="node_1" pkg="pkg_1" type="node_1" output="screen" />

    <!-- 第二个节点延迟 5 秒启动 -->
    <node name="node_2" pkg="pkg_2" type="node_2" output="screen">
        <param name="launch_delay" value="5" />
    </node>
</launch>
```

## 6. 场景：使用



# 五、rosbag

http://wiki.ros.org/rosbag

##### 动机：

机器人传感器获取到的信息，有时我们需要实时处理，有时可能只是采集数据，事后分析。

ROS为数据的存留和读取，提供了专门的工具：rosbag

##### 概念

用于**录制**和**回放**ROS话题的一个工具集。

##### 本质

- rosbag本质也是ros的节点。

- 当录制时，rosbag是一个订阅节点，订阅话题消息，并将订阅的消息写入磁盘文件。

- 当重放时，rosbag是一个发布节点，可以读取磁盘文件，发布文件中的话题消息。

## 1. 写 bag 文件(C++)

```C++
#include "ros/ros.h"
#include "rosbag/bag.h"
#include "std_msgs/String.h"


int main(int argc, char *argv[])
{
    ros::init(argc,argv,"bag_write");
    ros::NodeHandle nh;
    
    //创建bag对象
    rosbag::Bag bag;
    
    //打开bag文件流，open的两个参数：文件路径，操作类型(读、写、追加)
    bag.open("/home/rosdemo/demo/test.bag",rosbag::BagMode::Write);
    
    //写入
    //wirite的参数：话题名称、时间戳、消息内容
    std_msgs::String msg;
    msg.data = "hello world";
    bag.write("/chatter",ros::Time::now(),msg);
    bag.write("/chatter",ros::Time::now(),msg);
    bag.write("/chatter",ros::Time::now(),msg);
    bag.write("/chatter",ros::Time::now(),msg);
    
    //关闭文件流
    bag.close();

    return 0;
}

```

## 2. 读 bag 文件(C++)

```c++
#include "ros/ros.h"
#include "rosbag/bag.h"
#include "rosbag/view.h"
#include "std_msgs/String.h"
#include "std_msgs/Int32.h"

int main(int argc, char *argv[])
{

    setlocale(LC_ALL,"");

    ros::init(argc,argv,"bag_read");
    ros::NodeHandle nh;

    // 创建 bag 对象
    rosbag::Bag bag;
    
    // 打开 bag 文件
    bag.open("/home/rosdemo/demo/test.bag",rosbag::BagMode::Read);
    
    // 读数据
    // 先获取消息的集合View(bag)，再迭代取出消息的字段
    // m.getTopic()：获取话题，返回string类型
    // m.getTime()：获取时间戳，返回time类型
    // m.instantiate<消息类型>：获取消息内容，返回指针类型
    for (rosbag::MessageInstance const m : rosbag::View(bag))
    {
        std_msgs::String::ConstPtr p = m.instantiate<std_msgs::String>();
        if(p != nullptr){
            ROS_INFO("读取的数据:%s",p->data.c_str());
        }
    }

    //关闭文件流
    bag.close();
    return 0;
}
```

## 3. rosbag play

http://wiki.ros.org/rosbag/Commandline

### 3.1 `--clock`

```bash
rosbag play --clock example.bag
```

- 使用 `--clock` 参数时，`rosbag play` 会使用 `header.stamp` 的时间戳信息，并使用这些时间戳来模拟消息的发布时间。
  - 在 SLAM、多传感器融合中，**模拟时间是标准做法**。
- 不使用该参数，ROS 使用当前系统时间，适合只关注数据本身，不依赖时间的场景。

### 3.2 `-s`

```bash
rosbag play -s 0.5 example.bag
```

- 从 `example.bag` 的 0.5s 处开始播放

### 3.3 `--rate`

```
rosbag play --rate=3 example.bag
```

- 以 3 倍速度播放 `bag` 文件

### 3.4 `--duration`

```bash
rosbag play --duration=10000 example.bag
```

- 播放的总时长，单位为秒

### 3.5 `--topic`

```bash
rosbag play example.bag --topics /example_topic
```

- 播放特定话题
- `--topics` 参数需要放在最后

## 4. bag 文件相关操作

### 4.1 融合两个 bag 文件中的特定话题

要将两个bag文件中特定话题的消息融合为一个bag文件，可以使用`rosbag filter`命令。下面是一个示例：

```bash
rosbag filter input1.bag output1.bag "topic == '/your_topic'"
rosbag filter input2.bag output2.bag "topic == '/your_topic'"
rosbag merge output1.bag output2.bag merged_output.bag
```

这里的 `/your_topic` 是您要融合的特定话题。首先，使用 `rosbag filter` 从两个输入文件中提取特定话题的消息到两个输出文件中。然后，使用 `rosbag merge` 将这两个输出文件合并为一个文件。

请确保将 `input1.bag`、`input2.bag` 和 `merged_output.bag` 替换为实际的文件名和路径。

### 4.2 两个 bag 文件合并为一个，并保留需要的所有话题

设 `a.bag` 包有三个话题 `/a1` 、`/a2`、`/a3`，`b.bag` 包有 2 个话题，分别为 `/b1`、`/b2`

```cpp
#include "ros/ros.h"
#include "rosbag/bag.h"
#include "rosbag/view.h"
#include "std_msgs/String.h"

int main(int argc, char** argv) {
    // 初始化ROS节点
    ros::init(argc, argv, "bag_merger");
    ros::NodeHandle nh;

    // 从ROS参数服务器获取输入bag文件的路径和输出bag文件的路径
    std::string bag1_path, bag2_path, output_bag_path;
    nh.param<std::string>("bag1_path", bag1_path, "/path/to/a.bag");
    nh.param<std::string>("bag2_path", bag2_path, "/path/to/b.bag");
    nh.param<std::string>("output_bag_path", output_bag_path, "/path/to/merged.bag");

    // 打开输入bag文件和输出bag文件
    rosbag::Bag bag1(bag1_path, rosbag::bagmode::Read);
    rosbag::Bag bag2(bag2_path, rosbag::bagmode::Read);
    rosbag::Bag output_bag(output_bag_path, rosbag::bagmode::Write);

    // 创建一个Topic过滤器，选择需要保留的所有话题
    rosbag::View view1(bag1, rosbag::TopicQuery("/a1") || rosbag::TopicQuery("/a2") || rosbag::TopicQuery("/a3"));
    rosbag::View view2(bag2, rosbag::TopicQuery("/b1") || rosbag::TopicQuery("/b2"));

    // 循环读取输入bag文件中的消息并写入输出bag文件
    for (const rosbag::MessageInstance& msg : view1) {
        output_bag.write(msg.getTopic(), msg.getTime(), msg);
    }

    for (const rosbag::MessageInstance& msg : view2) {
        output_bag.write(msg.getTopic(), msg.getTime(), msg);
    }

    // 关闭输入和输出的bag文件
    bag1.close();
    bag2.close();
    output_bag.close();

    // 进入ROS事件循环，接收和处理ROS消息
    ros::spin();

    return 0;
}
```

### 4.3 删除/保留 bag 文件中的特定话题

```bash
rosbag filter input.bag output.bag "topic != '/topic1'"
```

删除多个话题，可以使用逻辑运算符 `&&` 和 `||`。比如，要删除 `/topic1` 和 `/topic2` 的消息，可以使用：

```bash
rosbag filter input.bag output.bag "topic != '/topic1' && topic != '/topic2'"
```

保留话题：使用 `==`

保留多个话题：使用 `or`

```bash
rosbag filter yxbdbgs23.bag 23l.bag "topic == '/livox/lidar' or topic == '/livox/imu'"
```



## 5. rosbag record

记录某个特定的话题

```bash
rosbag record /example_topic
```

指定输出文件名

```bash
rosbag record -O my_bagfile.bag /example_topic
```

## 6. rosbag filter 提取特定帧

```bash
rosbag filter input.bag output.bag "t.secs == 1639182735 and t.nsecs == 123456789"
```

# 六、PCL库

## 相关资料

- pcl 库官方文档

  http://wiki.ros.org/pcl/Overview

- pcl 相关函数文档

  http://docs.ros.org/en/hydro/api/pcl_conversions/html/namespacepcl.html#af662c7d46db4cf6f7cfdc2aaf4439760

## 问题：

##### 1、PCL 的 fromROSMsg() 和 toROSMsg() 不能正确处理 xyz 之外其他 field 的数据长度

https://blog.csdn.net/XCCCCZ/article/details/136142235

##### 2、将 `pcl::PointCloud<pcl::PointXYZI>` 的数据转换为 `sensor_msgs::PointCloud2` 后，怎么获取点的强度信息？

在 `sensor_msgs::PointCloud2` 消息中

- `fields` 数组描述点云数据中每个字段的信息，包括
  - 名称
  - 偏移量
  - 数据类型
  - 计数
- `data` 数组包含了实际的点云数据，每个点的数据按字节存储，并按 fields 数组的描述解析

代码示例：

```c++
#include <ros/ros.h>
#include <sensor_msgs/PointCloud2.h>
#include <sensor_msgs/PointField.h>
#include <pcl_conversions/pcl_conversions.h>
#include <pcl/point_types.h>
#include <pcl/point_cloud.h>

void cloudCallback(const sensor_msgs::PointCloud2ConstPtr& cloud_msg)
{
    // 查找intensity字段的偏移量
    int intensity_offset = -1;
    for (const auto& field : cloud_msg->fields)
    {
        if (field.name == "intensity")
        {
            intensity_offset = field.offset;
            break;
        }
    }

    if (intensity_offset == -1)
    {
        ROS_ERROR("No intensity field found in PointCloud2 message.");
        return;
    }

    // 计算每个点的步长
    int point_step = cloud_msg->point_step;

    // 遍历每个点，提取强度信息
    for (size_t i = 0; i < cloud_msg->width * cloud_msg->height; ++i)
    {
        const uint8_t* point_data = &cloud_msg->data[i * point_step];
        float intensity;
        memcpy(&intensity, point_data + intensity_offset, sizeof(float));

        // 打印或处理强度信息
        ROS_INFO("Point %zu: intensity=%f", i, intensity);
    }
}

int main(int argc, char** argv)
{
    ros::init(argc, argv, "pointcloud_intensity_extractor");
    ros::NodeHandle nh;

    ros::Subscriber sub = nh.subscribe("input_topic", 1, cloudCallback);

    ros::spin();

    return 0;
}
```

## 1. 表示点云数据的四种方式

##### 6.1.1、sensor_msgs::PointCloud —— ROS message，已弃用

- 由于 `sensor_msgs::PointCloud2` 更加灵活和高效，所以已弃用。
- 数据字段包括`points`（一个3D点列表）和 `channels`（关于点的额外数据，如强度）。

  ![image-20240430110136856](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240430110136856.png)
  
  ```msg
  std_msgs/Header header
    uint32 seq
    time stamp
    string frame_id
  geometry_msgs/Point32[] points
    float32 x
    float32 y
    float32 z
  sensor_msgs/ChannelFloat32[] channels
    string name
    float32[] values
  ```

##### 6.1.2、sensor_msgs::PointCloud2 —— ROS message 

- 旨在比其前身更加灵活和高效。
- 包含一个统一的`data`字段，其中包含所有点数据。
- 使用一个灵活的消息格式（类似于ROS中的图像表示方式）。
- 虽然更加高效，但直接使用它处理比较复杂。

数据结构:

![image-20240430110041982](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240430110041982.png)

```
std_msgs/Header header
  uint32 seq
  time stamp
  string frame_id
uint32 height
uint32 width
sensor_msgs/PointField[] fields
  uint8 INT8=1
  uint8 UINT8=2
  uint8 INT16=3
  uint8 UINT16=4
  uint8 INT32=5
  uint8 UINT32=6
  uint8 FLOAT32=7
  uint8 FLOAT64=8
  string name
  uint32 offset
  uint8 datatype
  uint32 count
bool is_bigendian
uint32 point_step
uint32 row_step
uint8[] data
bool is_dense
```

##### 6.1.3、pcl::PointCloud2 —— PCL 数据结构，主要是为了与 ROS 兼容

- PCL 的数据结构，与 `sensor_msgs::PointCloud2` 非常匹配。
- 存在的目的是允许 PCL 与 ROS 之间轻松转换，而不丢失任何信息。
- 虽然它与 ROS 消息非常匹配，但通常不会在 PCL 中直接使用这种格式来操作点云。相反，会将其转换为 `pcl::PointCloud<T>` 以进行大多数处理任务。

数据结构：

![image-20240520102156384](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240520102156384.png)

##### 6.1.4、pcl::PointCloud\<T> —— 标准的 PCL 数据结构

- PCL 中用于点云的标准且最常用的数据结构。
- 是模板化的，其中 `T` 是点的类型，例如 `pcl::PointXYZ`、`pcl::PointXYZRGB`、`。` 等。
- 提供了许多方便的方法，允许轻松访问点。
- 旨在为点云处理任务提供高效和用户友好的方式。

具体的数据结构：

![image-20240520103227482](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240520103227482.png)

```cpp
template<typename PointT>
class PointCloud
{
  public:
    // 点云的宽度，即点的数量
    uint32_t width;
    // 点云的高度，通常为 1
    uint32_t height;
    // 是否是密集点云，即是否每个点都有值
    bool is_dense;
    // 点云数据，是一个数组，每个元素代表一个三维点
    std::vector<PointT, Eigen::aligned_allocator<PointT> > points;
    // ...
};

```

## 2. 同时使用 ROS 和 PCL 时典型的工作流

1. 在一个 ROS 节点中接收一个 `sensor_msgs::PointCloud2` 消息的点云。
2. 如果需要使用 pcl 处理它，我们会将其转换为 `pcl::PointCloud<T>`，进行处理。
3. 最后将其转换回 `sensor_msgs::PointCloud2`，用以发布或者在 ROS 中进一步使用。<!-- 在ROS的pcl_conversions包中有函数，可以方便地在这些类型之间转换 -->

##### 示例代码：

```cpp
#include <ros/ros.h>
#include <sensor_msgs/PointCloud2.h>
#include <pcl_conversions/pcl_conversions.h>
#include <pcl/point_cloud.h>
#include <pcl/point_types.h>

// 回调函数，接收sensor_msgs::PointCloud2消息
void cloudCallback(const sensor_msgs::PointCloud2ConstPtr& msg)
{
    // 将sensor_msgs::PointCloud2转换为pcl::PointCloud<pcl::PointXYZ>
    pcl::PointCloud<pcl::PointXYZ>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZ>);
    pcl::fromROSMsg(*msg, *cloud);

    // 对点云进行处理，这里简单地输出点云的大小
    std::cout << "Received point cloud with " << cloud->size() << " points." << std::endl;

    // 可以在这里对点云进行进一步的处理，例如滤波、配准等

    // 将pcl::PointCloud<pcl::PointXYZ>转换回sensor_msgs::PointCloud2
    sensor_msgs::PointCloud2 output_msg;
    pcl::toROSMsg(*cloud, output_msg);

    // 发布处理后的点云消息
    // 注意：发布前要设置正确的header等信息
    // pub.publish(output_msg);
}

int main(int argc, char** argv)
{
    ros::init(argc, argv, "point_cloud_processing");
    ros::NodeHandle nh;

    // 订阅sensor_msgs::PointCloud2消息
    ros::Subscriber sub = nh.subscribe<sensor_msgs::PointCloud2>("/input_point_cloud", 10, cloudCallback);
    // 创建一个发布者，发布处理后的点云消息
    ros::Publisher pub = nh.advertise<sensor_msgs::PointCloud2>("/output_point_cloud", 10);
    // 进入ROS主循环，开始接收消息并处理
    ros::spin();

    return 0;
}

```

- 创建一个 ROS 节点，订阅了话题名为 `/input_point_cloud` 的 `sensor_msgs::PointCloud2` 消息。
- 在 `cloudback` 函数中将其转换为 `pcl::PointCloud<pcl::PointXYZ>` 进行处理（如滤波，配准）
- 最后，将处理后的点云转换回 `sensor_msgs::PointCloud2` 并发布到名为  `/output_point_cloud`  的话题中。

##### pcl::fromROSMsg()

```cpp
template<typename T>
void fromROSMsg(const sensor_msgs::PointCloud2 &cloud, pcl::PointCloud<T> &pcl_cloud)
{
    pcl::PCLPointCloud2 pcl_pc2;
    pcl_conversions::toPCL(cloud, pcl_pc2);
    pcl::fromPCLPointCloud2(pcl_pc2, pcl_cloud);
 }
```

- `cloud`

  `ros` 格式的消息

- `pcl_cloud`

  `pcl` 格式的消息

##### pcl::toROSMsg()

```cpp
template<typename T>
void toROSMsg(const pcl::PointCloud<T> &pcl_cloud,sensor_msgs::PointCloud2 &cloud)
{
     pcl::PCLPointCloud2 pcl_pc2;
     pcl::toPCLPointCloud2(pcl_cloud, pcl_pc2);
     pcl_conversions::moveFromPCL(pcl_pc2, cloud);
}
```

- `pcl_cloud`

  `pcl` 格式的消息

- `cloud`

  `ros` 格式的消息

# 七、从零创建 ROS 工程

##### 1、在 ROS 的工作目录下使用 catkin_create_pkg 命令创建一个新的 ROS 包。

假设工作目录是 `catkin_ws`

```bash
cd ~/catkin_ws/src
catkin_create_pkg my_package std_msgs sensor_msgs rosbag roscpp
```

上述命令在 `catkin_ws/src` 目录下创建一个名为 `my_package` 的新 ROS 包

- `catkin_create_pkg` 参数：

  ```bash
  catkin_create_pkg <package_name> [depend1] [depend2] [depend3] ...
  ```

  - `<package_name>`：包名

  - `[depend1]`, `[depend2]`, `[depend3]`：该包依赖的其他ROS包的名称。这些包将被添加到 `package.xml` 文件中的 `<depend>` 部分

##### 2、编写 C++ 程序（节点）

在 `my_rosbag_recorder` 包的 `src` 目录下，创建 C++ 源文件，如 `my_rosbag_recorder.cpp`，并编写代码

##### 3、编辑 CMakeLists.txt 文件

在 `my_rosbag_recorder` 包的根目录下，打开 `CMakeLists.txt` 文件，添加适当的 `rosbag` 依赖项。

```cmake
find_package(catkin REQUIRED COMPONENTS
  std_msgs
  sensor_msgs
  roscpp
  rosbag
)

## 默认四行都为注释，需要取消注释
catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES imu_convert
  CATKIN_DEPENDS rosbag roscpp sensor_msgs
  DEPENDS system_lib
)

add_executable(rosbag_recorder src/rosbag_recorder.cpp)

target_link_libraries(rosbag_recorder
  ${catkin_LIBRARIES}
)
```

##### 4、构建工程

回到 `ROS` 工作区目录，执行以下命令编译工程

```bash
catkin_make
```

##### 5、运行 ROS 核心

在终端中，运行 ROS 核心

```bash
roscore
```

##### 6、运行节点

```bash
rosrun my_rosbag_recorder my_rosbag_recorder
```

- rosrun 参数：第一个参数为 ROS 包的名称，第二个参数为 ROS 包中编写的节点的执行文件的名称。	

## 遇到的问题：

##### 1、执行 rosrun 时显示包找不到

**原因**： 

没有将工作区添加到环境变量中

**解决方法**：

1. 打开 `/.bashrc` 文件

   ```bash
   vim ~/.bashrc
   ```

2. 在 `.bashrc` 文件中最后一行添加

   ```bash
   source /home/dearmoon/shares/share_ubuntu/projects/bag_recorder/devel/setup.bash
   ```

    

3. source配置文件，使其立即生效

   ```bash
   source ~/.bashrc
   ```

#####  2、找到了包，但是找不到包中的可执行文件

**原因**：没有在包下的CMakeLists.txt文件中正确配置

**解决方法**：

1. 打开包中的 `CMakeLists.txt` 文件。

2. 使用 `add_executable()`命令指定可执行文件

   ```cmake
   add_executable(my_bag_recorder src/bag_write.cpp)
   ```

   - 第一个参数是生成的可执行文件名称，第二个参数为 `cpp` 源码文件

3. 使用 `target_link_libraries()` 命令将包链接到可执行文件，确保它能够找到所有的依赖项。

   ```cmake
    target_link_libraries(my_bag_recorder
      ${catkin_LIBRARIES}
    )
   ```

4. 重新执行 catkin_make

## 自定义消息格式

1. 在`msg`文件夹下创建`.msg`文件

   如：

   ```msg
   # CustomMsg.msg
   
   Header header           # ROS standard message header
   uint64 timebase         # The time of first point
   uint32 point_num        # Total number of pointclouds
   uint8 lidar_id          # Lidar device id number
   uint8[3] rsvd           # Reserved use
   CustomPoint[] points    # Pointcloud data
   ```

   

2. `CmakeList.txt`

   ```cmake
   # 1、声明自定义消息文件所需的依赖
   find_package(catkin REQUIRED COMPONENTS
     roscpp
     rospy
     std_msgs
     message_generation  # 添加这一行
   )
   
   # 2、添加生成自定义消息的语句
   add_message_files(
     FILES
     CustomMsg.msg
     CustomPoint.msg  # 如果CustomPoint消息单独定义了文件，则需要添加这一行
   )
   
   # 3、在 generate_messages() 函数中添加定义的自定义消息类型：
   generate_messages(
     DEPENDENCIES
     std_msgs
   )
   
   # 4、在 catkin_package() 中确保包含了消息生成的依赖
   catkin_package(
     CATKIN_DEPENDS roscpp rospy std_msgs message_runtime  # 添加 message_runtime
   )
   
   ```

3. 在需要用到该消息的cpp文件添加头文件

   ```cpp
   #include <rosbag_tools/CustomMsg.h>
   
   // 使用消息时，需要加上namespace
   rosbag_tools::CuostomMsg msg;
   ```




## 一个自定义包依赖另一个包

假设包 `example1` 依赖包 `example2`

1、`example1` 包的 `package.xml` 文件添加如下内容

```xml
<build_depend>example2</build_depend>
<build_export_depend>example2</build_export_depend>
<exec_depend>example2</exec_depend>
```

2、`example1` 包的 `CMakeLists.txt` 添加如下内容

```cmake
find_package(catkin REQUIRED COMPONENTS
  example2
  ......
)

catkin_package(
 INCLUDE_DIRS include
#  LIBRARIES cloud_merging
 CATKIN_DEPENDS rosbag roscpp sensor_msgs std_msgs example2
 DEPENDS system_lib
)

include_directories(
  src	# 添加这一行，才能找到 example2 包
# include
  ${catkin_INCLUDE_DIRS}
)

```



# 八、nodelet

## 问题

##### 1、nodelet 定义的三种句柄有什么区别

- 全局节点句柄
  - **作用范围：**提供对 ROS 全局命名空间中的所有主题、服务和参数的。
  - **线程安全性：**通常是单线程的，在多线程环境中可能需要额外的同步机制。
- 多线程节点句柄
  - **作用范围：**多线程句柄提供对 ROS 全局命名空间中的所有主题、服务和参数的访问，类似于全局节点句柄
- 私有节点句柄

# 九、catkin_make

## 1. 在一个系统上并行管理和运行同一个项目的不同版本

假设有两个版本的 4DRadarSLAM 项目

- 该项目依赖于 barometer_bmp388、fast_apdgicp、ndt_omp
  - catkin_ws/src 目录下包含 4DRadarSLAM、barometer_bmp388、fast_apdgicp、ndt_omp

### 步骤

1. **创建新的 `catkin_ws` 工作空间：**

   ```sh
   mkdir -p ~/new_catkin_ws/src
   cd ~/new_catkin_ws/src
   ```

2. **将另一个版本的 `4DRadarSLAM` 复制到新的工作空间：**

   ```sh
   cp -r /path/to/another/version/of/4DRadarSLAM ~/new_catkin_ws/src/
   ```

3. **将依赖包复制到新的工作空间：**

   你可以选择将依赖包也复制到新的工作空间中，或者在新工作空间的 `CMakeLists.txt` 和 `package.xml` 中添加对这些包的依赖。

   ```sh
   cp -r ~/catkin_ws/src/barometer_bmp388 ~/new_catkin_ws/src/
   cp -r ~/catkin_ws/src/fast_apdgicp ~/new_catkin_ws/src/
   cp -r ~/catkin_ws/src/ndt_omp ~/new_catkin_ws/src/
   ```

4. **编译新的工作空间：**

   ```sh
   cd ~/new_catkin_ws
   catkin_make
   ```

5. **切换工作空间环境：**

   每次运行不同版本的项目时，记得切换到对应的工作空间环境。

   - 切换到原来的工作空间环境：

     ```sh
     source ~/catkin_ws/devel/setup.bash
     ```

   - 切换到新的工作空间环境：

     ```sh
     source ~/new_catkin_ws/devel/setup.bash
     ```

### 注意事项

- 如果两个版本的 `4DRadarSLAM` 之间有依赖关系冲突，确保它们在各自独立的工作空间中运行。
- 使用独立的工作空间可以避免依赖包版本冲突，确保每个版本的项目可以正确编译和运行。

### 示例目录结构

原来的 `catkin_ws` 目录结构：

```
catkin_ws/
├── src/
│   ├── 4DRadarSLAM/
│   ├── barometer_bmp388/
│   ├── fast_apdgicp/
│   └── ndt_omp/
```

新的 `catkin_ws` 目录结构：

```
new_catkin_ws/
├── src/
│   ├── 4DRadarSLAM/  (另一个版本)
│   ├── barometer_bmp388/
│   ├── fast_apdgicp/
│   └── ndt_omp/
```

## 2. 自定义编译顺序

- 假设包 A 依赖于包 B
- 需要同时在包 A 的 `package.xml` 和 `CMakeLists.txt` 中声明对包 B 的依赖
- 经测试，第一次 `catkin_make` 仍会报错，必须二次编译。

### 2.1  `package.xml`

```xml
<package format="2">
  <name>package_A</name>
  <version>0.0.1</version>
  <description>Package A depends on Package B</description>
  <maintainer email="you@example.com">Your Name</maintainer>
  <license>MIT</license>

  <!-- 编译和运行时依赖 -->
  <build_depend>package_B</build_depend>
  <build_export_depend>package_B</build_export_depend>
  <exec_depend>package_B</exec_depend>

  <export></export>
</package>
```

### 2.2 CMakeLists.txt

```bash
find_package(catkin REQUIRED COMPONENTS
  package_B
)

catkin_package(
  CATKIN_DEPENDS package_B
)

include_directories(
  ${catkin_INCLUDE_DIRS}
)
```



# 十、格式转换

## 1. bag 包转 png

1. 播放 bag 包

2. 执行命令

   ```bash
   rosrun image_view image_saver image:=<image_topic> _filename_format:=<filename_format>
   ```

   其中：

   - `<image_topic>` 是包含图像数据的ROS话题名，对于你的情况是/stereo_inertial_publisher/color/image。
   - `<filename_format>` 是保存图像文件的命名格式。可以使用image:=<image_topic>来指定文件名，也可以使用时间戳等信息来动态命名。例如，image_%04i.png将以image_0001.png、image_0002.png等格式保存图像。

   eg:

   ```bash
   rosrun image_view image_saver image:=/stereo_inertial_publisher/color/image _filename_format:=image_%04i.png
   ```



## 2. bag 转 pcd

### 2.1 自定义节点

```cpp
#include <ros/ros.h>
#include <sensor_msgs/PointCloud.h>
#include <pcl/io/pcd_io.h>
#include <pcl/point_types.h>

ros::Publisher pub;
static int frame_count = 0; // 添加静态变量记录帧序号

void cloudCallback(const sensor_msgs::PointCloud::ConstPtr& cloud_msg)
{
    pcl::PointCloud<pcl::PointXYZI> cloud;
    cloud.width = cloud_msg->points.size();
    cloud.height = 1;
    cloud.is_dense = true; // 初始化为 true

    // 将 sensor_msgs::PointCloud 转换为 pcl::PointCloud<pcl::PointXYZ>
    for (size_t i = 0; i < cloud_msg->points.size(); ++i)
    {
        pcl::PointXYZI point;
        point.x = cloud_msg->points[i].x;
        point.y = cloud_msg->points[i].y;
        point.z = cloud_msg->points[i].z;
        point.intensity = cloud_msg->channels[1].values[i];

        // 检查是否有无效点
        if (!std::isfinite(point.x) || !std::isfinite(point.y) || !std::isfinite(point.z))
        {
            cloud.is_dense = false; // 如果发现无效点,将 is_dense 设置为 false
        }

        cloud.points.push_back(point);
    }

    // 根据当前帧序号动态生成输出文件名
    std::string output_file = "/home/dearmoon/datasets/calib/NWU508/pcd/" + std::to_string(frame_count) + ".pcd";

    pcl::io::savePCDFileASCII(output_file, cloud);
    ROS_INFO("Saved %lu data points to %s.", cloud.points.size(), output_file.c_str());

    frame_count++; // 增加帧序号
}

int main(int argc, char** argv)
{
    ros::init(argc, argv, "bag_to_pcd");
    ros::NodeHandle nh;

    ros::Subscriber sub = nh.subscribe("/ars548_process/detection_point_cloud", 10, cloudCallback);

    ros::spin();

    return 0;
}
```

转换为 `pcd` 后，可以使用 `pcl_viewer` 可视化点云数据。

```bash
pcl_viewer example.pcd
```

### 2.2 使用 pcl_ros 提供的节点

- 启动 `pcl_ros` 提供的 `pointcloud_to_pcd` 节点

  ```bash
  rosrun pcl_ros pointcloud_to_pcd input:=/my_topic
  ```

  - 输入的消息类型必须为 `sensor_msgs::PointCloud2`

- 播放 `bag` 文件

  ```bash
  rosbag play file.bag
  ```

- 一条消息会生成一个 `pcd` 文件，保存在当前工作目录下

## 3. bag 转 txt

```bash
rostopic echo -b <bag_name>.bag -p /<topic_name> > <new_name>.txt
```

举例

```bash
rostopic echo -b data1.bag -p /tag_detections > data1.txt
```

## 4. pcd 转 bag

- 使用 `pcl_ros` 包提供的 `pcd_to_pointcloud` 节点

- `pcd` 只有一帧，需要加上 `interval` 和 `latch` 参数确保发布的点云被接收到。

### 4.1 bash 命令

```bash
rosrun pcl_ros pcd_to_pointcloud _file_name:=/home/dearmoon/datasets/one_msg/lidar_step2.pcd _frame_id:=map __name:=cloud2_publisher_manual
```

### 4.2 launch 文件

```xml
<node name="cloud2_publisher_manual" pkg="pcl_ros" type="pcd_to_pointcloud">
    <param name="file_name" value="/home/dearmoon/datasets/one_msg/lidar_step2.pcd"/>
    <param name="frame_id" value="map"/>
    <param name="interval" value="1.0"/>
    <param name="latch" value="true"/>
    <remap from="/cloud_pcd" to="/cloud2"/>
</node>
```

- `file_name`
  - 必需的参数
  - `pcd` 文件路径
- `rate`
  - 发布频率
-  `interval`
  - 点云数据发布的时间间隔
  - 0：表示只发布一次
  - 大于0：以特定的时间间隔反复发布数据
    - 时间单位：秒

# 十一、使用参数服务器

## 1、设置参数

##### 通过 launch 文件：

```xml
<launch>
    <node name="param_test" pkg="your_package_name" type="param_test_node" output="screen">
        <param name="outlier_removal_method" value="RADIUS"/>
    </node>
</launch>
```

##### 通过命令行：

```sh
rosparam set /param_test/outlier_removal_method RADIUS
rosrun your_package_name param_test_node
```

## 2、获取参数

```cpp
#include <ros/ros.h>
#include <string>

int main(int argc, char** argv) {
    ros::init(argc, argv, "param_test");
    ros::NodeHandle nh;
    ros::NodeHandle private_nh("~");

    std::string outlier_removal_method = private_nh.param<std::string>("outlier_removal_method", "STATISTICAL");
    
    std::string outlier_removal_method = nh.param<std::string>("/param_test/outlier_removal_method","STATISTICAL")

    ROS_INFO("Outlier removal method: %s", outlier_removal_method.c_str());

    ros::spin();
    return 0;
}
```

- 如果参数服务器中存在 `outlier_removal_method` 参数且值为 `RADIUS` ，那么运行时该参数的值将是 `RADIUS` 。
- 如果参数服务器中没有设置该参数，那么运行时该参数的值将是 `STATISTICAL` ，这是默认值。

- 是否需要加前缀
  - 若使用公有句柄，需要加上前缀 `/param_test/` 
  - 若使用私有句柄，不需要加。在访问节点私有参数时，会自动在前面加上 `~`。



# 十二、定时器

```cpp
#include <ros/ros.h>
#include <ros/console.h>

void timerCallback(const ros::WallTimerEvent& event) {
    // 打印定时器触发的时间信息
    ROS_INFO("Timer expected to trigger at: %f", event.current_expected.toSec());
    ROS_INFO("Timer actually triggered at: %f", event.current_real.toSec());
    ROS_INFO("Last timer expected to trigger at: %f", event.last_expected.toSec());
    ROS_INFO("Last timer actually triggered at: %f", event.last_real.toSec());
    ROS_INFO("Time since last trigger: %f seconds", (event.current_real - event.last_real).toSec());
}

int main(int argc, char** argv) {
    ros::init(argc, argv, "timer_example");
    ros::NodeHandle nh;

    // 创建一个 WallTimer，每 1 秒触发一次 timerCallback
    ros::WallTimer timer = nh.createWallTimer(ros::WallDuration(1.0), timerCallback);

    // 进入 ROS 事件循环
    ros::spin();

    return 0;
}
```

# 十三、日志

```bash
rosrun rqt_logger_level rqt_logger_level
```

- 打开日志查看工具



# 十四、时间相关

## 1. 时间同步

##### message_filters

```cpp
#include <ros/ros.h>
#include <message_filters/subscriber.h>
#include <message_filters/sync_policies/approximate_time.h>
#include <sensor_msgs/Image.h>
#include <sensor_msgs/PointCloud2.h>

// 回调函数
void callback(const sensor_msgs::ImageConstPtr &image, const sensor_msgs::PointCloud2ConstPtr &pointcloud) {
    ROS_INFO("Synchronized data received!");
    // 在这里处理同步后的数据
}

int main(int argc, char **argv) {
    ros::init(argc, argv, "time_synchronizer");
    ros::NodeHandle nh;

    // 创建订阅器
    message_filters::Subscriber<sensor_msgs::Image> image_sub(nh, "/camera/image", 1);
    message_filters::Subscriber<sensor_msgs::PointCloud2> pointcloud_sub(nh, "/lidar/points", 1);

    // 定义同步策略（ApproximateTime）
    typedef message_filters::sync_policies::ApproximateTime<sensor_msgs::Image, sensor_msgs::PointCloud2> SyncPolicy;
    message_filters::Synchronizer<SyncPolicy> sync(SyncPolicy(10), image_sub, pointcloud_sub);

    // 注册回调函数
    sync.registerCallback(boost::bind(&callback, _1, _2));

    ros::spin();
    return 0;
}
```

##### 订阅器队列大小

- **定义**: `SyncPolicy(queue_size)`
- **类型**: `int`
- 定义了消息同步队列的大小。
- 作用：
  - `Synchronizer` 会根据时间戳将消息存储到队列中，并尝试对齐它们。
  - 如果队列过小，会导致未及时对齐的旧消息被丢弃；如果队列过大，可能会占用过多内存。

##### 设置最大时间间隔

```cpp
  sync.setMaxIntervalDuration(ros::Duration(0.05));       // 设置最大时间间隔，单位秒
```

## 2. /clock 话题

### 2.1 作用

- 使用虚拟时间

- `ros::Time::now()` 回返回系统的虚拟时间（`/clock`），而不是真实时间（`wall time`）

### 2.2 两种方式

- 虚拟时间必须加上 `use_sim_time = true`

- 若 rosbag 中包含 `/clock` 话题
  - 播放时发布该话题，使用虚拟时间
- 若 rosbag 中不包含 `/clock` 话题
  - 方式加上 `--clock` 参数，rosbag play 会提取每一条消息的时间戳，并生成 `/clock` 话题



## 3. 时间记录/打印

### 3.1 `std::ofstream`

##### 问题

默认采用科学计数法格式，但是时间戳的数值很大，导致后面几位被省略，影响准确的时间戳

```cpp
std::ofstream fout("/home/dearmoon/log/extrinsic_log.csv");

fout << "time,tx,ty,tz,roll,pitch,yaw\n";
for (size_t i = 0; i < vec_time.size(); ++i) {
    const auto& t = vec_translation[i];
    const auto& r = vec_euler[i];
    fout << vec_time[i] << "," 
         << t[0] << "," << t[1] << "," << t[2] << ","
         << r[0] << "," << r[1] << "," << r[2] << "\n";
}

```



##### 解决方法：

- 强制固定格式，保留小数位数：`std::fixed << std::setprecisoin(9)`

```cpp
std::ofstream fout("/home/dearmoon/log/extrinsic_log.csv");
fout << std::fixed << std::setprecision(9);  // 👈 加这行

fout << "time,tx,ty,tz,roll,pitch,yaw\n";
for (size_t i = 0; i < vec_time.size(); ++i) {
    const auto& t = vec_translation[i];
    const auto& r = vec_euler[i];
    fout << vec_time[i] << "," 
         << t[0] << "," << t[1] << "," << t[2] << ","
         << r[0] << "," << r[1] << "," << r[2] << "\n";
}
```



### 3.2 `ros::Time::now()` 打印

```cpp
ROS_INFO("ros:Time:now(): %f s", ros::Time::now().toSec());
```

- 只显示小数后 6 位

  - 原因：`%f` 只显示小数后 6 位
  - 实际上 `ros::Time:now()` 的精度达到小数后 9 位

- 解决方法：指定打印精度 `.9f`

  ```cpp
  ROS_INFO("ros:Time:now(): %.9f s", ros::Time::now().toSec());
  ```



### 3.3 确保精度不丢时

- 使用 `toSec()` 返回 `double` 类型，容易丢失精度
- 可转换为纳秒存储

```cpp
std::ofstream fout("/home/dearmoon/log/extrinsic_log.csv");
fout << std::fixed;
fout << "time,tx,ty,tz,roll,pitch,yaw\n";
for (size_t i = 0; i < vec_time.size(); ++i) {
    std::ostringstream oss;
    oss << vec_time[i].sec << "."<<std::setw(9) << std::setfill('0') << vec_time[i].nsec;
    const auto& t = vec_translation[i];
    const auto& r = vec_euler[i];
    fout << oss.str() << ","
         << t[0] << "," << t[1] << "," << t[2] << ","
         << r[0] << "," << r[1] << "," << r[2] << "\n";
}	
```

- `setw(9)`
  - 设置字段宽度为 9，这里是 `nsec`
- `setfilll('0')`
  - 若不足 9 位，在前面补 `0`
