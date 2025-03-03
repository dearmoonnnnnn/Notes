# 零、问题和概念

## 概念：

##### 1. RCS

- 定义：

  Radar Cross Section, 雷达散射截面积，是度量目标在雷达波照射下所产生的回波强度的一种物理量，它是目标的假想面积。

- 作用：

  它有助于确定雷达如何“看”这个目标——即目标在雷达上显得有多明显。例如，一个大的金属飞机可能会有一个很大的RCS，使其在雷达上很容易被检测到。而设计有隐身特性的飞机，其形状和表面被特别设计以散射雷达波，从而减少其RCS值，使其在雷达上更难被检测到。

- 影响RCS的相关因素

  RCS与目标的尺寸和形状、材料、目标视角、雷达工作频率及雷达发射和接收天线的==极化==有关。

- RCS的单位

  - RCS的单位是面积单位
    - 通常使用平方米(m^2^)。例如，一个物体的RCS是1m^2^。这并不意味着这个物体的实际物理面积是1m^2^,而是说这个物体反射雷达波的“效果”与一个完美反射1m^2^面积的目标相同。
  - 或者以分贝(dB)为单位
    - `ars548`4D毫米波雷达的手册中，RCS单位为$dBm^2$（即dBsm，dB square meters），表示的是RCS相对于一个平方米的参考面积的对数比。
    - 例如，如果某个目标的RCS是-10dBsm，意味着它的反射能力相当于一个平方米的参考面积的反射能力的10分之一。

  

## 问题：

##### 1. ars548_process_node 未接受到网口数据，从网口读数据的代码在哪？

源代码未给出,需要在 `receiveThread()` 函数中添加

##### 2. 在 ars548_process 包中添加了一个文件，需要在哪些文件中配置，才能与 ROS 适配

当你在 ROS 包中添加新文件时，为了确保`catkin_make`可以正确地编译和链接这些文件，你可能需要更新以下文件：

1. **CMakeLists.txt**: 这是 Catkin build 系统使用的主要文件。你需要确保你的新源文件被包含在某个可执行文件或库的构建中。

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

##### 3. ProcessRadarData函数，如何传递

##### 4. 为什么 obj 有 header 成员，而 obj_list 没有，添加时间戳的代码应该在 obj 中还是obj_list 中

##### 5. 从 bag 转换为t xt 文件后,第一行显示了 point 的数量，但是从 wireshark 可知每一帧的点云数量不一样，那么第一行的点的数量由什么决定呢?

在将 bag 文件转换为 txt 文件时，第一行显示的点是读取的 bag 文件第一个点云消息的点数量。

第一行的点数量不代表所有点云消息的实际数量，因为每一帧的点云数量可能不一样。

##### 6. 在 info_convert_node 文件的回调函数中添加输出， 命令行不显示输出

`ars548_process.lanuch` 文件中，启动 `info_convert_node` 时未添加参数：`output="screen"`

# 一、根据HesaiLidar_Swift_ROS实现（同4DRaSLAM,未实现，弃用）

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

# 二、根据 ARS548-demo 实现

## 1. 整体思路：

##### 两个项目：

- RosDriverForARS548：
  - 接受 json 格式的传感器数据，并用以 ROS 消息的格式发布
- rosbag_recorder
  - 订阅 RosDriverForARS548 的话题，并生成 bag 文件

##### 流程：

1. 使用 wireshark 将 pcap/pcapng 解析结果保存为 json 文件

   注意需要 ars548 插件

1. 使用 RosDriverForARS548，直接从 json 文件读取解析结果，和字符流，并发布相关话题

1. 同时使用 rosbag_recorder 订阅话题，转为 bag 文件

##### 运行环境：

- Ubuntu18.04  + ROS melodic 或 Ubuntu20.04 + ROS noetic

- nlohmann-json库
- wireshark

## 2. RosDriverForARS548

##### 代码地址：

https://github.com/wulang584513/ARS548-demo/tree/master

##### 修改后的代码：

https://github.com/letMeEmoForAWhile/RosDriverForARS548

### 2.1 如何读取数据流

##### 动机：

原始的 ars548-demo 项目没有提供获取数据流的代码，需要自己编写。

##### 思路一：从 pcap 文件读取数据

由于 pcap 保存的是原始字节流，无法读取重组后的字节流，因此放弃该思路。

##### 思路二：以 json 文件的形式保存 wireshark 的解析结果，并读取

利用 `lua` 插件，`wireshark` 可以直接解析出雷达点信息（位置、多普勒速度等）。将解析结果保存为 json，直接从 json 文件中读取信息。

