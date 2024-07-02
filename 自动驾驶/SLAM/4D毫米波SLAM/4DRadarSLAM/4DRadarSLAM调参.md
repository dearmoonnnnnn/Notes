

# 一、关键帧相关参数

`scan_matching_odometry_nodelet.cpp`节点`initialize_params()`函数中的相关参数 









# 二、点云融合阈值调整

距离参数

- 1.0
  - 大部分帧激光雷达点数量只有十几、几十
  - 少数帧达到1000多点
- 5.0
  - 

# 三、距离过滤

- 距离范围

- `z` 坐标范围

# 四、离群点去除

- 方法的选择
  - 因为点云较为稀疏，使用 `RADIUS`
- 判断点是否为离群点的阈值

# 五、去畸变

- 扫描周期的调整

# 六、定时器相关参数

- `graph_update_interval`
- `map_cloud_update_interval`
