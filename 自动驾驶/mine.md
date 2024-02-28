# Question

##### 1、毫米波雷达和激光雷达的区别。

- 探测距离：激光雷达一般不超过150；毫米波雷达能超过200米。
- 价格：激光雷达很贵；毫米波雷达较为成熟，价格便宜。
- 抗环境干扰能力：激光雷达在雨雪雾等极端天气下性能较差；毫米波雷达对于极端天气的适应能力更强。
- 探测精度：激光雷达具有更高的角度分辨率和探测精度；毫米波雷达只能“看到”模糊形体，但可以探测动态目标。(动态环境slam)
- 抗杂波干扰能力：激光的传输受杂波影响较小；而毫米波雷达不能完全过滤杂波，导致雷达信号处理错误。

##### 2、选择一个视觉slam方案作为2D分支，选择一个激光slam方案作为3D分支。

##### 3、语义分割对建图效果有很大的提升，具体该怎么加入到框架中。

##### 4、目前slam的方案的缺陷

- 不能做动态环境建图（目前未找到相关论文）

##### 5、4D成像雷达用于多传感器融合slam的可行性

##### 6、slam评价指标

# task

##### 视觉slam原理

##### 激光slam原理

##### IMU

##### 毫米波slam

##### 新的融合方式，跑一个激光雷达算法

##### overlapnet，极端天气下或者数据缺失情况下的解决方法

# possible path

##### 1、根据南京大学的两篇论文，将融合方案迁移到slam中，（现有slam的融合做到什么程度？）

##### 2、车路协同，使用电磁地图。

##### 3、点云分支，4D成像雷达

##### 4、图神经网络处理点云

##### 5、边缘云计算+SLAM提高算法

##### 6、图神经网络4D毫米波雷达图优化

##### 7、4D毫米波雷达 overlapnet

可行度较低。overlapnet使用激光雷达点云生成

##### 8、4D毫米波雷达图优化

##### 9、毫米波雷达标定，修改激光雷达标定，ICP改成自适应概率分布GICP

##### 动态障碍物滤除综述：

https://mp.weixin.qq.com/s/UTJ_yJJ_IMFVYmvifLB8Yg



多传感器融合需要注意的点：

- 参考CamLiRAFT，使用梯度分离，解决不同分支梯度尺度不匹配问题。

##### 生成对抗网络

https://aijishu.com/a/1060000000212521

- 使用生成对抗网络进行传感器融合。

  使用信息生成对抗网络，潜在编码c用作一个分支，生成对抗网络用于另一个分支。<!--该场景下生成的数据是什么-->

- 新的应用方向

  视频预测，预测车辆行驶过程中可能会发生的下个场景



##### 检测小目标的方法：

- 使用长焦摄像头和短焦摄像头结合（已有论文），类似于特征金字塔？



##### 使点云分布更加均匀的方法

- CamLiFlow提出的IDS(Inverse Depth Scaling)。
- PolarNet，在极坐标下表示鸟瞰图。

# slam综述

## 一、slam技术框架

SLAM技术是指同时定位和地图构建（Simultaneous Localization and Mapping）技术，用于实现机器人或其他设备在未知环境中的自主导航和定位。下面是目前主流的SLAM技术框架：

1. 基于滤波器的方法（Filter-based methods）：包括扩展卡尔曼滤波（EKF）、无迹卡尔曼滤波（UKF）和粒子滤波（PF）等。这些方法主要用于处理线性或非线性问题。https://blog.csdn.net/lightningwl/article/details/121549235
2. 基于优化的方法（Optimization-based methods）：主要包括图优化（Graph-based optimization）和因子图优化（Factor graph optimization）等。这些方法主要用于处理非线性问题，能够更好地处理大规模SLAM问题。https://blog.csdn.net/weixin_46405486/article/details/122540859

### 1.1 基于滤波器的方法

滤波方法尤其是EKF方法，在SLAM发展很长的一段历史中一直占据主导地位，早期的大神们研究了各种各样的滤波器来改善滤波效果，SLAM，EKF是必须要掌握的。滤波方法的优缺点：

