# 零、问题和概念

## 概念：

##### 1、点云配准

点云配准是一种在计算机视觉和机器学习领域广泛使用的技术，主要用于在三维空间中对多个点云数据进行对齐或者对比，从而获取一个全局的、统一的三维模型或者地图。

点云配准的主要步骤通常包括以下几个部分：

1. **预处理**：预处理步骤主要用于清理原始点云数据，包括去噪声、平滑处理、下采样等，以提高后续配准步骤的效率和精度。

2. **特征提取**：在点云数据中提取一些有区别性的特征，比如形状特征、颜色特征、局部特征等，这些特征可以帮助在配准过程中找到对应的点或者区域。

3. **匹配**：在特征空间中寻找最佳的对应点或者区域，使得这些对应点或者区域在空间中的位置尽可能接近。这个步骤通常使用最近邻搜索或者其他的优化方法。

4. **变换估计**：根据匹配的对应点或者区域，估计一个最优的变换矩阵，包括旋转矩阵和平移向量，将一个点云数据变换到另一个点云数据的坐标系下。

5. **误差计算和优化**：计算配准后的点云数据和目标点云数据之间的误差，然后通过优化算法，如迭代最近点（ICP）算法或者其他的优化算法，进一步优化变换矩阵，使得误差最小。

这些步骤可能会反复进行，直到配准误差达到预设的阈值，或者达到最大的迭代次数。

常用的方法：

- GICP
- ICP
- NDT

##### 2、GICP

