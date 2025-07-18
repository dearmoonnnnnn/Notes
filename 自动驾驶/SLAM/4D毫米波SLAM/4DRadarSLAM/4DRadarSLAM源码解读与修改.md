# 一、代码解析

## A 、问题

##### 1、包的名称为什么是 radar_graph，而不是 4DRadarSLAM

包名在 `package.xml` 文件定义，为 `radar_graph_slam`

##### 2、在预处理节点的 cloud_callback 函数中为什么需要两种类型的点云：radarpoint_raw 和 radarpoint_xyzi

- `radarpoint_raw` 包含了 xyz 和强度信息以及多普勒速度
  1. 所有点最终被添加到 `radarcloud_raw` 中

  2. `radarcloud_raw` 被转换为 ROS 中的 PointCloud2 格式的数据：`pc2_raw_msg`

  3. `pc2_raw_msg` 消息被发布

  4. `pc2_raw_msg` 作为自我速度评估器 `estimator.estimate()` 的输入，返回得到**内点**和**外点**的点云数据：`inlier_radar_msg`，`outlier_radar_msg`

  5. `inlier_radar_msg` 被转换 `radarcloud_inlier`，即从 ROS 消息转换为 pcl 点云类型。

  6. 若启用**动态物体去除**，`src_cloud` (即后续处理的点云)指向`radarcloud_inlier`

  7. 后续对 `src_cloud` 进行一系列处理，得到过滤后的点云 `filtered`

- `radarpoint_xyzi` 包含了xyz 和强度信息
  1. 所有点最终被添加到 `radarcloud_xyzi` 中

  2. 若未启用**动态物体去除**，`src_cloud` 指向 `radarcloud_xyzi`

  3. 后续对 `src_cloud` 进行一系列处理，得到过滤后的点云 `filtered`

##### 3、eagle->msg 的消息格式是什么

见“二、运行自己的数据，问题1”

##### 4、odom_msgs 的数据格式是什么，为什么要使用队列

odom_msgs 包含了机器人的位置、姿态、线速度、角速度等信息。

##### 5、初始位姿矩阵的作用是什么

初始位姿矩阵是描述机器人相对于全局世界坐标系的初始位置和方向的矩阵。

1. 初始化：用于初始化机器人在全局坐标系中的初始位置和方向。
2. 坐标系转换：通过初始位姿矩阵，可以将传感器数据从机器人本体坐标系转换到全局坐标系中，或者反之。
3. 运动模型初始化：可以用于初始化机器人的运动状态。
4. 路径规划和导航：机器人需要知道自己的初始位置，以便规划路径。
5. 系统状态估计：用作状态估计的起点。

##### 6、降采样和去除离群点的作用分别是什么

降采样和去除离群点是点云处理中常用的两种操作，它们的作用分别如下：

1. **降采样（Downsampling）**：
   - **作用**：降采样是为了减少点云数据量，从而提高处理效率、降低计算成本，并在一定程度上保持原始点云的形状特征。通过减少点云中的点数，可以在不丢失重要信息的前提下，提高后续处理步骤的速度和效率。
   - **方法**：降采样常用的方法之一是体素网格降采样（Voxel Grid Downsampling），将点云空间划分为立方体网格，每个网格内只保留一个点的信息。其他降采样方法还包括统计学降采样等。

2. **去除离群点（Outlier Removal）**：
   - **作用**：去除离群点是为了过滤掉可能由于传感器误差、环境干扰等原因引入的异常点，以提高点云的质量和准确性。离群点可能对后续的点云处理和分析步骤产生负面影响，因此去除这些离群点是一个预处理的重要步骤。
   - **方法**：常见的去除离群点的方法包括统计学方法（如统计学离群点移除），半径滤波方法（以每个点为中心，计算周围点的密度，去除密度较低的点），以及基于机器学习的方法等。

这两个操作通常在点云预处理阶段应用，以提高点云的质量和适应性，使得后续的点云处理、配准、建图等任务更加稳定和可靠。

##### 7、header.frame_id 和 child_frame_id 的区别