优点：在计算资源受限、待估计量比较简单的情况下，EKF为代表的滤波方法比较有效，经常用在激光SLAM中。

缺点：存储量和状态量是平方增长关系，存储的是协方差矩阵，不适合大型场景。基于视觉的SLAM方案，路标点（特征点）数据很大，滤波方法根本吃不消，所以此时滤波的方法效率非常低。

### 1.2 基于图优化的方法（graph-based slam）

主要用于视觉slam中，激光中也能使用。

在图优化的方法中（graph-based [slam](https://so.csdn.net/so/search?q=slam&spm=1001.2101.3001.7020)），处理数据的方式就和滤波的方法不同了，它不是在线的纠正位姿，而是把所有数据记下来，最后一次性算账。

图是由节点和边构成，slam问题如何构成图？

- 机器人的位姿是一个节点(node)或者顶点(vertex)；
- 位姿之间的关系构成边(edge),比如t+1时刻和t时刻之间的odometry关系构成边，或者由视觉计算出来的位姿转换矩阵也可以构成边。
- ==一旦图构建完成了，就要调整机器人的位姿去尽量满足这些边构成的约束==

图优化slam问题的前端和后端

1. 构建图，机器人位姿当作顶点，位姿间的关系当做边，这一步称为前端（front-end），往往是传感器信息的堆积。
2. 优化图，调整机器人位姿顶点使其尽量满足边的约束，这一步称为后端（back-end）

![image-20230517195629096](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230517195629096.png)

### 使用深度神经网络的视觉slam方法

[深度学习技术在视觉SLAM中的应用 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/624484889)

[推荐一些视觉SLAM的深度学习方法（下） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/173351259)

1. DeepVO：这是一个使用深度神经网络的视觉里程计方法，使用卷积神经网络（CNN）提取图像特征，并通过循环神经网络（RNN）来估计相机之间的运动。
2. DeepTIO：这是一个基于深度神经网络的弱姿态估计方法，通过训练一个深度卷积神经网络来将输入的图像对齐，从而减少姿态变化对图像匹配的影响。
3. DSO：这是一个基于深度神经网络的直接法视觉里程计方法，使用卷积神经网络来进行图像匹配，并且使用深度神经网络对三维地图进行优化，从而提高定位的准确性。
4. **LSD-SLAM：这是一个基于半直接法的SLAM方法，使用深度神经网络提取稠密深度图，以及特征点检测和描述子。**
5. VISO2-S：这是一个基于半直接法的双目视觉里程计方法，使用深度神经网络提取稠密深度图，以及特征点检测和描述子。

### 使用深度神经网络的激光slam方法

1. L3-Net：这是一种基于深度神经网络的3D激光点云语义分割方法，可以通过分割出不同类型的物体，提高激光SLAM的建图效果。
2. LPL-Net：这是一种基于深度神经网络的激光点云地图配准方法，使用卷积神经网络提取激光点云的特征，通过学习相似度变换来进行地图匹配和配准。
3. VoxelNet：这是一种基于深度神经网络的激光雷达点云目标检测和建图方法，将点云数据转换为体素网格，使用卷积神经网络提取特征进行目标检测和建图。
4. LaserNet：这是一种基于深度神经网络的3D激光点云分割方法，使用卷积神经网络提取特征，并通过预测每个点的类别来实现点云分割。
10. DynaSLAM，可以在运动的场景下实现实时地地图构建。
11. DeepV2D使用一个深度神经网络来预测相邻帧之间的深度图，并在此基础上进行位姿估计。

### 基于深度学习的毫米波SLAM(Millimeter-wave SLAM)

1. "Millimeter-Wave Radar-based SLAM Using Deep Neural Networks"，由J. Chen等人在2019年的IEEE Radar Conference上发表。该论文提出了一种基于深度神经网络的毫米波雷达SLAM方法，通过使用卷积神经网络对毫米波雷达数据进行特征提取，然后使用基于图的优化算法进行地图构建和机器人定位。
2. "DeepRSLAM: Deep Learning Monocular Visual SLAM for Autonomous Vehicles"，由S. Chen等人在2020年的IEEE Intelligent Transportation Systems Conference上发表。该论文提出了一种基于深度学习的单目视觉SLAM方法，可以与毫米波雷达进行融合，通过将深度学习模型集成到视觉SLAM流程中，提高了定位和地图构建的准确性和稳定性。
3. "Millimeter-Wave FMCW Radar-based SLAM using Deep Learning with Multi-sensor Fusion"，由S. Lee等人在2021年的IEEE Access上发表。该论文提出了一种基于深度学习的毫米波雷达SLAM方法，通过将毫米波雷达数据和其他传感器数据进行融合，使用卷积神经网络进行特征提取，然后使用图优化算法进行地图构建和机器人定位。

### 动态环境下的slam方案

1. "Real-time, On-board, Event-based State Estimation"，由D. Gehrig等人在2018年的IEEE International Conference on Robotics and Automation (ICRA)上发表。该论文提出了一种基于事件相机和激光雷达的动态SLAM系统，利用事件相机的高速响应和激光雷达的高精度进行动态障碍物检测和机器人状态估计。
2. "Dynamic SLAM: Visual SLAM for Dynamic Environments using a Moving Object Detection Algorithm"，由A. Carrio等人在2016年的IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS)上发表。该论文提出了一种基于运动物体检测的视觉SLAM方法，通过检测动态障碍物来排除其对地图构建和机器人定位的影响。
3. "Online Loop Closure Detection for Dynamic Environments using Gaussian Mixture Models"，由T. Cieslewski等人在2018年的IROS上发表。该论文提出了一种基于高斯混合模型的在线回环检测方法，能够在动态环境下实时检测回环，从而提高SLAM的鲁棒性和精度。
4. "Dynamic Object SLAM with Semantic Scene Understanding"，由Z. Zhang等人在2021年的IEEE Transactions on Robotics上发表。该论文提出了一种基于语义场景理解的动态物体SLAM方法，能够在复杂动态环境下进行高精度的地图构建和机器人定位。
5. 《**DS-SLAM**: A Semantic Visual SLAM towards Dynamic Environments》
6. 《**DynaSLAM**: Tracking, Mapping and Inpainting in Dynamic Scenes》
7. 《**VDO-SLAM**: A Visual Dynamic Object-aware SLAM System》
8. 《**Detect-SLAM**: Making Object Detection and SLAM Mutually Beneficial》
9. 《**DP-SLAM**: A visual SLAM with moving probability towards dynamic environments》
10. 《**DM-SLAM**: A Feature-Based SLAM System for Rigid Dynamic Scenes》
11. 《**KMOP-vSLAM**: Dynamic Visual SLAM for RGB-D Cameras using K-means and OpenPose》

