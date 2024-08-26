- [一、关键帧相关参数](#一关键帧相关参数)
- [二、点云融合阈值调整](#二点云融合阈值调整)
- [三、预处理相关参数](#三预处理相关参数)
  - [1. 距离过滤](#1-距离过滤)
  - [2. 离群点去除](#2-离群点去除)
  - [3. 去畸变](#3-去畸变)
- [四、定时器相关参数](#四定时器相关参数)
- [五、闭环检测相关参数](#五闭环检测相关参数)

# 零、概念&问题

## 概念

##### 1. 扫描上下文

- 扫描上下文是一种将点云数据转换为紧凑的描述符（desctiptor）的方法。
- 不同时间采集的点云数据可以通过比较其描述符来判断是否来自相同位置。

## 问题

##### 1. `scan_matching_nodelet` 和 `radar_graph_slam_nodelet` 为什么都需要各自的关键帧参数，它们的关键帧有什么区别

- `scan_matching_nodelet` 
  - 负责实时里程计估计，使用扫描匹配的方法来估计机器人的移动。
  - 关键帧参数决定什么时候关键帧被创造
  - 关键帧更加频繁
- `radar_graph_slam_nodelet` 
  - 负责创建和优化位姿图
  - 关键帧参数决定什么时候新的关键帧应该被加入到位姿图中
  - 关键帧更加稀疏

## 原始项目 ReadMe

The mapping quality largely depends on the parameter setting. In  particular, scan matching parameters have a big impact on the result.  Tune the parameters accoding to the following instructions:

### 3.1 Point cloud registration

- ***registration_method***

This parameter allows to change the registration method to be used for odometry estimation and loop detection. Our code gives five options: ICP, NDT_OMP, FAST_GICP, FAST_APDGICP, FAST_VGICP.

FAST_APDGICP is the implementation of our proposed  Adaptive Probability Distribution GICP, it utilizes OpenMP for  acceleration. Note that FAST_APDGICP requires extra parameters. Point uncertainty parameters:

- ***dist_var***
- ***azimuth_var***
- ***elevation_var***

*dist_var* means the uncertainty of a point’s range measurement at 100m range, *azimuth_var* and *elevation_var* denote the azimuth and elevation angle accuracy (degree)

### 3.2 Loop detection

- ***accum_distance_thresh***: Minimum distance beteen two edges of the loop
- ***min_loop_interval_dist***: Minimum distance between a new loop edge and the last one
- ***max_baro_difference***: Maximum altitude difference beteen two edges' odometry
- ***max_yaw_difference***: Maximum yaw difference beteen two edges' odometry
- ***odom_check_trans_thresh***: Translation threshold of Odometry Check
- ***odom_check_rot_thresh***: Rotation threshold of Odometry Check
- ***sc_dist_thresh***: Matching score threshold of Scan Context

### 3.3 Other parameters



All the configurable parameters are available in the launch file. Many are similar to the project ***hdl_graph_slam***.

# 一、关键帧相关参数

## 1. radar_graph_slam.launch

##### 1. keyframe_delta_trans_front_end

- 即 `scan_matching_odometry_nodelet.cpp` 中的 `keyframe_delta_trans` 参数
- 平移阈值

##### 2. keyframe_delta_trans_back_end

- `radar_graph_slam_nodelet` 中的 `key_frame_delta_trans_back` 参数
- 平移阈值

##### 3. keyframe_delta_angle

- `scan_matching_odometry_nodelet` 和 `radar_graph_slam_nodelet` 中的`keyframe_delta_angle`
  - 两者取值相同

- 旋转阈值

##### 4. keyframe_min_size 

- 两个节点都存在
  - `scan_matching_odometry_nodelet` 中为 100
  - `radar_graph_slam_nodelet` 中为 500
- 关键帧中包含的最小数据量

# 二、点云融合阈值调整

距离参数

- 1.0
  - 大部分帧激光雷达点数量只有十几、几十
  - 少数帧达到1000多点
- 5.0
  - 

# 三、预处理相关参数

## 1. 距离过滤

- 距离范围

- `z` 坐标范围

## 2. 离群点去除

- 方法的选择
  - 因为点云较为稀疏，使用 `RADIUS`
- 判断点是否为离群点的阈值

## 3. 去畸变

- 扫描周期的调整

# 四、定时器相关参数

- `graph_update_interval`
- `map_cloud_update_interval`

# 五、闭环检测相关参数

## radar_graph_slam.launch

##### 1.  `enable_loop_closure` 

开关

- true
- false

##### 2. min_loop_interval_dist

==新回环边和上一个回环边之间的最小距离==

##### 3. sc_dist_thresh

扫描上下文的距离阈值

- 原始为 0.5	

##### 4. sc_azimuth_range

扫描上下文的角度阈值



## Scancontext.h 

- `SC_DIST_THRES`
  - 扫描上下文的距离阈值
  - 当前扫描和历史记录中的扫描上下文之间的距离。
    - 如果小于设定的距离阈值，则认为形成闭环。
- ==PC_AZIMUTH_ANGLE==
  - 扫描上下文的 azimuth 角度阈值
  - 当前扫描和历史记录中扫描上下文之间的 azimuth 角度差异，也控制方向分辨率
  - 例如，如果azimuth角度阈值设置为10度，那么整个360度的圆周会被划分为36个方向扇区。
    - 在构建扫描上下文特征时，点云中的点会被分配到这些扇区中，然后统计每个扇区内的点数，形成一个描述环境特征的直方图。