##### 2.2.1 json 文件的数据结构

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

##### 2.1.2 C++ 读取 json 文件 | 安装 nlohmann 库

1. 将 wireshark 解析结果保存在 json 文件

2. 安装相关的库，apt 安装或者源码安装选择一个

   - apt 安装

      ```bash
      sudo apt update
      sudo apt install nlohmann-json3-dev
      ```


   - 源码安装: https://blog.csdn.net/jiemashizhen/article/details/129275915

     ```bash
     // 在你喜欢的位置
     git clone  https://github.com/nlohmann/json.git
     cd json
     mkdir build
     cd build
     cmake ..
     make
     sudo make install
     ```


3. 读取 json 文件，并将内容解析到 json 对象中 `nlohmann::json j`

4. 遍历 json，如果当前对象满足条件，返回当前数据包 `j[i]`,其格式仍为 `nlohmann::json`

   - 在 `nlohmann::json` 库中，JSON对象、数组、字符串、数字、布尔值和null都是使用 `nlohmann::json` 类型来表示的。
   - 当你通过索引、键或其他方法访问 `nlohmann::json` 对象中的元素时，返回的仍然是 `nlohmann::json` 类型，不过其内部的实际数据可能是字符串、数字、布尔值、数组、对象或null。

### 2.2 修改发布的点云消息，使其包含多普勒速度和强度信息

##### 动机：

原始代码发布的点云消息只包含位置信息（x、y、z），没有包含多普勒速度和强度信息。

在  `wireshark`  中查看点的消息内容，存在多普勒速度  `Detection Radial Velocity`  和强度信息  `RCS` 。

![image-20240301130657374](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240301130657374.png)

#### 思路1：自定义一个带有多普勒速度的点消息格式（报错）

##### 1、自定义消息类型，包含位置和多普勒速度信息

这里在 `ars548_msg` 这个包中自定义一个消息类型。

```yaml
# 在你的 ROS 包中创建一个名为 `DopplerPoint.msg` 的消息文件

# 文件路径: RosDriverForARS548/src/ars548_msg/msg/DopplerPoint.msg

geometry_msgs/Point32 position
float32 doppler_velocity
```

##### 2、修改配置文件

- `CMakeList.txt` 文件中的 `add_message_files` 选项添加 `DopplerPoint.msg`

  ```cmake
  add_message_files(
    FILES
    ‘其他msg’
    DopplerPoint.msg
  )
  ```

- 在 `generate_messages` 函数的 `DEPENDENCIES` 参数添加依赖。

  ```cmake
  generate_messages(
    DEPENDENCIES
    ‘其他依赖’
    geometry_msgs  # DopplerPoint.msg 依赖于 geometry_msgs，
  )
  ```

- 使得 `ars548_msg` 可以找到 `geometry` 包

  - 在  `package.xml` 文件中添加以下行

    ```xml
    <build_depend>geometry_msgs</build_depend>
    <exec_depend>geometry_msgs</exec_depend>
    ```

  - 在 `CMakeLists.txt` 中添加 `find_package` 声明

    ```cmake
    find_package(catkin REQUIRED COMPONENTS
      ‘需要找到的其他包’
      geometry_msgs
    )
    ```

##### 3、修改 `detectionReceie()` 函数

```c++
#include "ars548_msg/DopplerPoint.h"  // 包含自定义消息类型

void detectionReceive(const ars548_msg::DetectionList& msg)
{
    uint size = msg.detection_array.size();

    ars548_msg::DopplerPoint doppler_point;  // 自定义消息类型

    if(size > 0)
    {
        cloud.header.frame_id = "world";
        cloud.header.stamp = msg.detection_array[0].header.stamp;
        cloud.points.clear();
        for(uint i = 0; i < size; i++) 
        {
            doppler_point.position.x = msg.detection_array[i].f_x;
            doppler_point.position.y = msg.detection_array[i].f_y; 
            doppler_point.position.z = msg.detection_array[i].f_z; 
            doppler_point.doppler_velocity = msg.detection_array[i].doppler_velocity; // 将多普勒速度添加到自定义消息中

            cloud.points.push_back(doppler_point);
        }

        detections_cloud_pub.publish(cloud);
    }
}

```

##### 报错：

无法将 `ars548_msg::DopplerPoint` 类型的对象添加到 `std::vector<geometry_msgs::Point32>` 类型的容器中

![image-20240229192543291](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240229192543291.png)