## 二、现有的slam公开数据集

| 数据集名称    | 传感器                                                       | 场景                                                         | 天气情况(时间)/挑战                                          | 备注                                                         |
| :------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| KITTI         | 激光雷达、摄像头(灰度相机、彩色相机)、GPU、IMU               | 城市街景；乡村；高速公路                                     | 晴朗的白天、阴天/多云、日落、夜间                            | 高质量的视觉-LiDAR数据、准确的地面实况、丰富的序列、闭环、公共基准。最常使用。 |
| EuRoC         | 两个灰度相机(立体设置)、IMU                                  | 机器仓库、普通房间（室内）                                   | 无                                                           | 使用最广泛的数据集之一                                       |
| TUM RGB-D     | Kinect（ RGB相机+深度相机）                                  | 办公室（动态环境）                                           | 无                                                           | 该数据集提出了一套评价标准和指标，成为迄今为止本地化评价的共识 |
| 牛津RobotCar  | 相机(双目、单目、鱼眼)、激光雷达、GPS、IMU                   | 英国牛津市中心                                               | 包含各种时间段、各种天气（如小雨、大雨、阳光直射、雪、黄昏、夜晚） | 长期数据集、长轨迹、视觉-LiDAR 设置、反复遍历、各种具有挑战性的条件。 |
| TartanAir     | （模拟）两个彩色相机(立体设置)、IMU、深度相机、3D激光雷达    | 6类30种：城市、乡村、自然、家庭、公共和科幻等                | 光线变化、照明不佳、恶劣天气、时间和季节效果以及动态物体。   | 支持许多SLAM相关任务的模态，但数据过于完美，无法模拟真实-LiDAR的世界相机模糊或运动失真。 |
| Complex Urban | 两个彩色相机（双目立体）、两个2D-LiDAR、两个3D-LiDAR、GPS、3轴FOG、IMU | 大都市区、住宅区、综合公寓、高速公路、隧道、桥梁、校园等多样复杂的城市场景。 | 晴天、雨天、多云(待考证)                                     | 覆盖了许多复杂的高层建筑（GNSS性能摇摇欲坠）、多车道的道路、拥挤的移动物体，与KITTI等欧洲城市风格完全不同，对算法提出了新的挑战 |
| Radiate       | 毫米波雷达、3D激光雷达、相机、GPS/IMU                        | 公共道路（停车、城市、高速公路和郊区）                       | 太阳、夜晚、雨、雾、降雪                                     | 首个恶劣天气开源数据集                                       |
|               |                                                              |                                                              |                                                              |                                                              |
|               |                                                              |                                                              |                                                              |                                                              |

