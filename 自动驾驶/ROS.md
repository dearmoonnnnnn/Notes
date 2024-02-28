# 零、问题和概念

## 资料

##### 1、Autolabor教程

 http://www.autolabor.com.cn/book/ROSTutorials/



## 问题

##### 1、如何知道一个bag文件中的数据结构

使用rosbag命令行工具

1. **查看bag文件中的话题和消息数**

   ```
   rosbag info <your_bag_file.bag>
   ```

2. 查看特定话题的消息的内容

   ```
   rosbag echo -n 1 <your_bag_file.bag> <topic_name>
   ```

3. 查看消息的定义

   ```
   rosmsg show <message_type>
   ```


##### 2、话题和消息的关系

在ROS（Robot Operating System）中，话题（Topic）和消息（Message）之间存在明确的关系，这种关系对于理解ROS的通信机制至关重要。以下是话题和消息之间的关系概述：

1. **定义**:
   - **话题（Topic）**：它是一个命名的通道，节点可以发布消息到这个通道，或从这个通道订阅消息。话题为不同的节点提供了一种进行数据通信的方式。
   - **消息（Message）**：它是传输在话题上的数据单元。消息有一个特定的类型，定义了数据的结构，例如整数、浮点数、数组或更复杂的数据结构。

2. **关系**:
   - 每个话题都有一个与之关联的消息**类型**。例如，一个可能的话题是`/camera/image_raw`，其消息类型可能是`sensor_msgs/Image`，表示图像数据。
   - 节点可以**发布**一个特定类型的消息到一个话题。
   - 同时，其他节点可以**订阅**这个话题，以接收该类型的消息。
   - 虽然一个话题只能有一个消息类型，但多个不同的节点可以发布和订阅这个话题，允许多对多的通信。

3. **示例**:
   - 一个摄像头节点可能会从摄像头硬件获取图像数据，并发布一个类型为`sensor_msgs/Image`的消息到`/camera/image_raw`话题。
   - 同时，一个图像处理节点可以订阅`/camera/image_raw`话题，接收图像消息并进行处理。
   
4. **工具与命令**:
   - 使用`rostopic list`可以列出当前活跃的所有话题。
   - 使用`rostopic type <topic_name>`可以获取一个话题的消息类型。
   - 使用`rosmsg show <message_type>`可以查看一个特定消息类型的结构。

总之，**话题是数据传输的通道，而消息是在这些通道上实际传输的数据**。通过这种发布-订阅机制，ROS节点可以灵活、高效地进行通信。

##### 3、node和nodelet的关系

`node` 和 `nodelet` 都是ROS (Robot Operating System) 中的核心概念，但它们之间存在一些关键差异。以下是它们的关系和区别：

1. **Node（节点）**:
   - 在ROS中，节点是一个可执行的过程，它与其他节点使用ROS通信机制（如话题、服务和行动）交互。
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
   - 当你的ROS应用需要高效地处理和传输大型数据时（如在机器人的感知和图像处理任务中），使用`nodelet`可能是一个好选择。
   - 对于更通用的、不涉及大型数据处理的任务，常规的节点可能更为简单和方便。

总的来说，`node`和`nodelet`都是ROS中进行任务和算法处理的实体，但`nodelet`提供了一种更高效的方法，特别是当需要在算法之间交换大型数据时。

##### 4、一个节点可以发布或者接收多个主题吗

是的，一个节点可以发布多个主题。同样地，一个节点也可以订阅多个主题。在ROS中，节点的设计经常需要与多个其他节点通信，因此它们可能需要发布和/或订阅多个不同的主题。

例如，考虑一个机器人的移动控制节点。这个节点可能需要：
- 发布到一个`/cmd_vel`主题，发送速度命令给机器人的驱动器。
- 订阅一个`/laser_scan`主题，从激光雷达获取障碍物信息。
- 发布到一个`/robot_status`主题，提供关于机器人当前状态的更新。

因此，单个节点可以与ROS网络中的多个主题互动，既作为发布者又作为订阅者。这种灵活性使得ROS的节点可以在复杂的系统中灵活地工作，同时保持相对的解耦。

##### 5、nodelet节点在launch中配置的时候，节点所在的包(pkg)为什么是nodelet

