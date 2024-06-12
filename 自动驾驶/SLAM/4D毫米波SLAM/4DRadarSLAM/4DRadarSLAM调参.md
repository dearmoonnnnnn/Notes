# SLAM参数调整

### 1、关键帧相关参数

`scan_matching_odometry_nodelet.cpp`节点`initialize_params()`函数中的相关参数 

### 2、距离过滤、立群点去除相关参数



# 点云融合阈值调整

距离参数

- 1.0
  - 大部分帧激光雷达点数量只有十几、几十
  - 少数帧达到1000多点
- 5.0
  - 
