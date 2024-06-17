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
      command_msg.data = "time";  // 你可以在这里更改为 "point_distribution" 或 "hello"
  
      // 等待发布者和订阅者连接
      ros::Duration(1.0).sleep();
  
      // 发布命令
      command_pub.publish(command_msg);
      ROS_INFO("Published command: %s", command_msg.data.c_str());
  
      ros::spinOnce();
  
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


# 定量评估

输出轨迹代码定位

当前猜测：

`radar_graph_slam_nodelet.cpp`节点中的`command_callback`