```xml
<node pkg="nodelet" type="nodelet" name="radar_graph_slam_nodelet" args="load radar_graph_slam/RadarGraphSlamNodelet $(arg nodelet_manager)" output="screen">
```

中，节点`RadarGraphSlamNodelet`虽然是在包`radar_graph_slam`中定义，但是所属的包为`nodelet`

原因如下：

- 在ros中，`nodelet`是一个专门设计的框架，允许多个节点在同一个进程中运行，从而避免了跨进程通信的开销。
  - 为了让一个节点成为`nodelet`，它必须作为共享库编译，并链接到`nodelet`库中。
- 当我们运行一个`nodelet`时，不是直接运行该`nodelet`。运行的是**`nodelet`可执行文件**，并且告诉它要加载哪个特定的**`nodelet`插件**。
  - 而**nodelet可执行文件**存在于**nodelet包**中，所以节点所在的包为nodelet包。
  - 上例`load radar_graph_slam/RadarGraphSlamNodelet $(arg nodelet_manager)`相当于加载nodelet插件

##### 6、将melodic版本下能够跑通的项目迁移到noetic版本中

修改workspace/src路径下的CMakeList.txt即可。直接将neotic中能跑通的CMakeList.txt文件内容拷贝。

## 概念

##### 1、节点