不能直接修改点的消息类型。查阅官方文档可知，`sensor_msgs::PointCloud`  消息类型包含一个字段  `channels`，它是一个  `sensor_msgs::ChannelFloat32`  类型的数组，用于存储每个点的附加信息。

#### 思路2：将点的多普勒速度存放在 channels 字段中

1. 首先，在 `sensor_msgs::PointCloud`  中添加一个通道，用于存储多普勒速度。假设你命名这个通道为 "doppler_velocity"，你需要添加一个  `sensor_msgs::ChannelFloat32` 类型的通道。
2. 然后，在接收雷达数据的回调函数中，将每个点的多普勒速度存储在 `channels[0].values[i]`  中，其中 `i` 表示第 `i` 个点。

```c++
#include <sensor_msgs/PointCloud.h>
#include <sensor_msgs/ChannelFloat32.h>
#include <ars548_msg/DetectionList.h> // 假设这是雷达消息类型

void detectionReceive(const ars548_msg::DetectionList& msg)
{
    uint size = msg.detection_array.size();

    sensor_msgs::PointCloud cloud;  
    geometry_msgs::Point32 p;
    sensor_msgs::ChannelFloat32 doppler_channel; // 定义用于存储多普勒速度的通道

    if(size > 0)
    {
        cloud.header.frame_id = "world";
        cloud.header.stamp = msg.detection_array[0].header.stamp;
        cloud.points.clear();
        doppler_channel.name = "doppler_velocity"; // 设置通道名称

        for(uint i = 0; i < size; i++) 
        {
            p.x = msg.detection_array[i].f_x;
            p.y = msg.detection_array[i].f_y; 
            p.z = msg.detection_array[i].f_z; 

            cloud.points.push_back(p);

            // 将多普勒速度存储到通道中
            doppler_channel.values.push_back(msg.detection_array[i].f_RangeRate); 
        }

        // 将通道添加到点云消息中
        cloud.channels.push_back(doppler_channel);

        detections_cloud_pub.publish(cloud);
    }
}

```

**同理，添加了强度信息的代码如下：**

```c++
#include <sensor_msgs/PointCloud.h>
#include <sensor_msgs/ChannelFloat32.h>
#include <ars548_msg/DetectionList.h> // 假设这是雷达消息类型

void detectionReceive(const ars548_msg::DetectionList& msg)
{
    uint size = msg.detection_array.size();

    sensor_msgs::PointCloud cloud;  
    geometry_msgs::Point32 p;
    sensor_msgs::ChannelFloat32 doppler_channel;         // 定义用于存储多普勒速度的通道,存储雷达点的f_RangeRate
    sensor_msgs::ChannelFloat32 intensity_channel;       // 定义用于存储信号强度的通道，存储雷达点的RCS

    if(size>0)
    {
        cloud.header.frame_id = "world";
        cloud.header.stamp = msg.detection_array[0].header.stamp;
        cloud.points.clear();

        // 设置通道名称
        doppler_channel.name = "doppler_velocity"; 
        intensity_channel.name = "intensity";

        for(uint i=0;i<size;i++) 
        {
            p.x = msg.detection_array[i].f_x;
            p.y = msg.detection_array[i].f_y; 
            p.z = msg.detection_array[i].f_z; 

            cloud.points.push_back(p);

            // 将多普勒速度和信号强度存储到对应通道中
            doppler_channel.values.push_back(msg.detection_array[i].f_RangeRate); 
            intensity_channel.values.push_back(msg.detection_array[i].s_RCS);
        }

        // 将通道添加到点云消息中
        // cloud.channels[o].value[i]表示点i的多普勒速度
        // cloud.channels[1].value[i]表示点i的信号强度
        cloud.channels.push_back(doppler_channel);
        cloud.channels.push_back(intensity_channel);

        detections_cloud_pub.publish(cloud);
    }
}
```

### 2.3 test_radar_input_node 报错

- 暂时用不到该节点

- 在 `CMakeLists.txt` 中注销相关可执行文件

```
# add_executable(test_radar_input_node src/test_radar_input_node.cpp)
# target_link_libraries(test_radar_input_node
#   ${catkin_LIBRARIES}
# )
```

- 也可直接在第一次编译时忽略该问题，第二次编译不会再报错

## 3、rosbag_tools

##### 动机：为什么不直接使用 `rosbag record -a`

如果直接使用 `rosbag record -a` 记录数据，会使用当前时间作为时间戳。后续再重放数据时，使用的时间戳也为 record 时的时间，而不是消息头部中的stamp。

