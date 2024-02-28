# 零、问题和概念

## 问题：

##### 1、ars548_process_node未接受到网口数据，从网口读数据的代码在哪？

源代码未给出,需要在`receiveThread()`函数中添加

##### 2、在ars548_process包中添加了一个文件，需要在哪些文件中配置，才能与ROS适配

当你在ROS包中添加新文件时，为了确保`catkin_make`可以正确地编译和链接这些文件，你可能需要更新以下文件：

1. **CMakeLists.txt**: 这是Catkin build系统使用的主要文件。你需要确保你的新源文件被包含在某个可执行文件或库的构建中。

    - 如果你添加了一个新的节点（一个可执行文件），你可能需要更新或添加一个`add_executable()`和`target_link_libraries()`条目。
      
      ```cmake
      add_executable(your_new_node_name src/your_new_file.cpp)
      target_link_libraries(your_new_node_name ${catkin_LIBRARIES})
      ```

    - 如果你添加到一个现有的库或可执行文件，确保新文件被包含在相关的`add_library()`或`add_executable()`中。

2. **package.xml**: 如果你的新文件引入了新的依赖（例如新的ROS包依赖），你需要在`package.xml`中使用`<depend>`标签添加它们。

    ```xml
    <depend>new_dependency_name</depend>
    ```

3. **头文件**：如果你的新文件是一个C++头文件，并且你希望其他包可以访问它，你需要确保它在`CMakeLists.txt`中被安装到合适的目录中：

    ```cmake
    install(DIRECTORY include/${PROJECT_NAME}/
      DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
    )
    ```

4. **其他**：如果你的新文件不是源代码，例如启动文件、配置文件、URDF模型等，你可能还需要确保它们被复制到合适的安装目录或被其他部分的ROS系统引用。

完成上述更改后，从工作空间的根目录运行`catkin_make`应该就能成功编译你的新文件了。

记住，当添加新文件或进行大的更改时，为了避免构建问题，有时最好在`catkin_make`之前清理构建和devel目录。但要小心，因为这样会删除所有的编译输出和设置的环境。

##### 3、ProcessRadarData函数，如何传递

##### 4、为什么obj有header成员，而obj_list没有，添加时间戳的代码应该在obj中还是obj_list中



# 一、根据一个毫米波雷达ROS driver实现，同4DRaSLAM(未实现，弃用)

## 目前进展：

已弃用，太过复杂

## 代码地址：

https://github.com/HesaiTechnology/HesaiLidar_Swift_ROS

## 文件

### cloud_nodelet.launch

启动的节点：

pandar_pointcloud/CloudNodelet 



### /src/conversions/cloud_nodelet.cc

节点启动时调用

conv_.reset(new Convert(getNodeHandle(), getPrivateNodeHandle()));

- reset表示释放指针conv_当前的所持有的对象，同时指向新的对象。<!-- 若括号中无参数，则指向nullptr -->



### /src/conversions/convert.cc

构造函数：Convert

```C++
Convert::Convert(ros::NodeHandle node, ros::NodeHandle private_nh, std::string node_type)
	:data_(new pandar_rawdata::RawData()),
	 drv(node, private_nh, node_type, this)
```

为处理Pandar LiDAR数据设计的。以下是对这段代码的简要说明：

1. **参数列表**:
    - `ros::NodeHandle node`: ROS全局节点句柄。
    - `ros::NodeHandle private_nh`: 一个私有的节点句柄，通常用于读取该节点的私有参数。
    - `std::string node_type`: 表示节点类型的字符串。

2. **初始化列表**:
    - `data_(new pandar_rawdata::RawData())`: 为 `data_` 分配并初始化内存。这里，我们假设 `data_` 是一个指向 `pandar_rawdata::RawData` 类型的智能指针。
    - `drv(node, private_nh, node_type, this)`: 初始化 `drv` 成员，似乎是用来与某种驱动接口或者设备进行交互的。

3. **函数体**:
    - 设置一个版本字符串 `m_sRosVersion`。
    - 输出这个版本的警告信息。
    - 如果 `node_type` 与预定义的 `LIDAR_NODE_TYPE` 匹配，则执行以下操作：
        - 从私有节点句柄中获取一个名为 "publish_model" 的参数，并将其值设置为 `publishmodel`。
        - 从私有节点句柄中获取一个名为 "start_angle" 的参数，并将其值乘以100赋值给 `m_iLidarRotationStartAngle`。
        - 调用 `data_->setup(private_nh)`。这可能是进行某种初始化或设置的方法。
        - 初始化 `srv_`，它可能是一个dynamic reconfigure服务器。dynamic reconfigure允许在ROS运行时动态更改参数。
        - 设置dynamic reconfigure的回调函数为 `Convert` 类的 `callback` 成员函数。

总的来说，这个构造函数设置了Convert类的一些初值，并为这个类与Pandar LiDAR数据的处理进行了初始化。它也似乎具有动态重新配置的功能，允许在运行时修改某些参数。

