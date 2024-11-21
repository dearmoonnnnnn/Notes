# 问题

##### 1、command_callback如何才能调用？运行时，订阅者的消息来源是何处？

- 编写一个ROS节点发布`std_msgs::String`类型消息

  ```cpp
  #include <ros/ros.h>
  #include <std_msgs/String.h>
  
  int main(int argc, char** argv) {
      ros::init(argc, argv, "command_sender");
      ros::NodeHandle nh;
  
      ros::Publisher command_pub = nh.advertise<std_msgs::String>("/command", 10);
  
      // 创建一个命令消息
      std_msgs::String command_msg;
      command_msg.data = "time";  // 可以在这里更改为 "point_distribution" 或 "output_aftmapped"
  
      // 设置发布频率
      ros::Rate loop_rate(60);  // Hz
  
      while (ros::ok()) {
          // 发布命令
          command_pub.publish(command_msg);
          ROS_INFO("Published command: %s", command_msg.data.c_str());
  
          ros::spinOnce();
          loop_rate.sleep();  // 控制发送频率
      }
  
      return 0;
  }
  
  ```

或

- 使用`rostopicpub`发布`std_msgs::String`类型消息

  ```bash
  rostopic pub /command std_msgs/String "data: 'time'"
  ```

  ```cpp
  rostopic pub /command std_msgs/String "data: 'point_distribution'"
  ```

  ```cpp
  rostopic pub /command std_msgs/String "data: 'hello'"
  ```


# 方法1：使用 radar_slam 中

`radar_graph_slam_nodelet.cpp`节点中的`command_callback`

- 修改输出路径

  ```cpp
  fout.open("/home/dearmoon/datasets/NWU/日晴不颠簸低速3/estimated/stamped_pose_graph_estimate.txt", ios::out);
  ```




##### 出现的问题

程序可能会出现 out of memory 问题

# 方法2 ： ros record

##### 1. 记录 bag 数据

运行 SLAM 时，使用 ROS record 记录轨迹话题：`/radar_graph_slam/aftmapped_odom`

```bash
rosbag record -O stamped_traj_estimate.bag /radar_graph_slam/aftmapped_odom
```

##### 2. 转换格式

然后将其转换为 evo 可用格式

```bash
evo_traj bag XRGB.bag /radar_graph_slam/aftmapped_odom --save_as_tum
```