##### 代码地址：

https://github.com/letMeEmoForAWhile/rosbag_tools.git

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

# 三、运行步骤

### 零、配置环境

##### 1. 安装 ROS

参考如下链接：

官方文档：http://wiki.ros.org/ROS/Installation

其他教程：https://blog.csdn.net/sea_grey_whale/article/details/132023522

Autolabor（推荐）：http://www.autolabor.com.cn/book/ROSTutorials/chapter1/12-roskai-fa-gong-ju-an-zhuang/124-an-zhuang-ros.html

##### 2. 安装 wireshark

1. 安装 wireshark 软件

   ```bash
   sudo apt install wireshark
   ```

   出现弹窗，选择“是”，允许 Wireshark 捕获网络数据包。

2. 配置 wireshark 插件，使其能够解析 ars548 传感器数据。

   找到插件需要放置的位置：`/usr/lib/x86_64-linux-gnu/wireshark/plugins`

   ![wireshark插件位置](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/wireshark插件位置.png)

   在插件所在目录，复制插件到上述位置

   ```bash
   sudo cp packet-ars548（大陆原版）.lua /usr/lib/x86_64-linux-gnu/wireshark/plugins
   ```

   重启 `wireshark`

##### 3. 安装 nlohmann

见 2.1.2

##### 4. 安装 libpcap

`RosDriverForARS548` 需要该库。

```bash
sudo apt-get install libpcap-dev
```

### 一、使用 wireshark 将传感器数据转换为 json 文件

##### 1. 使用 wireshark 打开抓取的 pcapng 文件

雷达厂商提供了传感器的 lua 插件，可以直接过滤，只保留 `detectionlist` 数据

![image-20240119161238901](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240119161238901.png)

**lua 插件修改：RCS 值处理**

- 使用 VSCode 查看`lua` 插件，发现解析 RCS 字段时使用无符号整型 `uint8` 作为数据类型，解析后的数据范围为 [0 ~ 255]
  - 使用 wireshark 查看，发现大部分点的 RCS 值为 200 以上

- 查阅相关手册可知，毫米波 RCS 取值范围为 -128 ~ 127， 单位是 $dbm^2$
  - 查证可知，其他 4D 毫米波数据集中， RCS 存在很多负值。
- 应该使用 `int8_t`转换对应的 RCS 字节流数据，转换后取值范围为 [-128, 127] 

##### 2. 导出解析结果为 JSON 格式

![](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-2024-01-19-16:16:54.png)

### 二、RosDriverForARS548

##### 0. 修改路径

在 `ars548_process_node.cpp` 中修改 `json_file_path` 为步骤一中的 `json` 文件路径

##### 1. 编译

1）下载项目：

```bash
git clone https://github.com/letMeEmoForAWhile/RosDriverForARS548.git
```

2）在 ars548_msg 和 ars548_process 下创建 include 文件夹

- 不创建会报错：https://github.com/wulang584513/ARS548-demo/issues/3

3）终端切换到 RosDriverForARS548 根路径并编译

```bash
catkin_make
```

4）保存环境变量

```bash
vim ~/.bashrc
```

在最后一行如下内容。需要将 `PATH_TO_RosDriverForARS548_FOLDER` 改成 RosDriverForARS548 的路径

```bash
source PATH_TO_RosDriverForARS548_FOLDER/devel/setup.bash
```

```bash
source ~/.bashrc
```

##### 2. 运行

启动节点

```bash
roslaunch ars548_process ars548_process.launch
```

### 三、rosbag_tools

##### 0. 修改路径

在 `rosbag_recorder.cpp` 中修改`bag.open()`参数为输出的bag文件路径。

##### 1. 编译

1）下载项目：

```bash
git clone https://github.com/letMeEmoForAWhile/rosbag_tools.git
```

2）在根路径执行编译命令

```bash
catkin_make
```

5）保存环境变量

```bash
vim ~/.bashrc
```

在最后一行如下内容。需要将`PATH_TO_rosbag_recorder_FOLDER`改成rosbag_recorder的路径

```bash
source PATH_TO_rosbag_recorder_FOLDER/devel/setup.bash
```

```bash
source ~/.bashrc
```

##### 2. 运行

先启动 `RosDriverForARS548`，再启动该项目

```bash
rosrun rosbag_tools rosbag_recorder 
```

- 启动 RosDriverForARS548时，由于 json 读取较慢，可能需要等待较长时间。
- 及时关闭两个项目，避免自动进入第二轮转换。
