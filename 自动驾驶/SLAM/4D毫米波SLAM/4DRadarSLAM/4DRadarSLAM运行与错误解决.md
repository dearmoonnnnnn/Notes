# 1、日晴不颠簸低速3 

```bash
roslaunch radar_graph_slam radar_graph_slam.launch
```

### 1、第一次运行成功：

#### 问题1：轨迹十分混乱![第一次运行](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/第一次运行.png)

##### 可能原因：

1. 点云数据过于稀疏

2. 没有使用其他数据

   在`rosbag_play_radar_carpark1.launch`文件中可以发现，使用了4个话题运行slam

   ```xml
   <node pkg="rosbag" type="play" name="player"
       args = "-s 0.5 --clock --rate=3 --duration=10000
       $(arg path)$(arg file_0)
       --topic /radar_enhanced_pcl /rgb_cam/image_raw/compressed /barometer/filtered /vectornav/imu
       ">
   </node>
   ```

   - `/radar_enhanced_pcl`
   - `/rgb_cam/image_raw/image_raw/compressed`
   - `/baraometer/filtered`
   - `/vectornav/imu`

#### 问题2：尝试创建 KD 树时输入的点云为空

![image-20240311113228252](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240311113228252.png)

```bash
[pcl::KdTreeFLANN::setInputCloud] Cannot create a KDTree with an empty input cloud!
```

尝试创建 KD 树时输入的点云为空，导致无法创建 KD 树

##### 原因：

在预处理节点中，从原始点云得到处理后的点云过程中，使得所有点都被滤除。

##### 错误位置：

fast_adpgicp_mp_impl.hpp 226行 307行 

- `registrations.cpp`使用了`fast_adpgicp`

- **`scan_matching_odometry_nodelet.cpp`**

  - 使用了`registrations.cpp`

  - `matching`函数：`registration_s2s->setInputSource(filtered)`

    `flitered`由`matching`的参数`cloud`转换而来

  - `pointcloud_callback`函数调用了`matching`函数，并且传递`cloud`参数。 

    - 当`ego_vel_sub`和`points_sub`订阅的话题消息(`/eagle_data/twist`和`/flitered_points`)都到达时，调用回调函数

      ```cpp
      void pointcloud_callback(const geometry_msgs::TwistWithCovarianceStampedConstPtr& twistMsg, const sensor_msgs::PointCloud2ConstPtr& cloud_msg){...}
      ```

    - 调试可知，`pointcloud_callback`调用成功，cloud_msg指针不为空，但是其中点的数量为空。

- **`preprocessing_nodelet.cpp`**

  - 发布者`points_sub`发布`/filtered_points`话题

- 原始点云处理为filtered点云的一系列过程，导致所有点云被滤除。

  - 经过调试可知，在如下代码运行后，点云的数量几乎为零

    ```c++
    filtered = outlier_removal(filtered);
    ```

    由于点云过于稀疏，所有的点都被认为是离群点，被滤除。

##### 解决错误后的运行结果：

注释`outlier_removal`代码后的运行结果：

![image-20240313142522953](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240313142522953.png)

#### 问题3：有效点小于2

```bash
[ INFO] [1711097189.752290720, 1696750139.451054050]: To small valid_targets (< 2) in radar_scan (15)
[ INFO] [1711097193.176887109, 1696750149.712368178]: Warning: radar_data.rows() < config_.N_ransac_points
```

##### 错误原因1：

查看代码可发现，自我速度评估器`radar_ego_volocity_estimator.cpp`文件输出了上述错误。

因此报错原因为自我速度评估器中筛选出的点过少。

##### 解决方法1：修改自我速度评估器的阈值

- 距离阈值
  - `config_.min_dist`
    - 原始值：1
  - `config_.max_dist`
    - 原始值：400
- 强度阈值
  - `config_.min_db`
    - 原始值：10
  - 实际很多点强度小于10
- 角度阈值
  - `config_.azimuth_thresh_deg`
    - 原始值：0.986111
  - `config_.elevation_thresh_deg`
    - 原始值：0.392699

##### 错误原因2：雷达点信号强度转换错误



#### 警告4：四元数归一错误

```bash
[ WARN] [1715583673.975530943, 1695292812.529964256]: MSG to TF: Quaternion Not Properly Normalized
[ INFO] [1715583673.975613916, 1695292812.529964256]: Initial IMU euler angles (RPY): -nan, nan, -nan
[ INFO] [1715583673.975674263, 1695292812.529964256]: Initial Position Matrix = 
-nan, nan, -nan, 0, 
-nan, nan, -nan, 0, 
-nan, -nan, -nan, 0, 
0, 0, 0, 1, 
```

# 2、日雪不颠簸运行 | 融合阈值 5 

## 问题1：Too Large transform

```
Too large transform!!  1.50411[m] 0.114139[degree] Ignore this frame (1705378405.049944000) 
```

##### 可能原因：

在 `scan_matching_odometry_nodelet` 中，相邻关键帧之间的平移和旋转超过了特定阈值。

- `max_diff_trans`
- `max_diff_angle`

##### 尝试方法1：调大相关参数

运行成功，并且成功闭环。但存在问题2

## 问题2：网格太小，轨迹超出了网格范围

![image-20240827163519697](https://raw.githubusercontent.com/dearmoonnnnnn/typoraImage/main/img/image-20240827163519697.png)

## 问题3：闭环检测时，导出的轨迹发生变化，导致文件被覆盖，数据丢失

## 问题4： ROS节点崩溃

在最后进行优化时，ROS 节点崩溃，报错信息如下：

```bash
process has died [pid 149488, exit code -9, cmd /opt/ros/noetic/lib/nodelet/nodelet manager __name:=radarslam_nodelet_manager __log:=/home/dearmoon/.ros/log/41d886c0-65d4-11ef-a2cc-257cb301ef5d/radarslam_nodelet_manager-2.log].
```

### 可能原因：内存不足

在终端中使用 `dmesg` 命令查看  发现有 "Out of memory" 错误信息。

```bash
[438708.999901] Out of memory: Killed process 149488 (nodelet) total-vm:33448568kB, anon-rss:11658504kB, file-rss:0kB, shmem-rss:0kB, UID:1000 pgtables:47916kB oom_score_adj:0
```

### 解决方法：

换电脑