节点主要执行计算处理 。ROS被设计为细粒度的模块化的系统;一个机器人控制系统通常有很多节点组成 。例如，一个节点控制激光测距仪，一个节点控制轮电机，一个节点执行定位，一个节点执行路径规划，一个节点提供系统图形界面，等等。一个ROS节点通过ROS客户端库 [client library](http://wiki.ros.org/Client Libraries)编写，例如 [roscpp](http://wiki.ros.org/roscpp) o或[rospy](http://wiki.ros.org/rospy)

##### 2、ROS Master

在ROS (Robot Operating System) 中，`ROS Master` 是一个关键组件，它提供命名和注册服务，以便其余的节点能够找到彼此并进行通信。下面是有关`ROS Master`的一些详细信息：

1. **命名和注册服务**：
   - 当一个节点启动时，它会向`ROS Master`注册自己，说明它提供了哪些话题、服务等。
   - 同样地，当一个节点想要找到某个特定的话题或服务时，它会询问`ROS Master`来获得相关的信息。

2. **参数服务器**：
   - `ROS Master` 还包含一个参数服务器，它允许数据在节点之间被存储和共享。这些数据通常是配置参数，例如机器人的某些固定参数或启动时的配置设置。

3. **不进行实际的数据通信**：
   - 虽然节点通过`ROS Master`找到彼此，但当它们开始通信（例如，通过发布和订阅话题）时，数据流不会经过`ROS Master`。换句话说，`ROS Master` 只是提供了一个查找服务，不负责节点之间的实际数据传输。

4. **单点故障**：
   - 在ROS系统中，`ROS Master`是一个关键的组件，如果它停止工作，节点之间将无法找到彼此，导致整个系统的通信断裂。
   - 在实际的机器人系统中，需要确保`ROS Master`是可靠和稳定的，以确保整体系统的稳定性。

5. **启动和终止**：
   - `roscore` 命令用于启动`ROS Master`，以及其他几个核心的ROS背景进程。
   - 当`roscore` 运行时，你可以开始启动其他节点，并让它们通过`ROS Master`进行通信。
   - 关闭`roscore`将会终止`ROS Master`和相关的进程。

总之，`ROS Master`在ROS中扮演着中央目录的角色，允许节点发现彼此并建立通信连接。

##### 3、bags



# 一、安装ROS

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

有些版本的VScode可能与ros不兼容，报一些莫名其妙的错误，这时可以下载历史版本。

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



# 三、ROS的通信方式

ROS是进程(也称Nodes)的分布式框架。ROS中的每一个功能点都是一个单独的进程，每一个进程单独运行。

不同节点之间的通信方式有三种

- 基于同步RPC样式通信的**服务(services)**机制：请求响应模式
- 基于异步流媒体数据的**话题(topics)**机制：发布订阅模式
- 用于数据存储的**参数服务器(Parameter Server)**：参数共享模式

## 2.1 话题通信

适用于不断更新的、少逻辑处理的数据传输场景，比如各种使用传感器的场景。

### 2.1.1 理论模型

![image-20230901161228493](/home/dearmoon/snap/typora/82/.config/Typora/typora-user-images/image-20230901161228493.png)

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



### 2.1.2 具体示例

##### 发布话题的三个步骤(python)

1、建立publisher

```python
pcl_pub = rospy.Publisher('kitti_point_cloud', PointCloud2, queue_size = 10)
```

2、读取数据 (如果要发布Marker类型，可能不需要这一步)

点云数据被存在pont_cloud数组中

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

1. **初始化ROS节点**：使用`ros::init()`函数初始化节点。
2. **创建节点句柄**：使用`ros::NodeHandle`创建句柄。
3. **定义消息回调函数**：创建一个函数，该函数会在消息到达时被调用。
4. **订阅话题**：使用`node_handle.subscribe()`函数订阅一个话题，并关联回调函数。
5. **进入事件循环**：使用`ros::spin()`或`ros::spinOnce()`进入事件循环，等待并处理消息。

以下是一个简单的示例，该节点订阅名为`/chatter`的话题，该话题的消息类型为`std_msgs::String`：

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

- 当收到`/chatter`话题的新消息时，`chatterCallback`函数会被调用。
- `ros::spin()`会保持你的节点在事件循环中，直到节点被关闭或ROS被关闭。

确保你已经为`std_msgs`和其他必要的包设置了依赖，并且在你的`CMakeLists.txt`中正确地设置了编译选项。

## 2.2 服务通信

## 2.3 参数服务器

# 四、launch文件

##### 使用launch的动机

一个程序可能需要启动多个节点，比如ROS内置的小乌龟案例，控制小乌龟运动需要分别启动roscore,乌龟页面节点，键盘控制节点。如果每次都调用rosrun逐一启动，效率底下。

解决方案：使用roslaunch命令，集合launch文件启动管理节点。

##### 概念

launch文件是一个XML格式的文件，可以启动本地和远程的多个节点，还可以在服务器参数中设置参数。

##### 作用

简化节点的配置和启动，提高ROS程序的启动效率。

##### 调用launch文件

```
roslaunch 包名 xxx.launch
```

PS：roslaunch命令执行launch文件时，首先会判断是否启动了roscore，如果启动了则不再启动，否则自动调用roscore。

##### 常见参数

1. **节点（node）**:

   - 使用`<node>`标签来启动一个ROS节点。<!-- roslaunch命令不能保证按照node的声明顺序来启动节点，因为节点的启动是多进程的 -->

   - 可以为节点指定名称、所属的包、运行的二进制文件、命名空间等。

   - 示例：

     ```xml
     <!-- 必要属性 -->
     <!-- name：节点名称; pkg：节点所属的包; type：节点类型-->
     
     <!-- 可选属性 -->
     <!-- args = "XXX XXX XXX"：将参数传递给节点; output = "log | screen": 日志发送目标(log日志文件或者屏幕) -->
     <!-- machine = "机器名"： 启动另一台机器上的节点 -->
     <!-- respan = "ture | false" : 如果节点退出，是否自动重启，传感器节点、GUI节点一般会设置 -->
     <!-- respan_delay = "N": 当respan为true时，重启节点时会延时N秒 -->
     
     <node name="talker" pkg="rospy_tutorials" type="talker" />
     ```

2. **包含其他的launch文件（include）**:

   - 使用`<include>`标签包含另一个`.launch`文件，使得`.launch`文件可以模块化。

   - 示例：

     ```xml
     <!-- $(find 包名)：可以定位到～/catkin_ws/src/包名 -->
     <include file="$(find another_package)/launch/another_file.launch"/>
     ```

3. **环境变量（env）**:

   - 使用`<env>`标签设置环境变量，这些环境变量对于启动的节点是可见的。

   - 示例：

     ```xml
     <env name="ROBOT_INITIAL_POSE" value="-x 0 -y 0 -Y 0"/>
     ```

4. **重新映射（remap）**:

   - 使用`<remap>`标签将一个话题名或服务名从一个名称重映射到另一个名称。

   - 使得订阅者可以与发布者关注同一个话题，建立通信。

   - 示例：

     ```xml
     <node name="talker" pkg="rospy_tutorials" type="talker" >
       <remap from="chatter" to="new_chatter" />
     </node>
     ```

5. **组（group）**:

   - 对节点分组，具有ns属性，让节点归属于某个命名空间

   - 示例：

     ```xml
     <group ns="robot1">
       <node ... />
       <node ... />
     </group>
     ```

6. **参数（param）**:

   - **作用：**向参数服务器设置参数。

   - **参数源：**可以在标签中通过value指定，也可以通过外部文件加载。

   - **使用位置：**可以添加在launch内，node外；也可以添加在node内。`<node>`标签中作为子标签时，相当于私有命名空间。

   - 示例： 

     ```xml
     <!-- name = "命名空间/参数名" ：参数名称，可以包含命名空间-->
     <!-- value = "XXX" : 可选，定义参数值，如果省略，必须指定外部文件作为参数源 -->
     <!-- type = "str | int | double | yaml" ：可选，指定参数类型，如果未指定，roslaunch会根据一定规则尝试确定参数类型 -->
     
     <launch>
         <!-- 在launch内，node外 -->
         <!-- 向参数服务器设置一个名称为param_A, 类型为int， 值为100的参数-->
     	<param name="param_A" type = "int" value = "100" />
     	
         <!-- 在node内-->
         <!-- 也会在参数服务器中设置参数，但参数存在前缀-->
         <node name="talker" pkg="rospy_tutorials" type="talker" />>
         	<param name="param_B" type="double" value="3.14" /> 
         </node>
         
     </launch>
     ```

     启动launch文件后，在cmd中查看参数服务器中的参数

     ```
     rosparm list 
     ```

     显示：

     ```c
     /param_A
     /talker/param_B
     ```

7. **参数文件加载（rosparam）**:

   - 从`YAML`文件导入参数，或者将参数导出到`YAML`文件，也可以用来删除参数

   - **使用位置**：同param

   - 示例：

     ```xml
     <!-- command="load | dump |delete "： 加载、导出或删除参数 -->
     <!-- param="参数名称" -->
     <!-- ns="命名空间"： 可选-->
     <rosparam file="$(find my_package)/param/config.yaml" command="load" />
     ```

8. **命令行参数（arg）**:

   - 用于动态传参，类似于函数的参数。

   - 示例：

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

     也可以在命令行中传递参数的值，

     ```
     roslaunch 程序名 XXX.launch car_length:=0.6
     ```

     这时候参数car_length不再是默认的0.5, 而是0.6

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

## 5.1 写bag文件(C++)

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

## 5.2 读bag文件(C++)

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

## 5.3 命令行

http://wiki.ros.org/rosbag/Commandline

### rosbag play

- `rosbag play -- clock ***.bag`

  使用`--clock`参数时，rosbag play 会读取bag文件中的时间戳信息，并使用这些时间戳来模拟消息的发布时间。

  这对于 replay 数据时保持与记录时相同的时间关系是很有用的，特别是在涉及到需要同步的多个话题时。

## 5.4 bag转txt

```bash
rostopic echo -b <bag_name>.bag -p /<topic_name> > <new_name>.txt
```

举例

```bash
rostopic echo -b data1.bag -p /tag_detections > data1.txt
```

# 六、PCL库

## 6.1 表示点云数据的四种方式

- sensor_msgs::PointCloud —— ROS message，已弃用
  - 由于`sensor_msgs::PointCloud2`更加灵活和高效，所以已弃用。
  - 数据字段包括`points`（一个3D点列表）和`channels`（关于点的额外数据，如强度）。

- sensor_msgs::PointCloud2 —— ROS message 
  - 旨在比其前身更加灵活和高效。
  - 包含一个统一的`data`字段，其中包含所有点数据。
  - 使用一个灵活的消息格式（类似于ROS中的图像表示方式）。
  - 虽然更加高效，但直接使用它处理比较复杂。

- pcl::PCLPointCloud2 —— PCL数据结构，主要是为了与ROS兼容
  - PCL的数据结构，与`sensor_msgs::PointCloud2`非常匹配。
  - 存在的目的是允许PCL与ROS之间轻松转换，而不丢失任何信息。
  - 虽然它与ROS消息非常匹配，但通常不会在PCL中直接使用这种格式来操作点云。相反，会将其转换为`pcl::PointCloud<T>`以进行大多数处理任务。

- pcl::PointCloud\<T> —— 标准的PCL数据结构
  - PCL中用于点云的标准且最常用的数据结构。
  - 是模板化的，其中`T`是点的类型，例如`pcl::PointXYZ`、`pcl::PointXYZRGB`、`pcl::PointNormal`等。
  - 提供了许多方便的方法，允许轻松访问点。
  - 旨在为点云处理任务提供高效和用户友好的方式。


当我们同时使用ROS和PCL时，典型的工作流如下：

1. 在一个ROS节点中接收一个`sensor_msgs::PointCloud2`消息的点云。
2. 如果需要使用pcl处理它，我们会将其转换为`pcl::PointCloud<T>`，进行处理。
3. 最后将其转换回`sensor_msgs::PointCloud2`，用以发布或者在ROS中进一步使用。<!-- 在ROS的pcl_conversions包中有函数，可以方便地在这些类型之间转换 -->

### 6.1.1 sensor_msgs/PointCloud2

### 6.1.2 pcl/PointCloud\<T>

# 七、从零创建ROS工程

##### 1、在ROS的工作目录下使用catkin_create_pkg命令创建一个新的ROS包。

假设工作目录是`catkin_ws`

```bash
cd ~/catkin_ws/src
catkin_create_pkg my_bag_recorder std_msgs sensor_msgs rosbag roscpp
```

上述命令在catkin_ws/src目录下创建一个名为`my_rosbag_recorder`的新ROS包

- `catkin_create_pkg`参数：

  ```shell
  catkin_create_pkg <package_name> [depend1] [depend2] [depend3] ...
  ```

  - `<package_name>`：包名

  - `[depend1]`, `[depend2]`, `[depend3]`：该包依赖的其他ROS包的名称。这些包将被添加到`package.xml`文件中的`<depend>`部分

##### 2、编写C++程序

在`my_rosbag_recorder`包的`src`目录下，创建c++源文件，如`my_rosbag_recorder.cpp`，并编写代码

##### 3、编辑CMakeLists.txt文件

`catkin_create_pkg`会自动完成该步

在`my_rosbag_recorder`包的根目录下，打开`CMakeLists.txt`文件，添加适当的rosbag依赖项。

```cmake
find_package(catkin REQUIRED COMPONENTS
  std_msgs
  sensor_msgs
  roscpp
  rosbag
)
```

##### 4、构建工程

回到ROS工作区目录，执行以下命令编译工程

```bash
catkin_make
```

##### 5、运行ROS核心

在终端中，运行ROS核心

```
roscore
```

##### 6、运行节点

```
rosrun my_rosbag_recorder my_rosbag_recorder
```

- rosrun参数：第一个参数为ROS包的名称，第二个参数为ROS包中编写的节点的执行文件的名称。	

## 遇到的问题：

##### 1、执行rosrun时显示包找不到

**原因**： 

没有将工作区添加到环境变量中

**解决方法**：

1. 打开/.bashrc文件

   ```bash
   vim ~/.bashrc
   ```

2. 在`.bashrc`文件中最后一行添加`source /home/dearmoon/shares/share_ubuntu/projects/bag_recorder/devel/setup.bash` 

3. source配置文件，使其立即生效

   ```
   source ~/.bashrc
   ```

#####  2、找到了包，但是找不到包中的可执行文件

**原因**：没有在包下的CMakeLists.txt文件中正确配置

**解决方法**：

1. 打开包中的CMakeLists.txt文件。

2. 使用`add_executable()`命令指定可执行文件

   ```cmake
   add_executable(my_bag_recorder src/bag_write.cpp)
   ```

   - 第一个参数是生成的可执行文件名称，第二个参数为cpp源码文件

3. 使用`target_link_libraries()`命令将你的包链接到你的可执行文件，确保它能够找到所有的依赖项。

   ```cmake
    target_link_libraries(my_bag_recorder
      ${catkin_LIBRARIES}
    )
   ```

4. 重新执行catkin_make

# 八、nodelet

## 问题

##### 1、nodelet定义的三种句柄有什么区别

- 全局节点句柄
  - **作用范围：**提供对ROS全局命名空间中的所有主题、服务和参数的。
  - **线程安全性：**通常是单线程的，在多线程环境中可能需要额外的同步机制。
- 多线程节点句柄
  - **作用范围：**多线程句柄提供对ROS全局命名空间中的所有主题、服务和参数的访问，类似于全局节点句柄
- 私有节点句柄
- 

# 九、catkin_make

