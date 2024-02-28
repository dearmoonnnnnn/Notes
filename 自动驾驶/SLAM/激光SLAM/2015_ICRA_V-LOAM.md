# Question：

##### 1、论文如何将视觉和激光结合



# 具体架构

![image-20230505161404993](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230505161404993.png)

两个部分：视觉里程计部分

视觉里程计部分

- Feature Tracking，特征跟踪模块，提取和匹配连续图像之间的视觉特征。
- Depth Map Registration，深度图配准模块，在==局部的深度图（local depthmap）==上配准激光雷达点云。
- Frame to Frame Motion Estimation，帧到帧的运动估计模块，利用视觉特征计算运动估计。

激光雷达里程计部分(每次扫描都执行一次)

- Sweep to Sweep Refinement，扫描到扫描的改进模块，用来改进运动估计并去除点云中的畸变。
- Sweep to Map Registration，==匹配和配准当前构建的地图上的点云==，并发布相对于地图的传感器姿势。
- ==在高频图像帧率下，传感器姿态输出是两个部分变换的积分。==