[经典论文阅读之-GICP（ICP大一统） - 古月居 (guyuehome.com)](https://www.guyuehome.com/41417)

- ICP，Iterative Closest Point，迭代最近点，是一种用于点云配准的算法。

  这通常包括以下步骤：

  1. **初始化：** 选择一个初始的变换参数，例如平移和旋转。
  2. **找对应点：** 将源点云中的点通过当前的变换映射到目标点云中，找到它们在目标点云中的对应点。
  3. **计算最优变换：** 使用找到的对应点，计算一个新的变换，以最小化它们之间的距离。
  4. **迭代：** 重复上述步骤，直到收敛或达到预定的迭代次数。

- GICP，Generalized Iterative Closest Point, 广义迭代最近点，是对传统ICP算法的一种改进。

  传统的ICP可能会受到初始估计敏感性和局部最小值的影响。

  GICP引入更灵活的误差度量和权重调整，==适应不同的噪声和不确定性模型==。

##### 3、NDT

Normal Distributions Transform，正态分布变换，用于点云的匹配和配准。

核心思想：

- 将点云数据表示为一组**多维正态分布**。每个点云可以通过其局部分布的参数来描述。

具体步骤：

1. **离散化：** 将连续的点云数据转换为离散的网格表示。
2. **建模：** 对每个网格单元建立正态分布，表示该单元内点的分布。
3. **匹配：** 寻找最佳的刚体变换，以最小化两组点云之间的正态分布的差异

## 问题：

##### 1、毫米波雷达的主要原理：

[谈谈毫米波雷达的点云形式与分辨能力 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/386615052#三 参数估计)

##### 2、强度扫描上下文和扫描上下文的区别

[Scan Context_scancontext_weixin_49024732的博客-CSDN博客](https://blog.csdn.net/weixin_49024732/article/details/124929349)

扫描上下文使用的是最大高度信息作为上下文，强度扫描上下文使用的是反射信号的最大功率。

##### 3、在毫米波雷达slam中，为什么需要计算雷达的自我速度

为了更好地估计车辆/机器人的运动状态。

运动估计：自我速度估计是运动估计的一个重要组成部分，了解平台的运动方式有助于更准确地估计其未来位置。 

# 一、引言

##### 提出4D成像毫米波雷达SLAM的原因:

1. 4D毫米波雷达在恶劣天气下优于激光雷达。
2. 4D毫米波雷达与2D/3D毫米波雷达相比，提供高程信息和更密集的点云。

##### 文章主要贡献：

1. 提出了一个开源的完整的4D成像毫米波雷达SLAM
2. 在前端的GICP中考虑了点测量的概率分布。在闭环检测中，引入==**强度扫描上下文**==来找到环路候选，加上环路滤波和里程计检查，获得良好的环路闭合。在后端的位姿图中考虑了里程计、闭环和GPS。
3. 在两种类型的平台、五个数据集上进行了广泛的实验，证明了其准确性、鲁棒性和实时性。

##### 3D毫米波雷达SLAM分类

1. 基于特征匹配。将雷达点云视为图像，提取并匹配特定特征，以计算连续扫描之间的变换。
2. 基于==点云注册==。
3. 基于==相关性==。

##### 3D激光雷达SLAM分类

1. 基于点云注册。
2. 基于特征匹配。

##### 4D成像毫米波SLAM：

- 由于4D毫米波雷达噪声大、稀疏性强，提取可靠的边缘和平面特征具有特征性。选择基于点云匹配的方法。

# 二、系统架构

![image-20230713155323891](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230713155323891.png)

系统由三个模块组成：前端、环路检测和后端。

- 前端：4D雷达点云被用作估计里程计和生成关键帧的输入。
- 闭环检测模块：评估每个新的关键帧，确定其是否可以形成闭环。
- 后端：使用g2o构建并优化位姿图，产生优化的位姿作为输出。

## 2.1 前端

### 2.1.1 预处理

- 首先要过滤掉动态对象，来自雷达的多普勒速度信息可以识别这样的物体。
- 估计雷达自身运动：使用文献[33]提出的线性最小二乘法。
- 使用估计的多普勒速度和自身速度，我们可以定位物体的真实速度。

### 2.1.2 扫描到扫描的匹配

##### 输入：

最后一个关键帧$\mathcal{F}_k$和一个新帧$\mathcal{P}_t$

##### 目标：

找到变换从t到k的变换(矩阵)$T^k_t$

##### 思路：

GICP：由于4D毫米波雷达具有更多噪声，不能直接提取几何特征，作者发现GICP的结果相比于ICP和NDT更容易被接受。

##### 具体实现：

将每一个点的空间概率分布考虑到GICP中，提出自适应概率分布GICP(Adaptive Probability Distribution-GICP，APDGICP)

==// TO DO LIST==

### 2.1.2 关键帧的选择

第一个帧被指定为固定关键帧。

后续关键帧需要满足下列**条件**中的一个：

1. 当前帧和最后一个关键帧之间的**平移**超过阈值$\delta_t$
2. 当前帧和最后一个关键帧之间的**旋转**超过阈值$\delta_r$

按经验值设置$\delta_t$为0.5m或者2m，$\delta_r$为15°

## 2.2 闭环检测

每一个关键帧和数据库关键帧比较，判断其是否形成一个闭环

### 2.2.1 闭环预过滤

- **好处：**提前过滤出潜在的候选帧，无需在整个数据库中搜索
- **四条规则：**
  1. 新的闭环查询帧不应离最后一个查询帧太近，并且闭环的帧之间不应该太近。
  2. 确保闭环的帧在一定半径内(即空间上接近)。半径自适应调整。
  3. 根据来自气压计(barometer)的高度信息，讲闭环帧之间的高度差设定为2m的阈值。该规则有助于确保已识别的环路处于相似的高度，并且避免由于海拔差异而出现误报。
  4. 确保闭环的帧具有相似的偏航角度，根据经验，20°的阈值适合避免雷达的误报匹配。

### 2.2.2 扫描上下文

由于传感器的限制和多次反射回波，雷达高度信息具有噪声，因此采用强度扫描上下文<!--Intensity Scan Context: Coding Intensity and Geometry Relations for Loop Closure Detection》(ICRA2020)-->而不是扫描上下文。

- 构建上下文：使用最大高度作为上下文是不准确的，==因此使用反射信号的最大功率==

- 处理视野问题：本文使用的傲酷eagle雷达，视野只有110°，而扫描上下文原本是360°激光雷达提出的。

### 2.2.3 里程计检查

##### 动机：

在执行扫描上下文以找到最可能的闭环后，需要考虑**几何一致性**。单独的扫描上下文可能会引起几何不一致性，对后端位姿图优化造成灾难。

##### 方法：

采用LAMP<!--LAMP: Large-Scale Autonomous Mapping and
Positioning for Exploration of Perceptually-Degraded Subterranean
Environments-->进行里程计检查

## 2.3 后端

![image-20230728133309659](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230728133309659.png)

根据前端里程计、环路闭合和GPS(可选)构建位姿图。使用g2o库对位姿图进行优化。



# 三、实验



# 四、源码解析

源码结构：

```
/4DRadarSLAM
├── apps
│   ├── preprocessing_nodelet.cpp : 定义预处理类
│   ├── radar_graph_slam_nodelet.cpp: 定义RadarGraphSlamNodelet类
│   ├── scan_matching_odometry_nodelet.cpp: 定义ScanMatchingOdometryNodelet类
├── cmake
│   ├── FindG2O.cmake
├── config
│	├── params.yaml
├── doc
├── include
│   ├── dbscan // 聚类算法，用于将数据点划分为具有高密度的区域(簇)和低密度的区域(噪声)
│   │	├── DBSCAN_kdtress.h : 定义一个模板类'DBSCANKdtreeCluster'，主要用于基于k-d树的DBSCAN算法聚类。
│   │	├── DBSCAN_precomp.h
│   │	├── DBSCAN_simple.h
│   ├── g2o
│   │	├── edge_plane_identity.hpp
│   │	├── edge_plane_parallel.hpp
│   │	├── edge_plane_prior.hpp
│   │	├── ......
│   ├── radar_graph_slam
│   │	├── graph_slam.hpp
│   │	├── information_matrix_calculator.hpp
│   │	├── keyframe.hpp
│   │	├── keyframe_updater.hpp
│   │	├── loop_detector.hpp
│   │	├── map_cloud_generator.hpp
│   │	├── nmea_sentence_parser.hpp
│   │	├── polynomial_interpolation.hpp
│   │	├── registrations.hpp
│   │	├── ros_time_hash.hpp
│   │	├── ros_utils.hpp
│   ├── rio_utils
│   │	├── data_types.h
│   │	├── math_helper.h
│   │	├── radar_point_cloud.h
│   │	├── ros_helper.h
│   │	├── simple_profiler.h
│   │	├── strapdown.h
│   ├── scan_context
│   │	├── KDTreeVectorOfVectorsAdaptor.h
│   │	├── Scancontext.h
│   │	├── nanoflann.hpp
│   │	├── tictoc.h
│   ├── radar_ego_velocity_estimator.h
│   ├── utility_radar.h
├── launch
├── msg
├── rviz
├── src
├── srv
└── (编译所需的文件)  
```

##### nodelet：

- 是一种特殊类型的节点(node)实现，用于提高ROS系统的性能和效率。

- ’nodelet‘将一个节点的功能**分解**成更小的、可重用的**组件**，从而减少了ROS节点之间的消息传输(进程间通讯)
- 通过将多个算法节点(nodelet)运行在一个进程中，避免了数据传输的开销。
- 在进程内部，内存是共享的，因此传输数据只需要传递指针即可实现零拷贝。

# 数据处理

carpark1_2022-02-26.bag文件

gt_odom.txt : ground truth 文件，通过什么收集，GPS？

# -1、思考

关键帧的选择策略是否可以改进，比如参考ORB_SLAM2的方案、