# 二、根据ARS548-demo实现

## A、整体思路：

##### 两个项目：

- RosDriverForARS548：
  - 接受json格式的传感器数据，并用以ROS消息的格式发布
- rosbag_recorder
  - 订阅RosDriverForARS548的话题，并生成bag文件

##### 流程：

1. 使用wireshark将pcap(pcapng)的解析结果保存为json文件

   注意需要ars548插件

1. 使用RosDriverForARS548，直接从json文件读取解析结果，和字符流，并发布相关话题

1. 同时使用rosbag_recorder订阅话题，转为bag文件

##### 运行环境：

- Ubuntu18.04 

  - ROS melodic

  - nlohmann-json库

    具体见 3.2.2 部分

  - wireshark

    1. 安装wireshark

       ```bash
       sudo apt install wireshark
       ```

       出现弹窗，选择“是”，允许Wireshark捕获网络数据包。

    2. 配置wireshark插件，使其能够解析ars548传感器数据。

       

       

- Ubuntu20.04(推荐)
  - ROS noetic
  - nlohmann-json库
  - wireshark

## B、RosDriverForARS548

##### 代码地址：

https://github.com/wulang584513/ARS548-demo/tree/master

##### 调整后的代码：

https://github.com/letMeEmoForAWhile/RosDriverForARS548

### 2、文件

#### ars548_process.launch

启动四个节点

- ars548_process_node：
  - 发布三个话题：
    - /ars548_process/object_list
    - /ars548_process/detection_list
    - /ars548_process/radar_status
- test_radar_input_node
  - 检测雷达的输入信号
  - 发布车辆速度，车辆方向，偏航角等话题
- info_convert_node
  - 将object和detection列表的数据转换为rviz可以显示的数据格式。
    1. 订阅ars548_process_node节点的两个话题：/ars548_process/object_list和ars548_process/detection_list
    2. 经过格式转换后，发布成rviz可以显示的两个话题：/ars548_process/object_marker，显示object话题；/ars548_process/detection_point_cloud，显示detection话题。
- rviz

#### ars548_process/src/udp_interface.cpp(忽略该部分)

udp接口，从udp读取数据

##### UDP三种通信模式

UDP（用户数据报协议）是一个简单的面向消息的传输层协议。它不提供可靠性、流量控制或数据重组，这意味着应用程序在上层必须处理丢失、乱序或重复的数据报。但正因为这种简单性，UDP常常被用于那些需要快速、低延迟的通信，例如实时音频、视频或游戏。

当涉及UDP通信时，主要有三种通信模式：单播、组播和广播。

1. **UDP 单播 (Unicast)**:
    - 单播是一对一的通信方式。消息从一个点发送到另一个点。
    - 例如，当一个客户端向服务器发送一个请求，并从该服务器获取一个响应时，这就是单播。
    - 在上述代码中的`initUdpUnicastClient`方法和`sendToRadar`方法，目的是实现单播通信。即，一个客户端向一个特定的服务器发送消息。

2. **UDP 组播 (Multicast)**:
    - 组播是一对多的通信方式。消息从一个点发送到多个订阅该组播组的端点。
    - 组播地址是一个特定的IP地址范围（例如，IPv4的`224.0.0.0`到`239.255.255.255`）。
    - 服务器向一个组播地址发送消息，然后所有订阅这个组播地址的客户端都会收到这个消息。
    - 组播主要用于那些需要同时向多个订阅者发送相同数据的应用，例如流式视频服务或在线会议工具。
    - 在上述代码中的`initUdpMulticastServer`方法和`receiveFromRadar`方法，目的是实现组播通信。即，服务器可以发送消息到一个组播组，并从这个组接收消息。

3. **UDP 广播 (Broadcast)**:
    - 广播是一对所有的通信方式。消息从一个点发送，然后网络上的所有机器都会接收它。
    - 广播主要用于本地网络，并且不会跨路由器传递。
    - 广播用于那些需要在本地网络上通知所有设备的情况，例如DHCP。

总之，UDP单播是一对一的通信，UDP组播是一对多的通信，而UDP广播是一对所有的通信。上述代码中描述了如何创建一个UDP组播服务器和一个UDP单播客户端。

##### 代码实现的函数

- 组播服务器：
  - socket_server_fd
  - IPv4地址族，UDP通信协议，

- 单播客户端
- ==接收数据流==
- void ProcessRadarData( char *data, int len):
  - 处理三种类型的数据并发布，三种数据分别为 
    - ObjectList
    - DetectionList
    - BasicStatus

#### ars548_process/src/data_process.cpp

##### 作用

实现数据处理

##### 函数

- processObjectListMessage(char *in, RadarObjectList *o_list):

#### ars548_process/src/data_struct.h

### 3、如何数据读取流

##### 动机：原始的ars548

#### 3.1、从pcap文件读取数据

由于pcap保存的是原始字节流，无法读取重组后的字节流，因此放弃该思路。