[ros常用组件_什么是child frame id-CSDN博客](https://blog.csdn.net/weixin_62349967/article/details/126667793)

两者均是ROS消息的头部(`Header`)中的两个重要的标识符

1. `header.frame_id`：表示消息所在的坐标系的名称。例如，激光雷达数据的`frame_id` 可能是 `laser_frame`（以激光雷达为原点的坐标系），相机的 `frame_id` 可能是 `camera_frame` (以相机为原点的坐标系)。
2. `child_frame_id`：表示一个相对于 `frame_id` 的子坐标系。通常它用于表示机器人的移动部件或传感器相对运动坐标系。例如，激光雷达数据的 `child_frame_id` 可能是 `base_laser` ，表示机器人底盘坐标系。
3. `translation` 表示 `child_frame_id` 相对于 `header.frame_id` 的偏移量，而 `rotation` 表示 `child_fram_id` 相对于 `header.frame_id` 的偏航、俯仰、翻滚和w(四元数)

##### 8、内点数据和外点数据的意义和区别

内点：与模型拟合较好的点，用于估计模型

外点：与模型拟合较差的点，用于判断模型的鲁棒性

举例：

`estimator.estimate(pc2_raw_msg, v_r, sigma_v_r, inlier_radar_msg, outlier_radar_msg)`使用 RANSAC 算法对点云数据进行==线性模型估计==，得到点云数据的自我速度以及内点和外点数据。内点和外点数据可以帮助我们更好地理解点云数据，以便于更好地估计模型的准确性和鲁棒性。

##### 9、什么是扫描到地图的配准，它与扫描到扫描的配准有什么区别

扫描到地图的配准是将3D点云数据与已有的地图或模型进行对齐。

- 源点云
  - 传感器采集的新的一帧点云数据
- 目标点云
  - 系统先前构建好的、覆盖了机器人所在环境的大尺度点云地图

扫描到扫描的配准是将多个3D点云数据相互对齐。

- 源点云
  - 传感器采集的新的一帧点云数据
- 目标点云
  - 传感器采集的上一帧（关键帧）点云

##### 10、params.yaml 和 utility_radar.h 都定义了节点的相关参数，比如说 pointCloudTopic 参数，两者不会冲突吗，能不能只保留其中一个？

在 ROS 中，参数可以从多个来源进行设置，包括 `launch` 文件、命令行参数、参数文件（如`params.yaml`）、程序内部设置等。当参数从不同来源设置时，ROS 会按照一定的优先级进行处理，确保参数值正确地被设置。

通常情况下，参数文件（如 `params.yaml` ）中定义的参数会被ROS参数服务器加载，而在程序内部定义的参数（如 `utility_radar.h` 中的 `ParamServer` 类）是由节点内部直接设置的。

如果在 `params.yaml` 文件和 `utility_radar.h` 中都定义了相同的参数，ROS 会按照一定的优先级来确定最终使用的参数值。一般来说，命令行参数的优先级最高，其次是 `launch` 文件中设置的参数，然后是参数文件中定义的参数，最后是程序内部设置的参数。

如果你希望只保留其中一个参数定义，可以根据你的需求选择保留哪个。如果你更喜欢使用参数文件（ `params.yaml` ）来管理参数，你可以删除 utility_radar.h中的参数定义，并将所有参数都集中在 `params.yaml` 文件中进行管理。反之，如果你更喜欢在程序内部定义参数，你可以删除`params.yaml`文件，并在`utility_radar.h`中设置所有参数。

总之，确保最终使用的参数值是正确的，且来源清晰明确即可。

##### 11、gps数据为什么有三种话题？

##### 12、IMU的融合方法是什么

使用了简单的加权平均策略来整合IMU和激光雷达里程计的Roll和Pitch角度信息。

## B、概念

##### 1、tf 变化

在 ROS 中，通过使用 `tf(transform)`，我们可以知道任何时刻、任何两个坐标系之间的相对位置和方向。具体用途：

1. 提供坐标转换服务：

   在任意两个坐标系之间查询一个点或者向量的变换。

2. 时间旅行：

   tf数据在时间上是连续的，因此可以查询过去或未来某个时刻两坐标系之间的变换。(这取决于保存了多少历史数据和==预测了多少未来数据==)

3. 支持多坐标系

   一个机器人可能拥有多个传感器，每个传感器都有自己的坐标系。使用`tf`，所有这些坐标系可以转换为一个共同的坐标系，如 `base_link` 或 `odom`

4. 方便的可视化

   RViz 利用 `tf` 数据来正确地显示来自不同传感器的数据。这样无论数据来自哪个传感器或在哪个坐标系中，都可以在同一个视图中共同显示。

5. 自动管理坐标系关系

   当机器人的一个部分相对于另一个部分移动时(例如，一个机器人的手臂相对于它的身体)，tf会自动跟踪这些变化，并为应用程序提供实时的、准确的坐标转换。

##### 2、里程计信息

包含机器人运动的测量，包括平移和旋转信息。

- **平移信息(Translation)**：机器人相对于起始点在x、y和z轴上的平移量。
- **旋转信息(Rotation)**：机器人相对于起始点的旋转角度。可以用欧拉角或四元数来表示。

##### 3、点云消息的帧ID

指点云数据所在坐标系的标识符。

帧ID指定了点云数据相对于某个参考系的位置和方向。例如，如果点云数据表示激光雷达扫描的结果，帧ID可能是与激光雷达坐标系相关的标识符。

具体例子：

- map：标准的、全局的坐标系
- base_link：与机器人相关的坐标系
  - 原点：通常选择在机器人的底盘中心或某个显著的参考点
  - 坐标轴：通常选择机器人的前进方向为x轴，左侧为y轴，垂直于底盘的方向为z轴。
  - 方向：遵循右手定则。

ROS中，机器人相关的坐标系通常由TF(Transform)库进行管理。便与机器人系统的各个部件(例如激光雷达、相机、底盘等)之间的坐标变换。

##### 4、nav_msgs

ROS提供的标准信息库

包含了一系列与**导航**和**路径规划**相关的消息类型，用于在ROS系统中传递与机器人导航和路径规划相关的信息。

常见的消息类型如下：

- `Odometry`：包含机器人的里程计信息，如位置、方向和速度。
- `Path`：表示机器人的轨迹或路径，通常由一系列的位姿(位置和方向)组成
- `MapMetaData`：包含地图的元数据信息，如地图的分辨率、大小、原点等
- `GetMap`和`GetPlan`：用于请求地图和路径规划服务的请求消息

##### 5、geometry_msgs

ROS提供的标准信息库

包含了一系列与**几何**和**运动学**相关的消息类型，用于在ROS系统中传递与机器人姿态、位置、变换等信息。

常见的消息类型如下：

- `Point`：表示三位空间中的一个点，具有x、y、z坐标
- `Quaternion`：表示四元数，通常用于表示旋转姿态
- `Pose`和`PoseStamped`：分别表示三位空间中的姿态(位置和旋转)和带时间戳的姿态
- `Twist`和`TwistStamped`：表示线速度和角速度，以及带时间戳的线速度和角速度
- `Transform` 和 `TransformStamped`：代表坐标变换和带时间戳的坐标变换

##### 6、初始位姿矩阵

用于初始化机器人在**全局坐标系**中的初始位置和方向。

##### 7、barometer

气压计，通常用于测量高度、气象预测、气象研究等领域。

气压计测量大气压力的变化，根据气压的变化可以推断海拔高度的变化。

##### 8、初始位姿矩阵

初始位姿矩阵是一个4x4的矩阵，用于表示机器人在初始时刻的位置和姿态。它描述了机器人坐标系相对于全局坐标系的变换关系。初始位姿矩阵通常记为 T_init，其结构如下：

```
T_init = [R_init | t_init]
         [  0    |   1   ]
```

其中：
- R_init 是一个3x3的旋转矩阵，表示机器人坐标系相对于全局坐标系的旋转关系。
- t_init 是一个3x1的平移向量，表示机器人坐标系原点相对于全局坐标系原点的位置。

初始位姿矩阵的作用包括：

1. 建立全局坐标系和机器人坐标系之间的关系：初始位姿矩阵定义了机器人坐标系相对于全局坐标系的位置和姿态，使得我们可以在两个坐标系之间进行转换。

2. 提供机器人初始状态的参考：在机器人开始运动或执行任务时，初始位姿矩阵给出了机器人的起始位置和姿态。这对于后续的定位、导航和控制任务非常重要。

3. 实现坐标转换：通过初始位姿矩阵，我们可以将机器人本地坐标系下的点、向量或轨迹转换到全局坐标系下，或者将全局坐标系下的目标位置转换到机器人坐标系下。

4. 融合多传感器数据：当机器人配备了多个传感器（如IMU、里程计、GPS等）时，初始位姿矩阵可以用于将不同传感器的数据统一到同一个坐标系下，以便进行数据融合和状态估计。

5. 误差校正和重定位：在某些情况下，机器人的位姿可能出现漂移或误差累积。通过重新估计初始位姿矩阵，可以校正这些误差，并将机器人的位姿重新与全局坐标系对齐。

在实际应用中，初始位姿矩阵通常通过标定或者利用传感器数据（如IMU、GPS等）来估计。一旦估计出初始位姿矩阵，它就可以作为机器人定位、导航和控制算法的基础，用于跟踪机器人的运动和执行任务。

需要注意的是，初始位姿矩阵只表示机器人在初始时刻的位姿，随着机器人的运动，其位姿会不断变化。为了持续跟踪机器人的位姿，通常需要结合其他传感器数据和运动模型，通过位姿估计算法（如卡尔曼滤波、粒子滤波等）来更新机器人的位姿。

## C、节点文件

![img](https://github.com/zhuge2333/4DRadarSLAM/raw/main/doc/fig_flowchart_system.png)

- *preprocessing_nodelet*
  - 下采样
  - 将雷达点云传送到Livox雷达帧
  - 评估自我速度并移除动态物体
- *scan_matching_odometry_nodelet*
  - 通过对相邻帧迭代的使用扫描匹配，评估传感器位姿，即里程计信息
- *radar_graph_slam_nodelet*
  - 使用闭环检测消除累积误差并且优化位姿图

### 零、数据流动的方向

#### 1、点云数据流动方向

##### 1.1、preprocessing_nodelet

被 `points_sub` 订阅，进入 `cloud_callback` 函数，转为 `radarcloud_raw` 、 `radarcloud_xyzi`

- `radarcloud_raw`
  - 包含位置信息、强度和多普勒速度
  - 转为 `sensor_msgs::PointCloud2` 格式的 `pc2_raw_msg`
  - `pc2_raw_msg` 传入自我速度评估器 `estimator`，得到线速度 `twist`（即`v_r`）、内点信息 `inlier_radar_msg` 、外点信息 `outlier_radar_msg` 。然后发布这三类信息。
- `radarcloud_xyzi`
  - 包含位置信息和强度
- `src_cloud`
  - 若启用动态物体去除，指向 `radarcloud_inlier` ；否则指向 `radar_xyzi`
  - 经过一系列的处理后(转为 `filtered`)，由 `poins_pub` 发布


##### 1.2 scan_matching_odometry_nodelet

`twist` 消息被 `ego_vel_sub` 订阅；`filtered` 点云消息被 `points_sub` 订阅。当两者消息都到达时，被传入`pointcloud_callback`。

- `twist`
  - 被用来计算当前帧到上一帧之间的累积线速度：`egovel_cum_x`、`egovel_cum_y`、`egovel_cum_z`。
- `filtered` 点云消息
  - 转为 pcl 点云格式
  - 被传入 `matching` 函数，执行点云配准，得到传感器位姿 `pose`
- `pose` 和 `twist` 一起被 `publish_odometry` 发表。同时利用 `read_until` 机制来发布处理完的数据。

##### 1.3 radar_graph_slam_nodelet

`odom` 消息被 `odom_sub` 订阅，`filtered_points` 话题被 `cloud_sub` 订阅。当两者都到达时，调用 `cloud_callback`

###### 提问：这里的 `filtered_points` 话题是 `preprocessing_nodelet` 发布还是 `scan_matching_nodelet` 发布?

TO DO

#### 2、imu 数据流动方向

##### 2.1 preprocessing_nodelet

被 `imu_sub` 订阅，传入 IMU 回调函数 `imu_callback`。

- 将原始的IMU数据进行坐标轴的旋转，使其和 livox 坐标系对齐。被 `imu_pub`以 `/imu` 话题发布。

##### 2.2 scan_matching_odometry_nodelet

`/imu`话题被`imu_sub`订阅，传入`imu_callback`

- 参数`enable_imu_fusion`为 false，源代码未启用imu融合
- imu的四元数被提取出来，存储在 `imu_quat_from` 中
- 使用预定义的`extQRPY`对 IMU 的方向进行去扰动，得到 `imu_quat_deskew`。这是为了补偿 IMU 安装时的方向偏差，使 IMU 方向和激光雷达方向对齐。
- 根据去扰动后的 imu 数据，创建新的 IMU 数据，添加到 `imu_queue` 队列中

若 `enable_imu_fusion` 为 `true`，发生的事情：

- `matching()` 函数中

  ```cpp
   if(keyframe_updater->decide(Eigen::Isometry3d(odom_s2s_now), stamp)) {
        // Loose Coupling the IMU roll & pitch
        if (enable_imu_fusion){
          if(enable_scan_to_map) transformUpdate(odom_s2m_now);
          else transformUpdate(odom_s2s_now);
        }
   ......
   }
  ```

  - 调用 `transformUpdate` 函数	
    - 若启用点云到地图的配准，传入参数 `odom_s2m_now`
    - 若未启用点云到地图的配准，传入参数 `odom_s2s_now`

- `transformUpdate` 函数

##### 2.3 radar_graph_slam_nodelet

`imu_sub` 订阅 `preprocessing_nodelet` 传入的 `/imu` 话题。

- 将 `imu` 消息转换到雷达坐标系，得到 `imu_quat_deskew`
- 得到初始位姿矩阵

#### 3、GPS数据流动方向

##### radar_graph_slam_nodelet

如果启用了 gps(在launch文件中设置是否启用)

- 订阅 `/gps/geopoint`、`/gpsimu_driver/nmea_sentence`、`gpsTopic`(即`/ublox/fix`)。
- 若接收到 `/gps/geopoint` 话题，调用 `gps_callback()`
  - 将 gps 数据进行时间校正，然后添加到队列 `gps_geopoint_queue` 中。
  - 后续未对 `gps_geopoint_queue` 进行处理
- 若接收到 `/gpsimu_driver/nmea_sentence` 话题，调用`nmea_callback()`
  - 从 nmea_msg 中解析头部、经纬度信息，将解析后的结果传递到`gps_callback()` 函数中
- 若接收到 `gpsTopic` 话题，即 `/gps/geopoint` 话题，调用`navsat_callback()`
  - 将 `GPS` 数据存储到 `gps_navsat_queue` 队列中
  - `flush_gps_queue()` 对该队列进行处理

#### 4、自我速度 twist 数据流向

##### processing_nodelet.cpp

- 通过雷达点云的多普勒速度评估自我速度，得到 `v_r`，并将 `v_r` 传给 `twist` 后发布。

##### scan_matching_odometry_nodelet.cpp

- 接收到时间同步的自我速度twist消息和点云消息后，调用 `pointcloud()` 函数。
- `pointcloud()` 函数中，计算当前帧和上一帧之间的位移 `egovel_cum`，并调用 `matching()` 函数。
- `matching()` 函数中，
  - 若启用自我速度：初始猜测变换矩阵 = 上一次扫描到扫描的变换 * `egovel_cum` * 扫描匹配的相对位姿变换

#### 5、conmand 数据流向



### 一、apps/preprocessing_nodelet.cpp

#### 三个订阅者：

##### 1、imu_sub

- 话题：`imuTopic`，即 `/vectornav/imu`
- 消息类型：
- 回调函数：`&PreprocessingNodelet::imu_callback`
  - 从输入的 `imu_msg` 获取信息并调整，得到 `imu_data` 并发布
  - 同时，判断 `odom` 消息是否需要更新，若需要，更新后重新发布

##### 2、points_sub：

- 话题：`pointCloudTopic`，从 `config/params.yaml` 中可知，`pointCloudTopic` 即 `/radar_enhanced_pcl`
- 回调函数：`&PreprocessingNodelet::cloud_callback`
  - 输入： `sensor::PointCloud::ConstPtr& eagle_msg`
  - `radarpoint_raw`
  - `radarpoint_xyzi`
  - `radarcloud_raw`
  - `radarcloud_xyzi`
  - 两个 opencv 对象，用于存储原始点和转换后的点：`ptMat`，`dstMat`
  - `dstMat`：原始点云与转换矩阵 `Radar_to_livox` 相乘。

##### 3、command_sub

- 话题：`/conmand`
- 回调函数：`&PrecessingNodelet::command_callback`


#### 八个发布者：

##### 1、points_pub

- 话题：`/flitered_points`

- 消息类型：`sensor_msgs::PointCloud2`

##### 2、colored_pub

- 话题：`/colored_points`
- 消息类型：`sensor_msgs::PointCloud2`

##### 3、imu_pub

- 话题：`/imu`
- 消息类型：`sensor_msgs::Imu`

##### 4、gt_pub

- 话题：`/aftmapped_to_init`
- 消息类型：`nav_msgs::Odometry`
- 描述：`Aft-mapped`到初始位姿的里程计数据

##### 5、pub_twist

- 话题：`topic_twist`,即`/eagle_data/twist`
- 消息类型：`gemetry_msgs::TwistWithConvarianceStamped`
- 描述：Twist通常是指机器人的运动变化，包括线速度和角速度

##### 6、pub_inlier_pc2

- 话题：`topic_inlier_pc2`，即`/eagle_data/inlier_pc2`
- 消息类型：`sensor_msgs::PointCloud2`
- 描述：在点云配准或特征提取中，内点是指与模型或特征匹配的点。内点点云可能是经过某种滤波或配准后，与某个模型或参考帧相关的点云。

##### 7、pub_outlier

- 话题：`topic_outlier_pc2`, 即`/eagle_data/outlier_pc2`
- 消息类型：`sensor_msgs::PointCloud2`
- 描述：与内点相反，外点是指不符合模型或特征的点。外点点云通常包含未能与给定模型或参考帧匹配的点。

##### 8、pc2_raw_pub

- 话题：`/eagle_data/pc2_raw`
- 消息类型：`sensor_msgs::PointCloud2`
- 描述：这是从传感器（如激光雷达或深度相机）获取的未经处理的点云数据。原始点云包含传感器采集到的所有点，可能包含噪声、离群点等。

#### 成员函数

##### 1、onInit()

-  描述
   -  初始化参数，设置订阅者、发布者


##### 2、initializeTransformation()

- 描述
  - 初始化一些变换矩阵，主要涉及不同坐标系之间的转换
- 参数
  - 无

- 相关变量：
  - `livox_to_RGB`：Livox雷达坐标系到RGB坐标系的变换矩阵。
    - 后续代码中未使用
  - `RGB_to_livox`：RGB坐标系到Livox雷达坐标系的逆变换矩阵，即 `livox_to_RGB` 的逆矩阵。
    - 后续代码中未使用
  - `Thermal_to_RGB`：红外热像仪坐标系到RGB坐标系的变换矩阵。
    - 后续代码中未使用
  - `Radar_to_Thermal`：雷达坐标系到红外热像仪坐标系的变换矩阵。
    - 后续代码中未使用
  - `Change_Radarframe`：雷达坐标系到`livox`雷达坐标系的变换矩阵，通过交换坐标轴实现。
    - 后续代码中未使用
  - `Radar_to_livox`：将雷达坐标系转换到`livox`坐标系的组合变换矩阵，通过矩阵相乘得到。
    - 将毫米波雷达坐标系的点`ptMat`转换为`livox`坐标系的点`dstMat`
    - `radarcloud_raw`和`radarcloud_xyzi`中的点都是`livox`坐标系
- 返回值
  - 无

##### 3、initializeParams()

- 描述
  - 从ROS参数服务器获取并初始化一些参数，这些参数主要涉及点云处理中的降采样、离群点去除等操作的参数设置。
  - 从`ground truth`文件中读取每一行，作为`odom_msgs`队列的元素，每个odom消息包含位置和方向数据
- 参数
  - 无

- 相关变量：
  - `voxelgrid`：
    - 用于进行体素网格降采样
  - `outlier_removal_filter`：
    - 离群点移除对象的指针
  - `odom_msgs`：
    - `std::deque<nav_msgs::Odometry>`
    - ``Odometry`消息的队列，这里存储的是`ground truth`文件中的`odom`消息
- 返回值：
  - 无


##### 4、imu_callback()

- 描述：
  - **IMU 数据处理**：
    - 从 IMU（惯性测量单元）传感器获取的数据进行处理。
    - 对原始的 IMU 数据进行一些变换，包括将欧拉角表示的姿态信息转换为四元数表示的姿态信息。
  - **发布处理后的 IMU 数据**：
    - 将处理后的 IMU 数据以 `sensor_msgs::Imu` 消息的形式发布，以供后续处理或显示使用。
  - **更新队列**：
    - 将 IMU 数据按照时间戳顺序存储到队列中。
    - 通过对比时间戳，定期从队列中移除已经过时的 IMU 数据。
  - **更新 TF（Transform）**：
    - 如果启用了 TF 的发布（`publish_tf` 为真），则将当前的姿态信息发布为 TF 变换。
  - **发布 Ground Truth（地面真值）数据**：
    - 将 Ground Truth 数据（在这里是里程计信息）以 `nav_msgs::Odometry` 消息的形式发布。

- 参数：
  - `imu_msg`：
    - 类型：`sensor_msgs::ImuConstPtr&`
    - 从IMU获取的数据
- 返回值：无
  - 将处理后的IMU数据以`sensor_msgs::Imu`消息的形式发布
  - 并且发布和IMU消息时间同步的`Ground Truth`信息

##### 5、cloud_callback()

- 描述：
  - 对从传感器获得的原始点云进行处理，根据一定条件(信号强度、位置等)筛选有效的点，构建新的点云数据。
- 参数：
  - `eagle_msg`，从传感器接收到的雷达消息
- 返回值：无
  - 发布经过处理后的点云`*filtered`
- 相关变量：
  - `radarpoint_raw`
    - 原始点，包含x、y、z坐标，强度、多普勒速度
  - `radarcloud_raw`
    - 原始点云
  - `radarpoint_xyzi`
    - 原始点，包含x、y、z坐标，强度
  - `radarcloud_xyzi`
    - 原始点云
  - `src_cloud`
    - 点云指针，若启用了动态物体去除，指向雷达点云的内点，否则指向原始的雷达点云(`radarcloud_xyzi`)，最后会更新坐标系，称为baselinkFrame中的点云
  - `transform`
    - 从src_cloud坐标系到baselinkFrame的变换
  - `filtered`
    - 变量类型：`pcl::PointCloud<PointT>::ConstPtr`
    - 表示`src_cloud`进行距离过滤、下采样、离群点去除后的点云
  - `num_at_dist`：当前帧的距离直方图

##### 6、passthrough()

- 描述
  - 点云的区域截取，输入点云中 z 坐标在 -2 和 10 之间的点截取出来，返回一个新的点云
- 参数
  - `cloud`
    - 类型：`const pcl::PointCloud<PointT>::ConstPtr&`
    - 表示点云
- 返回值
  - `filtered`
    - 类型：`pcl::PointCloud<PointT>::ConstPtr`
    - 截取后的点云

##### 7、downsample()

- 描述
  - 下采样函数，用于减少点云密度
  - 方法，在`initializeParams()`函数中确定
    - `VOXELGRID`
    - `APPROX_VOXELGRID`
- 参数
  - `cloud`
    - 类型：`const pcl::PointCloud<PointT>::ConstPtr&`
    - 表示点云
- 返回值
  - `filtered`
    - 类型：`pcl::PointCloud<PointT>::ConstPtr`
    - 下采样后的点云

##### 8、outlier_removal()

- 描述
  - 去除点云中的离群点
- 参数
  - `cloud`
    - 类型：`const pcl::PointCloud<PointT>::ConstPtr&`
    - 表示点云
- 返回值
  - `filtered`
    - 类型：`pcl::PointCloud<PointT>::ConstPtr`
    - 去除离群点后的点云

##### 9、distance_filter()

- 描述
  - 距离过滤，只保留与原点的距离在一定范围内的点
- 参数
  - `cloud`
    - 类型：`const pcl::PointCloud<PointT>::ConstPtr&`
    - 表示点云
- 返回值
  - `filtered`
    - 类型：`pcl::PointCloud<PointT>::ConstPtr`
    - 距离过滤后的点云

##### 10、deskewing()

- 描述
  - 去除点云中的扭曲，以便更准确地估计运动或提取特征。
- 参数
  - `cloud`
    - 类型：`const pcl::PointCloud<PointT>::ConstPtr&`
    - 表示点云
- 返回值
  - `filtered`
    - 类型：`pcl::PointCloud<PointT>::ConstPtr`
    - 代表去畸变后的点云

##### 11、RadarRaw2PointCloudXYZ()

- 描述
  - 将`raw`点云转为`cloudxyzi`点云

##### 12、RadarRaw2PointCloudXYZI

- 描述
  - 将`raw`点云转为`cloudxyz`点云

##### 13、command_callback

- 描述
  - 回调函数，它处理来自 `std_msgs::String` 类型的命令消息。
  - 收到`time`命令，返回时间的中值
  - 收到`point_distribution`命令，返回平均点分布数据
- 参数
  - `str_msg`
    - 变量类型：`const std_msgs::String&`
- 返回值：无
  - 输出时间容器的中值，或者平均点分布数据

### 二、apps/scan_matching_odometry_nodelet.cpp

#### 订阅者：

##### 1、ego_vel_sub

- 话题：`/eagle_data/twist`
- 消息类型：`geometry_msgs::TwistWithCovarianceStamped`
- 回调函数：`&ScanMatchingOdometryNodelet::pointcloud_callback`

##### 2、points_sub

- 话题：`/filtered_points`
- 消息类型：`sensor_msgs::PointCloud2`
- 回调函数：同上

<!--上述消息同步处理-->

##### 3、imu_sub

- 话题：`/imu`
- 回调函数：`&ScanMatchingOdometryNodelet::command_callback`

##### 4、command_sub

- 话题：`/command`
- 回调函数：`&ScanMatchingOdometryNodelet::command_callback`

##### 5、消息同步器Synchronizer

```cpp
sync.reset(new message_filters::Synchronizer<ApproxSyncPolicy>(ApproxSyncPolicy(32), *ego_vel_sub, *points_sub));
    sync->registerCallback(boost::bind(&ScanMatchingOdometryNodelet::pointcloud_callback, this, _1, _2));
```

当`ego_vel_sub`和`points_sub`订阅的话题消息都到达时，调用回调函数`pointcloud_callback`

#### 发布者：

##### 1、read_until_pub

- 话题：/scan_matching_odometry/read_until
- 消息类型：std_msgs::Header

##### 2、odom_pub

- 雷达扫描匹配的里程计
- 话题：odomTopic，即/odom
- 消息类型：nav_msgs::Odometry

##### 3、trans_pub

- <!--雷达扫描匹配的转换？？？-->
- 话题：/scan_matching_odometry/transform
- 消息类型：geometry_msgs::TransformStamped

##### 4、status_pub

- 话题：/scan_matching_odometry/status
- 消息类型：ScanMatchingStatus

##### 5、aligned_points_pub

- 话题：/aligned_points
- 消息类型：sensor_msgs::PointCloud2

##### 6、submap_pub

- 话题：/radar_graph_slam/submap
- 消息类型：sensor_msgs::PointCloud2

#### 成员函数：

##### 0、onInit()

- 描述
  - 初始化句柄、订阅者、发布者

##### 1、initialize_params()

- 描述
  - 初始化参数
    - 关键帧

##### 2、imu_callback()

- 描述
  - 将IMU消息中的四元数提取出来，存储在`imu_quat_from`中。
  - 使用预定义的`extQRPY`对IMU的方向进行去扰动，得到`imu_quat_deskew`。这是为了补偿IMU安装时的方向偏差，使IMU的方向与机器人的方向对齐。
  - 对去扰动后的四元数进行归一化，确保四元数的模长为1。
  - 将去扰动后的四元数转换为TF库中的四元数类型，并使用TF库计算对应的RPY（Roll-Pitch-Yaw）角。这是为了方便后续使用RPY角进行姿态表示和计算。
  - 更新IMU队列的指针，将当前IMU消息的时间戳、滚转角和俯仰角存储在相应的数组中。这是为了保存一段时间内的IMU数据，以便后续进行数据融合或其他处理。
  - 创建一个新的IMU消息，将去扰动后的四元数作为消息的方向，并将其他IMU数据（如角速度、线性加速度等）复制到新的消息中。
  - 使用互斥锁将新的IMU消息推送到IMU队列中，确保线程安全。这是为了在多线程环境下安全地访问和修改IMU队列。
  - 在第一次处理IMU消息时，使用TF库根据当前的RPY角创建一个四元数，并将其转换为旋转矩阵，存储在`global_orient_matrix`中。这是为了建立一个全局的初始方向，作为后续姿态估计的参考。
- 参数
  - `imu_msg`
    - 变量类型：`const sensor_msgs::ImuConstPtr&`
    - 表示imu消息
- 返回值：无
- 相关参数
  - `imu_queue`

##### 3、flush_imu_queue()

- 描述：
  - 用于将IMU数据与关键帧（keyframes）关联，将时间戳相差不大的IMU数据和关键帧关联起来。
- 参数：无
- 返回值：
  - 布尔值
    - `true`：有新的IMU数据与关键帧关联
    - `false`：没有新的IMU数据与关键帧关联


##### 4、get_closest_imu()

- 描述：
  - 用于获取与给定时间戳最接近的IMU数据
- 参数：
  - `frame_stamp`：
    - 时间戳
    - 变量类型：`ros::Time`
- 返回值
  - 变量类型：`std::pair<bool, sensor_msgs::Imu>`
    - `false_result`：未成功获取最近的IMU数据，`<false, NULL>`
    - `result`：成功获取

##### 5、transformUpdate()

- 描述
  - 将IMU（惯性测量单元）的姿态信息融合到激光雷达里程计（Odometry）的变换矩阵中，以实现更准确的姿态估计。
- 参数
  - `odom_to_update`
    - 变量类型：`Eigen::Matrix4d&`
    - ==代表激光雷达扫描周期内的里程计（Odometry）变换矩阵==
    - 两个可能的实参：
      - `matching()`函数中的`odom_s2m_now`
      - `matching()`函数中的`odom_s2s_now`
- 返回值：无
- 相关变量
  - `odom_to_update.block`：
    - 激光雷达里程计的变换矩阵
    - 描述了激光雷达在一个另一个坐标系中的位置和方向
  - `imu_fusion_ratio`:
    - 融合系数
    - 对IMU和激光雷达里程计的Pitch和Roll角度进行加权融合
- question：
  - 为什么IMU的姿态信息可以表示激光雷达里程计的变换矩阵

##### 6、pointcloud_callback()

- 描述
  - 处理传入的点云数据和自我速度徐必须
  - 调用`matching()`函数，进行点云配准
- 参数
  - `twistMsg`
    - 变量类型：`const geometry_msgs::TwistWithCovarianceStampedConstPtr&`
    - 表示自我速度，包含角速度，线速度等
  - `cloud_msg`
    - 变量类型：`const sensor_msgs::PointCloud2ConstPtr&`
    - 表示点云信息
- 返回值：无

##### 7、msf_pose_callback()

- 描述
  - 回调函数，处理接收到的带协方差的姿态信息消息
- 参数
  - `pose_msg`
    - 变量类型：`const geometry_msgs::PoseWithCovarianceStampedConstPtr&`
    - 表示带协方差的姿态信息
  - `after_update`
    - 变量类型：`bool`
    - 表示姿态是否更新过

##### 8、downsample()

- 描述：
  - 下采样一个点云
- 参数
  - `cloud`
    - 变量类型：`const pcl::PointCloud<PointT>::ConstPtr&`
- 返回值
  - `filtered`
    - 变量类型：`pcl::PointCloud<Point>::ConstPtr`

##### 9、matching()

- 描述
  - 在`pointcloud_callback`中被调用一次
  - 估计输入点云和关键帧点云之间的位姿
- 参数
  - `stamp`
    - 变量类型：`const ros::Time&`
  - `cloud`
    - 变量类型：`const pcl::PointCloud<PointT>::ConstPtr&`
- 返回值
  - 返回类型为 `Eigen::Matrix4d`，表示输入点云与关键帧点云之间的相对姿态变换矩阵。
- 相关变量：
  - 扫描配准部分
    - `keyframe_cloud_s2s`：
      - 扫描到扫描的配准中的目标点云
      - 即上一个关键帧对象
    - `keyframe_pose_s2s`
      - 初始化时设置为单位矩阵
      - 关键帧更新后：`keyframe_pose_s2s`更新为`odom_s2s_now`
    - `keyframe_cloud_s2m`：
      - 扫描到地图的配准中的目标点云
      - 即先前构建好的、覆盖了机器人所在环境的大尺度点云地图
    - `guess`：
      - 初始猜测变换矩阵
      - 初始猜测的好坏直接影响了扫描匹配算法的收敛速度和结果的准确性。一个良好的初始猜测可以使算法更容易找到全局最优解，而不容易陷入局部最优解。
    - `trans_s2s`：
      - 最终的扫描到扫描的变换矩阵
    - `odom_s2s_now`： 
      - 等于上一帧关键帧的位姿 * `tran_s2s`
      - 当前时刻的扫描到扫描的位姿变换矩阵，即当前帧相对于上一个关键帧的变换矩阵
    - `odom_s2m_now`：
      - 当前时刻的地图到扫描的位姿变换矩阵，即当前帧相对于起始点的变换矩阵
  - 异常判断
    - 

##### 10、publish_odometry()

- 描述

  发布里程计信息

- 输入参数

  - `stamp`
    - 变量类型：`const ros::Time&`
    - 代表时间戳
  - `father_frame_id`
    - 变量类型：`const std::string&`
    - 父帧ID
  - `child_frame_id`
    - 变量类型：`const std::string&`
    - 子帧ID
  - `pose_in`
    - 变量类型：`const Eigen::Matrix4d&`
    - 位姿矩阵
  - `twist_in`
    - 变量类型：`const geometry_msgs::TwistWithCovariance`
    - 

##### 11、publish_scan_matching_status()

##### 12、conmand_callback()

- 描述
  - 若接收到`time`命令，输出扫描到扫描配准的耗费时间

### 三、apps/radar_graph_slam_nodelet.cpp

定义了一个类:RadarGraphSlamNodelet

#### 订阅者

##### 1、odom_sub

- 话题：odomTopic, 即`/odom`
- 消息类型：`nav_msgs::Odometry`

##### 2、cloud_sub

- 话题：`/flitered_points`
- 消息类型：`sensor_msgs::PointCloud2`


#### 发布者

##### 1、markers_pub

- 话题：`/radar_graph_slam/markers`
- 消息类型：`visualization_msgs::MarkerArray`

##### 2、odom2base_pub

- ==将雷达里程计转换为基线==
- 话题：`/radar_graph_slam/odom2base`
- 消息类型：`geometry_msgs::TransformStamped`

##### 3、aftmapped_odom_pub

- 话题：`/radar_graph_slam/aftmapped_odoml`
- 消息类型：`nav::Odometry`

##### 4、aftmapped_odom_incremenral_pub

- 话题：`/radar_graph_slam/aftmapped_odoml_incremenral`
- 消息类型：`nav::Odometry`

##### 5、map_points_pub

- 话题：`/radar_graph_slam/map_points`
- 消息类型：`sensor_msgs::PointCloud2`

##### 6、read_uintil_pub

- 话题：`/radar_graph_slam/read_until`
- 消息类型：`std_msgs::Header`

##### 7、odom_frame2frame_pub

- 话题：`/radar_graph_slam/odom_frame2frame`
- 消息类型：`nav_msgs::Odometry`



#### 成员函数：

##### 1、onInit()

- 描述：
  - 初始化参数，设置订阅者、发布者


##### 2、cloud_callback()

- 描述：
  - 将接受到的点云扔到关键帧队列中

- 参数：
  - `odom_msg`：里程计信息，即当前帧到基座之间的旋转、平移信息
  - `cloud_msg`：点云信息，即当前帧各个点的x、y、z坐标和强度等
- 相关变量：
  - 构造的关键帧：`KeyFrame::Ptr keyframe(new KeyFrame(keyframe_index, stamp, odom_now, accum_d, cloud));`
    - 包含关键帧索引、时间戳、当前的里程计信息、累计的距离、点云

##### 3、imu_callback()

- 描述：

  - 根据IMU数据的四元数部分计算初始的机器人初始位姿矩阵`initial_pose`

- 参数：

  - `imu_odom_msg`:接受到的imu数据消息，包含机器人在某个时间点的惯性测量单元数据，如加速度、角速度等信息。

    `sensor_msgs::Imu`常见的成员：

    - `Header`：时间戳`stamp`
    - `linear_acceleration`：包含机器人在三个坐标轴上的线性加速度
    - `angular_velocity`：包含机器人在三个坐标轴上的角速度
    - `orientation`（可能有）：包含机器人的方向信息，通常表示为四元数x、y、z、w

    在这个`imu_callback()`中主要处理`imu_odom_msg->orientation`部分

- 相关变量：

  - `initial_pose`：初始位姿矩阵，有imu中的四元数经过一系列转换得到。

##### 4、imu_odom_callback()

- 描述
  - 接收imu和里程计融合的消息，并将其存储在imu_odom_queue队列中

- 参数
  - `imu_odom_msg`：imu和里程计融合的消息


##### 5、preIntegrationTransform()

- 描述
  - 计算关键帧队列第一个imu-odom消息和最后一个imu-odom消息之间的变换

- 参数：无
- 返回值：
  - `trans_`：
    - `trans_.rotation`：整个队列的旋转
    - `trans_.translation`：整个队列的平移

- 相关变量：
  - `lastImuOdomQt`：队列中最早的imu-odom消息的时间戳
  - `isom_imu_odom_btn`：队列中最后一个消息到第一个消息之间的变换矩阵

##### 6、barometer_callback

- 描述：气压计回调函数，处理来自barometer_bmp388话题的消息。若启用了barometer，将消息加入到barometer_queue队列中，并使用互斥锁保护对队列的访问。若没有，直接返回，不进行处理。
- 参数：
  - `baro_msg`：气压计消息
- 返回值：无

##### 7、==flush_barometer_queue()==

- 描述：将气压计数据和关键帧对齐，从而提供相对于机器人起始点的高度信息。
- 参数：无
- 返回值：无

##### 创建slam需要用到的对象:

- graph_slam, keyframe_updater, loop_detector，map_cloud_generator等等

- 对应类的定义都在src/radar_graph_slam/文件夹下

##### 8、flush_keyframe_queue()

- 描述：
  - 将关键帧添加到位姿图中
- 参数：
  - 无
- 相关变量：
  - `graph_slam`
- 返回值`bool`
  - `true`：至少一个关键帧被添加到位姿图中

##### 9、optimization_timer_callback()

##### 10、addLoopFactor()

##### 11、map_points_publish_timer_callback()

##### 12、create_marker_array()

##### 13、edge_marker()

##### 14、dump_service()

##### 15、save_map_setvice()

##### 16、nmea_callback()

- 描述：从`nmea_msg`中解析出gps_msg，然后调用`gps_callback()`
- 参数
  - `nmea_msg`
    - 类型：`const nmea_msgs::SentenceConstPtr&`
    - ==。。。。==
- 返回值
  - 无

##### 17、navsat_callback()

- 描述：将gps数据存储到`gps_navsat_queue`

##### 18、gps_callback()

- 描述：
  - 将gps数据进行时间校准，然后添加到队列中
- 参数：
  - `gps_msg`
    - 类型：`const sensor_msgs::NavSatFixConstPtr&`
    - 订阅话题接收到的gps数据
- 相关变量：
  - `gps_time_offset`
    - 时间偏移量，用于校正gps时间，自己设值
  - `gps_navsat_queue`
    - gps数据队列
- 返回值
  - 无

##### 19、flush_gps_queue()

- 描述：处理gps数据队列
- 参数：
  - 无
- 相关变量
  - `gps_navsat_queue`
    - 存储GPS数据的队列
  - `keyframes`
    - 关键帧队列
  - `last_gps_edge_index`
    - 

##### 20、command_callback()

- 描述
  - 如果消息是`output_aftmapped`，则打开一个文件，将关键帧的位置和旋转信息写入文件中，并打印
  - 如果消息是`time`，则计算并打印预滤波匹配时间、扫描上下文时间、里程计检查时间和优化时间的中位数。

## D、launch文件

### 1、radar_graph_slam.launch

- 启动三个radar_graph_slam中的三个节点和rviz节点

- 加载数据

  - ```xml
    <include file="$(find radar_graph_slam)/launch/rosbag_play_radar_carpark1.launch" />
    ```

### 2、rosbag_play_radar_carpark1.launch

```xml
<node pkg="rosbag" type="play" name="player"      args="-s 0.5 --clock --rate=3 --duration=10000      $(arg path)$(arg file_0)      --topic /radar_enhanced_pcl /rgb_cam/image_raw/compressed /barometer/filtered /vectornav/imu      ">  </node>
```

- `pkg="rosbag"`: 指定ROS软件包为`rosbag`，表示要执行`rosbag`软件包中的一个节点。
- `type="play"`: 指定要执行的节点类型为`play`，即`rosbag play`节点。
- `name="player"`: 指定节点的名称为`player`。
- `args`: 传递给`rosbag play`节点的命令行参数。
  - `-s 0.5`: 设置开始时间偏移为0.5秒，表示从rosbag文件中的0.5秒处开始播放。
  - `--clock`: 使用ROS时间，即使用ROS系统的时间。
  - `--rate=3`: 设置播放速度为3倍，表示以3倍速播放rosbag文件。
  - `--duration=10000`: 设置播放时长为10000秒，表示播放的最长时间为10000秒。
  - `$(arg path)$(arg file_0)`: 拼接参数中指定的`path`和`file_0`，形成完整的rosbag文件路径。
  - `--topic /radar_enhanced_pcl /rgb_cam/image_raw/compressed /barometer/filtered /vectornav/imu`: 指定要发布的topic列表，包括`/radar_enhanced_pcl`、`/rgb_cam/image_raw/compressed`、`/barometer/filtered`和`/vectornav/imu`

## E、配置文件

### config/params.yaml

ROS参数服务器的配置文件，配置了一个名为`radar_slam`的ROS节点的参数。主要包括与雷达SLAM相关的参数，包括话题名称、坐标系、传感器设置、IMU设置、外部传感器之间的变化等。

##### 如何将params.yaml文件中的参数加载到参数服务器？

在launch文件中使用如下代码

```xml
<!-- radar_graph_slam.launch --> 
<rosparam file="$(find radar_graph_slam)/config/params.yaml" command="load" />
```

## F、include文件

### utility_radar.h

##### 1、定义了`RadarGraphSlamNodelet` ROS节点的参数服务器：`ParamServer()`类。

- `ParamServer()`用于管理节点的相关参数。
- 三个`nodelet`（`PreprocessingNodelet`、`RadarGraphSlamNodelet`、`ScanMatchingOdometryNodelet`）在创建时均会继承`nodelet::Nodelet`和`ParamServer`类
- 注意ROS节点的参数服务器和ROS参数服务的区别。

## G、其他工具

### 1、radar_ego_velocity_estimator.cpp

##### estimate()

- 描述
  - 估计雷达扫描中的自我速度
  - 先通过一定策略找到有效点 `valid_targets`
  - 若有效点大于2
    - 计算去除一定比例的异常值（`outlier`）后的剩余点数量n
    - 对多普勒速度值进行排序，找到第n个中位数元素，并将这个多普勒速度值存在`median` 中
      - 若 `median` 小于零速度阈值，则认为自我速度为零，将v_r设置为0，并将速度满足零速度阈值的点添加到内点点云。
      - 若不小于零
        - 若使用ransac，使用RANSAC算法解决3D问题
        - 若不使用ransac，直接将所有点作为内点
- 输入参数
  - `radar_scan_msg`
    - 类型：`const sensor_msgs::PointCloud2& radar_scan_msg`
    - 雷达扫描消息
- 输出参数
  - `v_r`
    - 类型：`Vector3&`
    - 存储估计得到的自我速度沿`x、y、z`的速度分量
  - `sigma_v_r`
    - 类型：`Vector3&`
    - 速度估计的不确定性或标准差，即沿`x、y、z`的速度误差估计
  - `inlier_radar_msg`
    - 类型：`sensor_msgs::PointCloud2&`
    - 存储那些被认定为与估计模型相符的点（即内点），这些点用于速度估计
  - `outlier_radar_msg`
    - 类型：`sensor_msgs::PointCloud2&`
    - 存储那些被认为是噪声或不适用于速度估计的点（外点）。
- 关键变量
  - `v_pt`
    - 变量类型：`Vector11`
    - 代表一个有效的雷达目标点的特征向量，包含11个元素
      - 点的三维坐标
      - 点的信号强度
      - 点的多普勒速度信息
      - 相对雷达源的距离
      - 方位角和俯仰角
      - 归一化后的三维坐标
- 返回值
  - true
  - false


#  二、运行自己的数据

## A、问题

##### 1、在预处理节点中，cloud_callback 函数型参 eagel_msg 包含哪些信息，与自己采集的 ars548 传感器数据有什么不同

查看 bag 文件的具体内容，需要将其转换为 txt 文件，参考如下链接：https://blog.csdn.net/jppdss/article/details/130029063

- 查看作者提供的 bag 文件

  1. 在 bag 文件的路径下，使用 `rosbga info` 查看 bag 文件相关信息

     ```bash
     rosbag info cp_2022-02-26.bag
     ```

     可知，原话题为：`/radar_enhanced_pcl`。PS：在 `params.yaml` 文件中也可知该信息。

     消息类型为：`sensor_msgs/PointCloud`

  2. 保存该话题为 txt 文件

     ```bash
     rostopic echo -b cp_2022-02-26.bag -p /radar_enhanced_pcl > cp_2022-02-26-radar_enhanced_pcl.txt
     ```

     ctrl + c 提前结束转换，避免文件过大，导致无法打开

  3. 查看 txt 文件

     ![image-20240227144636257](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240227144636257.png)

     ![image-20240227144854629](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240227144854629.png)

     通过 txt 第一行可知，该话题包含了

     - 时间：`%time`
     - 序列号：`field.header.seq`
     - 时间戳：`field.header.stamp`
     - 坐标系：`field.header.frame_id`
     - 6556个点：`filed.points[0-6555].x`,`filed.points[0-6555].y`,`filed.points[0-6555].z`
     - 四个通道
       - 通道名称：`field.channels[0-4].name`
       - 通道中每个点的值：`field.channels[0-4].values[0-6555]`
       - 由 `cloud_callback` 可知，`channels[0].value[i]` 表示点 `i` 的多普勒速度，`channels[2].value[i]` 表示点 `i` 的信号强度。

- 查看自己数据的消息内容

  1. 在 bag 文件的路径下，使用 `rosbga info` 查看bag文件相关信息

     ```bash
     rosbag info RiQingBanShiNeiDiSu3.bag
     ```

     得到点云话题为：`/ars548_process/detection_point_cloud`

  2. 保存该话题为 txt 文件

     ```bash
     rostopic echo -b RiQingBanShiNeiDiSu3.bag -p /ars548_process/detection_point_cloud > RiQingBanShiNeiDiSu3.txt
     ```

  3. 查看 txt 文件

     ![image-20240227153954708](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240227153954708.png)

     可知，该话题包含了

     - 时间：`%time`
     - 序列号：`field.header.seq`
     - 时间戳：`field.header.stamp`
     - 坐标系：`field.header.frame_id`
     - 37个点：`filed.points[0-36].x`,`filed.points[0-36].y`,`filed.points[0-36].z`

- `eagle_msg` 和自己采集的 ars548 数据区别如下：

  1. 点的数量差距很大。

     - `eagle_msg`数据一帧拥有6556个点
     - 而ars548数据一帧只有36个点。
  
     **当前解决思路：**点云融合。
  
  2. 两者的消息格式都为 `sensor_msgs/PointCloud`，但是消息内容有区别
  
     - `eagle_msg` 数据拥有多普勒速度和信号强度信息
     - ars548 未收集到多普勒速度和信号强度信息。
  
     **原因：**
  
     点云消息类型都为：`sensor_msgs/PointCloud`，但是点的消息格式不同。
  
     **当前解决思路：**重新从原始的 ars548 传感器数据流中获取点的信息，定义新的点消息类型，使其包含多普勒速度。

##### 2、原作者提供的数据，包含两种点云数据`/radar_enhanced_pcl`和`/radar_trk`，它们有什么区别？

执行

```bash
rosbag info cp_2022-02-26.bag 
```

可得与毫米波点云相关的话题有两个：

```bash
/radar_enhanced_pcl              5428 msgs    : sensor_msgs/PointCloud     
/radar_trk                       5428 msgs    : sensor_msgs/PointCloud 
```

- `/radar_enhanced_pcl`：
  - 经过了**增强**处理，比如去噪声、配准等。
  - 也是 `preprocessing_nodelet` 用的点云数据。
- `/radar_trk`
  - **原始**的点云数据。
  - 转换为 txt 后，发现该话题一帧也只有 53个 点。而`/radar_enhanced_pcl`一帧有6000多个点。

## B、需要修改的文件:

##### 1、launch文件

- 添加新的launch文件：

  ```xml
  <!-- rosbag_play_radar_NWU.launch -->
  
  <!-- This launch file loads rosbags and makes an octomap file -->
  
  <launch>
  
  <!-- <param name="/use_sim_time" value="true"/> -->
  
  <!-- paths to the rosbag files -->
  <arg name="path" default="/home/dearmoon/datasets/NWU/"/>
  
  <arg name = "file_0" default = "RiQingBuDianBoGaoSu4.bag"/>
  <arg name = "file_1" default = "carpark_400/carpark1_2023-02-01.bag"/>
  <arg name = "file_2" default = "carpark_400/carpark8_normal_2023-01-14.bag"/>
  <arg name = "file_3" default = "carpark_400/carpark0_normal_2023-01-14.bag"/>
  <arg name = "file_4" default = "carpark_400/carpark0_hard_2023-01-14.bag"/>
  <arg name = "file_5" default = "carpark_400/carpark0_2023-01-27.bag"/>
  <arg name = "file_6" default = "carpark_400/carpark8_2023-01-27.bag"/>
  
  <!-- Plays the dataset. WARNING: changing 'rate' will cause interactions with the demo.  -->
  <!--  /radar_pcl /radar_trk -->
  <node pkg="rosbag" type="play" name="player"
      args = "-s 0.5 --clock --rate=3 --duration=10000
      $(arg path)$(arg file_0)
      --topic /ars548_process/detection_point_cloud
      ">
  </node>
  
  </launch>
  ```

- radar_graph_slam.launch

  - 添加

    ```xml
    <include file="$(find radar_graph_slam)/launch/rosbag_play_radar_NWU.launch" />
    ```

  - 注释下行

    ```xml
    <include file="$(find radar_graph_slam)/launch/rosbag_play_radar_carpark1.launch" />
    ```

##### 2、参数相关的文件

- `config/params.yaml`

  该文件代表 ROS 参数服务器

  - Topics 部分

    ```yaml
    pointCloudTopic : "/ars548_process/detection_point_cloud"
    ```

- `include/utility_radar.h`

  ROS 节点的参数服务器

  - Topics 部分

    ```c++
    nh.param<std::string>("radar_slam/pointCloudTopic", pointCloudTopic, "/ars548_process/detection_point_cloud");
    ```

##### 3、节点文件

- `preprocessing_nodelet.cpp`

  根据自己写的毫米波 RosDriver，信号强度存储在点云消息的通道1。

  `eagle_msg->channels[2].values[i]` 更改为 `eagle->msg->channels[1].values[i]`

- `radar_graph_slam_nodelet.cpp`

  修改点云话题 `points_topic`

  ```cpp
  points_topic = private_nh.param<std::string>("points_topic", "/ars548_process/detection_point_cloud");
  ```

- `scan_matching_odometry_nodelet.cpp`

  修改点云话题 `points_topic`

  ```cpp
  points_topic = private_nh.param<std::string>("points_topic", "/ars548_process/detection_point_cloud");
  ```
  
  修改关键帧相关参数
  
  ```c++
   void intialize_params(){	
      ......
   	keyframe_delta_trans = pnh.param<double>("keyframe_delta_trans", 0.15);                 // 关键帧之间的最小平移距离
      keyframe_delta_angle = pnh.param<double>("keyframe_delta_angle", 0.05);                 // 关键帧之间的最小旋转角度
      keyframe_delta_time = pnh.param<double>("keyframe_delta_time", 0.1);                    // 关键帧之间的最小时间间隔
  	......
   }
  ```
  
  





## D、数据增强

##### 动机：

采集到的 ars548 点云数据过于稀疏，一帧30-60个点，且包含很多噪声。而运行 4DRadarSlam 所使用的点云数据一帧包含了 6000 多个点。

##### 方法：

- 超分辨率（Super Resolution）技术

  - https://arxiv.org/abs/2306.09839

- 点云配准方法

- 点云插值

  - 最邻近插值
  - 双线性插值
  - 高斯过程插值

- 深度学习方法

##### github 相关仓库

- Zadar Labs：

  Zadar Labs 是一个致力于 4D 毫米波雷达的公司，他们的 GitHub 存储库包含有关 4D 毫米波雷达的深度学习处理器单元（DPU）等项目。

- TI mmWave SDK：

  德州仪器（Texas Instruments）提供了一个用于毫米波雷达的开发工具包（SDK）。你可以在这里找到与 4D 毫米波雷达相关的示例代码和资源。

- Open3D：

  Open3D 是一个用于 3D 数据处理的开源库，包括点云处理。虽然它不专门针对 4D 毫米波雷达，但你可以在其中找到一些有用的功能。

- PCL (Point Cloud Library)

  PCL 是一个广泛使用的点云处理库，也包括一些点云配准和插值算法。虽然它不是专门为 4D 毫米波雷达设计的，但你可以在其中找到一些通用的点云处理工具。



##### 使用了 ars548 传感器的数据集：

- Dual-Radar

  https://github.com/adept-thu/Dual-Radar
  
- k-Radar

### 1、点云插值

##### 1.1、线性插值

```python
import rospy
from sensor_msgs.msg import PointCloud
from sensor_msgs.msg import ChannelFloat32

def increase_point_cloud_density(original_pc):
    # Copy the header
    new_pc = PointCloud()
    new_pc.header = original_pc.header

    # Copy the points
    new_pc.points = original_pc.points

    # Extract the channels
    velocity_channel = None
    intensity_channel = None
    for channel in original_pc.channels:
        if channel.name == "velocity":
            velocity_channel = channel
        elif channel.name == "intensity":
            intensity_channel = channel

    # Check if channels exist
    if velocity_channel is None or intensity_channel is None:
        rospy.logerr("Velocity or intensity channel not found in the point cloud.")
        return None

    # Interpolate between consecutive points
    new_points = []
    new_velocity_values = []
    new_intensity_values = []
    for i in range(len(original_pc.points) - 1):
        point1 = original_pc.points[i]
        point2 = original_pc.points[i + 1]
        num_interpolated_points = 10  # Adjust this value as needed
        for j in range(num_interpolated_points):
            ratio = float(j) / num_interpolated_points
            new_x = point1.x + (point2.x - point1.x) * ratio
            new_y = point1.y + (point2.y - point1.y) * ratio
            new_z = point1.z + (point2.z - point1.z) * ratio
            new_point = rospy.Point32(new_x, new_y, new_z)
            new_points.append(new_point)
            # Interpolate velocity and intensity values
            new_velocity = velocity_channel.values[i] + (velocity_channel.values[i + 1] - velocity_channel.values[i]) * ratio
            new_velocity_values.append(new_velocity)
            new_intensity = intensity_channel.values[i] + (intensity_channel.values[i + 1] - intensity_channel.values[i]) * ratio
            new_intensity_values.append(new_intensity)

    # Append the last point
    new_points.append(original_pc.points[-1])
    new_velocity_values.append(velocity_channel.values[-1])
    new_intensity_values.append(intensity_channel.values[-1])

    # Assign new points and values to the point cloud
    new_pc.points = new_points

    # Add the channels to the new point cloud
    new_velocity_channel = ChannelFloat32()
    new_velocity_channel.name = "velocity"
    new_velocity_channel.values = new_velocity_values
    new_intensity_channel = ChannelFloat32()
    new_intensity_channel.name = "intensity"
    new_intensity_channel.values = new_intensity_values
    new_pc.channels.append(new_velocity_channel)
    new_pc.channels.append(new_intensity_channel)

    return new_pc

def main():
    rospy.init_node('point_cloud_density_increaser', anonymous=True)
    original_pc_topic = "/ars548_process/detection_point_cloud"
    increased_pc_topic = "/ars548_process/increased_point_cloud"

    original_pc_sub = rospy.Subscriber(original_pc_topic, PointCloud, original_pc_callback)
    increased_pc_pub = rospy.Publisher(increased_pc_topic, PointCloud, queue_size=10)

    rospy.spin()

def original_pc_callback(original_pc):
    increased_pc = increase_point_cloud_density(original_pc)
    if increased_pc is not None:
        increased_pc_pub.publish(increased_pc)

if __name__ == "__main__":
    main()

```

# 三、修改思路

## 1、IMU融合

### 问题1：bash报错

加入IMU的部分后，产生如下错误

```bash
[radarslam_nodelet_manager-2] process has died [pid 48909, exit code -11, cmd /opt/ros/noetic/lib/nodelet/nodelet manager __name:=radarslam_nodelet_manager __log:=/home/dearmoon/.ros/log/3010c130-fbcd-11ee-a86c-554fd01168bd/radarslam_nodelet_manager-2.log].
log file: /home/dearmoon/.ros/log/3010c130-fbcd-11ee-a86c-554fd01168bd/radarslam_nodelet_manager-2*.log
```

#### 原因：

`exit code -11` 通常表示程序由于段错误而非正常退出。段错误的原因可能包括：内存访问错误、空指针解引用、内存溢出等。

这种错误通常意味着程序试图访问未分配给它的内存区域或者试图访问已经释放的内存区域。

总之，程序试图访问某些不存在的字段。

#### 问题位置定位：

1. 可能是 imu 时间戳混乱
   - 该问题已排除
   - 毫米波雷达时间戳未排除
2. 不管错误，反复运行，`scan_matching_odometry_nodelet.cpp` 359行，输出的值为 NAN
3. `preprocessing_nodelet` 执行完第八个 `cloud_callback` 后报错。
4. 跑原作者数据集，启动 imu 融合就会报错
   - 排除是数据集的问题。



==TODO: 使用GDB运行程序，找到段错误位置==

## 2、利用激光雷达辅助，增强毫米波雷达数据

### 2.1、slam预处理节点修改

##### 动机：

增强后的点云格式为 `sensor_msgs::PointCloud2`，而原slam入口的点云回调函数参数为 `sensor_msgs::PointCloud`，因此需要修改点云回调函数。

##### 修改内容：

1. 引入自定义点云头文件 `point_xyzidv.h`
2. 声明 `pcl::PointCloud<pcl::PointXYZIDV>` 类型的变量
   - 使用 `fromROSMsg` 将输入点云转为上述类型
   - 遍历每一个点，得到它们的xyz坐标，信号强度和多普勒速度


### 2.2、自我速度评估器修改

##### 动机：

填充的激光雷达点

##### 修改内容：

- 添加头文件 `cmath`

- 若点的 `doppler` 为 `NAN`，跳过

### 2.3、参数文件修改

##### 动机：

当前点云话题为 `/radar_merged`

##### 修改内容：

`params.yaml` 和 `utility_radar.h` 文件中修改话题内容

## 3、自己采集的数据

编写ROS驱动，

数据采集的过程，图片

## 4、点云配准算法改进

使用 GNC 而不是 RANSAC