https://zhuanlan.zhihu.com/p/471979416

### 1、KITTI

slam中最常用的数据集

#### 1.1 传感器配置

激光雷达、摄像头、GPU、IMU

- 2个一百四十万像素的PointGray Flea2灰度相机；
- 2个一百四十万像素的PointGray Flea2彩色相机；
- 4个Edmund的光学镜头，水平视角约为90°，垂直视角约为35°；
- 1个64线的Velodyne旋转激光雷达，10Hz，角分辨率为0.09度，每秒约一百三十万个点，水平视场360°，垂直视场26.8°，至多120米的距离范围；
- 1个OXTS RT3003组合导航系统，6轴，100Hz，分别率为0.02米，0.1°。

#### 1.2 场景：

城市街景数据，乡村、高速公路

#### 1.3 天气

晴朗的白天：序列00、02

多云/阴天：01，03、04、05、06、08

日落：07

夜间：09、10

### 2、EuRoC

[EuRoC数据集简介与使用_euroc dataset_colorsky100的博客-CSDN博客](https://blog.csdn.net/colorsky100/article/details/85331711)

#### 2.1 传感器

双目相机和一个IMU，

#### 2.2 场景

EuRoC数据集包括11个不同场景的数据，其中有些是室内场景，有些是室外场景。下面是数据集中的室内和室外场景：

室内场景：

- V1_01_easy：办公室、走廊、楼梯等
- V1_02_medium：办公室、走廊、实验室等
- V1_03_difficult：大厅、楼梯、实验室等
- V2_01_easy：工厂大厅、停车场等
- V2_02_medium：工厂车间、货车等
- V2_03_difficult：工厂车间、走廊、大厅等

室外场景：

- V1_04_difficult：室外、森林、田野等
- V1_05_difficult：室外、森林、山区等
- V2_04_difficult：室外、森林、山区等
- V2_05_difficult：室外、森林、田野等
- MH_01_easy：室外、街道、公园等

这些场景中的一些包含了快速运动、快速旋转、运动模糊、暗度环境等挑战，是测试SLAM算法性能的有效数据来源。

#### 天气情况未知

### 3、TUM

分为好几个分支：TUM-Mono，TUM-RGB-D(使用最多)，TUM-campus，TUM-Car

#### 3.1 传感器

TUM-Mono:一个灰度相机 Point Grey Flea2

TUM-RGB-D:一个Kinect相机，它包括一个RGB相机和一个深度相机，在数据采集时，相机被固定在一个平台上，并通过机械臂在室内环境中移动。

TUM-Campus：一个SICK LMS 151-2激光雷达和一个IMU

TUM-Car：

1. 六自由度惯性测量单元（IMU）：采用Xsens MTI-G-700 IMU，采样频率为100Hz。
2. 单目相机：采用IDS UI-3580CP-M-GL相机，采样频率为20Hz，分辨率为752x480。
3. GPS/INS组合导航系统：采用NovAtel SPAN-SE系统，采样频率为100Hz。
4. 雷达：采用Velodyne HDL-32E激光雷达，采样频率为10Hz。
5. 距离传感器：采用Sick LMS111激光测距传感器，采样频率为50Hz。

#### 3.2 场景

室内环境（自动驾驶中应该用不到）

- TUM-Mono：一个比较小的办公室内环境，主要包括一些物体、家具、室内地面、墙壁。
- TUM-RGB-D：一个比较大的室内环境，包括一些物体、家具、室内地面、墙壁，也包含了Kinect相机的深度信息。

室外场景

- TUM-Campus：慕尼黑大学校园内的一个开阔区域，包括一些树木、行人、建筑物、车辆等等。
- TUM-Car：在慕尼黑城市内驾驶一辆车采集的数据，包括城市道路、人行道、建筑物、车辆等。

#### 3.3 天气情况（室外场景）

均为晴朗

### 4、RobotCar

[1年，1000公里：牛津RobotCar数据集-汽车开发者社区-51CTO.COM](https://icv.51cto.com/posts/1111)

#### 4.1 传感器

- 5个360度激光雷达（Velodyne HDL-32E），每秒10Hz采集率，每个激光雷达有32个线束
- 5个带有相机和IMU的系统，每个系统有6个摄像头（前、后、左、右、上和下）和一个IMU，相机采集的图像分辨率为1280x960，每秒5Hz采集率

#### 4.2 数据场景

英国牛津市中心的城市环境，包括市中心的繁忙街道、小巷、树木林立的公园、河流、天桥、地下车库和室内停车场等。

#### 4.3 天气

包括晴天、多云、雨天、雪天和夜晚等。同时，数据采集还考虑了不同的光照条件和交通情况，例如拥堵、红绿灯和行人等。

### 5、ICL-NUIM

传感器配置：

- Microsoft Kinect相机

数据采集场景：

- 室内环境，包括办公室、实验室、会议室等
- 涵盖不同的场景和结构，包括室内物体、家具和墙壁等

### 6、Ford Campus Vision and Lidar Data Set

传感器配置：

- 摄像头：该数据集使用了三个不同类型的摄像头，包括Basler A504k，Sony Color XC-999和Sony B/W XC-77，分别采集不同分辨率的图像。
- 激光雷达：该数据集使用了Velodyne HDL-64E激光雷达，它可以同时获取360度的激光扫描数据，共计64个激光束。
- GPS：该数据集使用了Novatel GPS Receiver的OEMV-2模块，用于获取位置和时间戳数据。

数据采集场景：

- 该数据集是在密歇根大学的Ford校园内采集的，包括校园的道路、停车场、建筑物和植被等不同场景。
- 采集的数据包括行驶和停车等不同运动状态下的场景。

数据采集时的天气状况：

- 该数据集的采集时间跨度为3年，涵盖了不同季节的数据。
- 天气状况包括晴天、多云、阴天、雨天等。

### 7、Radiate

[世界首个恶劣天气开源数据集 | Heriot-Watt大学Radiate项目发布 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/405626607)

### 8、包含毫米波雷达的slam数据集

KAIST Urban Dataset

Waymo Open Dataset

## 三、不同雷达的对比





##### 4D成像雷达采购

| 公司          | 型号        | 报价              | 备注                               |
| ------------- | ----------- | ----------------- | ---------------------------------- |
| 德国 大陆集团 | ARS 548     | 1w5               | 武汉大学论文使用该雷达。淘宝可买。 |
| 安波福        | FLR4+、FLR7 | \                 | 未量产                             |
| 采埃孚        | Premiun     | \                 | 已量产、淘宝未找到。               |
| 傲酷          | Eagle       | 2w-3w(未询问实价) | 淘宝可买                           |

# flow的团队

清华李骏院士