#### 3.2、以json文件的形式保存wireshark的解析结果，并读取

##### 3.2.1 json文件的数据结构

##### 对象(object)

- 使用大括号'{ }'包围。

- 由一组键值对组成。

  - 键是字符串
  - 值可以是：字符串、数字、布尔值、null、对象或者数组
  - 键和值之间使用冒号`:`分隔
  - 键值对之间使用`,`分隔

- 示例

  ```json
  {
      "firstName": "John",
      "lastName": "Doe",
      "age": 25,
      "isStudent": false
  }
  ```

##### 数组

- 使用方括号'[ ]'包围

- 由一系列值组成

  - 值可以是：字符串、数字、布尔值、null、对象或者数组
  - 值之间使用`,`分隔

- 示例

  ```json
  [1, "apple", true, null, {"color": "red"}]
  ```

##### 3.2.2 C++读取json文件

1. 将wireshark解析结果保存在json文件

2. 安装相关的库，apt安装或者源码安装选择一个

   - apt安装

      ```
      sudo apt update
      sudo apt install nlohmann-json3-dev
      ```


   - 源码安装: https://blog.csdn.net/jiemashizhen/article/details/129275915

     ```
     // 在你喜欢的位置
     git clone  https://github.com/nlohmann/json.git
     cd json
     mkdir build
     cd build
     cmake ..
     make
     sudo make install
     ```


3. 读取json文件，并将内容解析到json对象中`nlohmann::json j`

4. 遍历json，如果当前对象满足条件，返回当前数据包`j[i]`,其格式仍为`nlohmann::json`

   - 在`nlohmann::json`库中，JSON对象、数组、字符串、数字、布尔值和null都是使用`nlohmann::json`类型来表示的。
   - 当你通过索引、键或其他方法访问`nlohmann::json`对象中的元素时，返回的仍然是`nlohmann::json`类型，不过其内部的实际数据可能是字符串、数字、布尔值、数组、对象或null。

## C、rosbag_recorder

##### 动机：为什么不直接使用`rosbag record -a`

如果直接使用 `rosbag record -a`记录数据，会使用当前时间作为时间戳。后续再重放数据时，使用的时间戳也为record时的时间，而不是消息头部中的stamp。

##### 代码地址：



##### 具体代码：

在`bag.write()`的第三个参数中设置时间戳，不写则默认当前时间。

```python
#include "ros/ros.h"
#include "sensor_msgs/PointCloud.h"
#include "rosbag/bag.h"
#include "rosbag/view.h"

rosbag::Bag bag; // 声明一个rosbag

void alCallback(const sensor_msgs::PointCloud::ConstPtr& msg) {
    // 在这里处理接收到的PointCloud消息
    // 可以通过msg来访问消息的数据

    if (bag.isOpen()) {
        bag.write("/ars548_process/detection_point_cloud", msg->header.stamp, *msg);
    }
}

int main(int argc, char **argv) {
    ros::init(argc, argv, "my_subscriber");
    ros::NodeHandle nh;

    // 打开Bag文件以写入
    bag.open("/home/dearmoon/datasets/4DRadar/ours/bag/syd.bag", rosbag::bagmode::Write);

    ros::Subscriber sub = nh.subscribe<sensor_msgs::PointCloud>("/ars548_process/detection_point_cloud", 10, alCallback);

    ros::spin(); // 进入ROS主循环，等待消息

    bag.close(); // 关闭Bag文件

    return 0;
}

```

## D、具体步骤

### 一、使用wireshark将传感器数据转换为json文件

##### 1、使用wireshark打开抓取的pcapng文件

雷达厂商提供了传感器的lua插件，可以直接过滤，只保留`detectionlist`数据

![image-20240119161238901](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240119161238901.png)

##### 2、导出解析结果为JSON格式	![屏幕截图 2024-01-19 16:16:54](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/屏幕截图 2024-01-19 16:16:54.png)

### 二、RosDriverForARS548

##### 1、编译

1）下载项目：

```bash
git clone https://github.com/letMeEmoForAWhile/RosDriverForARS548.git
```

2）在ars548_msg和ars548_process下创建include文件夹

- 不创建会报错：https://github.com/wulang584513/ARS548-demo/issues/3

3）安装依赖

安装nlohmann库

- 见3.2.2 C++读取json文件部分

4）终端切换到RosDriverForARS548根路径并编译

```bash
catkin_make
```

5）保存环境变量

```bash
vim ~/.bashrc
```

在最后一行如下内容。需要将`PATH_TO_RosDriverForARS548_FOLDER`改成RosDriverForARS548的路径

```bash
source PATH_TO_RosDriverForARS548_FOLDER/devel/setup.bash
```

```bash
source ~/.bashrc
```

##### 2、运行

1）在`ars548_process_node.cpp`中修改`json_file_path`为步骤一中的json文件路径

2）启动节点

```bash
roslaunch ars548_process ars548_process.launch
```

