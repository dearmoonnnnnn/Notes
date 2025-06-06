# 零、概念&问题&相关资料

## 概念：

##### 1、绝对位姿误差(APE)

Absolute Pose Error

**作用：**衡量估计的位姿与实际真实位姿之间的差异

使用轨迹上对应时间点的估计位姿和地面真实位姿的欧几里得距离来计算。

公式如下：
$$
\text{APE}(\mathbf{T}, \mathbf{\hat{T}}) =
\frac{1}{N} \sum_{i=1} ^ N \| \mathbf{T}_i - \mathbf{\hat{T}}_i\|
$$

- $T_i$是第i个时间点的真实位姿

- $\hat{T_i}$是第i个时间点的估计位姿

- N为轨迹中时间点的数量

- $\| x\|$​表示x的范数，用于衡量向量或矩阵的大小或长度

  - 向量范数

    - 1-范数（曼哈顿范数）

    $$
    \|\mathbf{x}\|_1 = \sum_{i=1} ^n |x_i|
    $$

    - 2-范数（欧几里得范数）
      $$
      \| \mathbf{x} \|_2 = \left( \sum_{i=1}^{n} |x_i|^2 \right)^{1/2}
      $$

- 

$$
\text{ATE} = \sqrt{\frac{1}{N} \sum_{i=1}^{N} \left\| \mathbf{T}_i - \mathbf{\hat{T}}_i \right\|^2}
$$

##### 2、相对位姿误差（RPE）

Relative Pose Error，是相对误差（RE，Relative Error）的具体实现

**作用：**衡量短时间间隔内的位姿估计误差

相邻或固定时间间隔内的估计位姿和真实位姿之间的变化差异。

公式如下：
$$
\text{RPE}(\mathbf{T}, \mathbf{\hat{T}}) = \frac{1}{N-1} \sum_{i=1}^{N-1} \left\| \Delta \mathbf{T}_i - \Delta \mathbf{\hat{T}}_i \right\|
$$

- $\Delta \mathbf{T}_i = \mathbf{T}_{i+1}^{-1} \mathbf{T}_i $
- $\Delta \mathbf{\hat{T}}_i = \mathbf{\hat{T}}_{i+1}^{-1} \mathbf{\hat{T}}_i$​
- N为轨迹上的时间点总数

###### 问题：为什么$\Delta \mathbf{T}_i = \mathbf{T}_{i+1}^{-1} \mathbf{T}_i $代表相邻时间点之间的位姿变换

- $\mathbf{T}_i$
  - 表示第$i$个时间点相对于原点的位姿
- $\mathbf{T}^{-1}_{i+1}$
  - 将第$i+1$​个时间点的坐标系变换到原点坐标
  - 这意味着$\mathbf{T}^{-1}_{i+1}$将世界坐标系中的点变换到第$i+1$个时间点的坐标系中
- 组合变换$\mathbf{T}^{-1}_{i+1} \mathbf{T}_i$
  - 将第$i$个时间点的位姿从原点坐标系变换到第$i+1$个时间点的坐标系中。
  - 从而得到第$i$个时间点到第$i+1$个时间点的相对位姿变化。

##### 3、ATE和RTE

绝对轨迹误差，相对轨迹误差

**和APE、RPE的区别**：

<img src="https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/60e107aec977f9fc709f645a18eede79.png" alt="60e107aec977f9fc709f645a18eede79" style="zoom: 33%;" />

- ATE和RTE需要轨迹对齐
  - 左边是未对齐直接计算轨迹的误差
  - 右边是对齐后的误差
- APE和RPE不进行轨迹对齐，直接计算位姿的误差

##### 总结：

第一个字母

- A：绝对
  - 真值与估计值直接的误差
- R：相对
  - 真值与估计值相邻帧之间变化的误差
  - 关注局部片段之间的相对运动

第二个字母

- P：位姿
- T：轨迹

第三个字母

- E：误差





## 问题：

##### 1、如何将自己生成的轨迹转换为txt文档

见

https://github.com/letMeEmoForAWhile/Notes/blob/main/%E8%87%AA%E5%8A%A8%E9%A9%BE%E9%A9%B6/SLAM/4D%E6%AF%AB%E7%B1%B3%E6%B3%A2SLAM/SLAM%E6%95%88%E6%9E%9C%E8%AF%84%E4%BC%B0/4DRadarSLAM%E5%AF%BC%E5%87%BA%E8%BD%A8%E8%BF%B9.md

##### 2、什么是箱线图，为什么使用箱线图表示评估结果

**箱线图图例解释**

- **箱体（Box）**：表示数据的四分位范围（IQR， Interquartile Range）
  - 箱体底部是第一个四分位数（Q1），表示数据中25%的值。
  - 箱体顶部是第三个四分位数（Q3），表示数据中75%的值。
  - 箱体内的水平线是中位数。
- **胡须（Whiskers）**：表示除去异常值以外的数据范围
  - 下胡须从Q1延伸到最小值（不低于 Q1 - 1.5*IQR）
  - 上胡须从Q3延伸到最大值（不高于 Q3 + 1.5*IQR）
- **异常值（Outliers）**：如果有，会显示为箱体和胡须之外的单独点。



**以 `rel_translation_error.pdf` 为例**

![mh05_rel_translation_error](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/mh05_rel_translation_error.png)

- 横坐标
  - 距离
- 纵坐标
  - 相对平移误差，相邻点之间的平移误差
- 每一个距离只有一个点，为什么会有统计误差？
  - 横坐标9米的箱线图，表示 0-9米内，所有相邻点对之间的平移误差的统计。

##### 3、相对轨迹误差为什么还要对齐

## 相关资料

##### 评价指标：

[SLAM中的位姿与轨迹评价指标:APE、RPE、ATE、RTE_ape计算 slam-CSDN博客](https://blog.csdn.net/lovely_yoshino/article/details/128505833)

##### evo：

- 项目地址

  https://github.com/MichaelGrupp/evo

- 博客

  https://zhuanlan.zhihu.com/p/259349163

  https://zhuanlan.zhihu.com/p/672731463

  https://blog.csdn.net/u010196944/article/details/129384012

##### rpg

- 项目地址：

  [uzh-rpg/rpg_trajectory_evaluation: Toolbox for quantitative trajectory evaluation of VO/VIO (github.com)](https://github.com/uzh-rpg/rpg_trajectory_evaluation)

- 安装与使用

  https://blog.csdn.net/Android_WPF/article/details/132589717


# 一、evo (APE/ATE + RPE)

APE+RPE

##### 安装1：pip

1. 创建虚拟环境 `python3.8`

2. pip安装

   ```bash
   sudo python -m pip install evo --upgrade --no-binary evo  # or python2, python3...
   ```

##### 安装2：源码安装

- pip 安装只有核心库

  

##### 可用的数据格式：

- tum，文件格式为txt，每一行代表一个位姿

  - 具体格式

    ```perl
    timestamp tx ty tz qx qy qz qw
    ```

    - `timestamp`：时间戳
    - `tx, ty, tz`:  平移向量的三个分量，单位：米
    - `qx, qy, qz, qw`: 四元数的旋转部分

- kitti，txt，每一行代表一个位姿

  - 格式

    ```
    r00 r01 r02 tx r10 r11 r12 ty r20 r21 r22 tz 
    ```

    - `tx, ty, tz`: 表示平移向量的三个分量（单位：米）
    - `r00, r01 ,r02, r10, r11, r12, r20, r21, r22`: 表示旋转矩阵的元素
    
  - 没有时间戳，使用该格式比较两个轨迹时，需要确保位姿的数量完全一致

- EuRoc，CSV

  - 格式

    ```less
    #timestamp, p_RS_R_x [m], p_RS_R_y [m], p_RS_R_z [m], q_RS_w [], q_RS_x [], q_RS_y [], q_RS_z []
    ```

    - `#timestamp`: 时间戳，表示数据采集的时间
    - `p_RS_R_x, p_RS_R_y, p_RS_R_z`: 平移向量的三个分量
    - `q_RS_w, q_RS_x, q_RS_y, q_RS_z`：四元数的旋转部分

- `bag` & `bag2`

  - 包含如下话题的bag文件
    - `geometry_msgs/PoseStamped`
    - `geometry_msgs/TransformStamped`
    - `geometry_msgs/PoseWithCovarianceStamped`
    - `geometry_msgs/PointStamped`
    - `nav_msgs/Odometry`
  - v1.9版之后 TF2 也被支持

PS: 使用evo评估两条轨迹的绝对位姿误差时，待比较的两者文件格式需要一致

## 结果图解释:

##### 1、轨迹对比图

<img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/markers.png" alt="img" style="zoom:67%;" />

**横纵坐标**：

- **横坐标（x）**：表示机器人在 x 轴方向上的位置，单位为米（m）。
- **纵坐标（y）**：表示机器人在 y 轴方向上的位置，单位为米（m）。
- **z 坐标**：表示机器人在 z 轴方向上的位置，单位为米（m）。

**图例**：

- **reference（灰色虚线）**：真实的轨迹。
- **trajectory（蓝色实线）**：估计的轨迹。

##### 2：误差统计图

<img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_stats.png" alt="evo" style="zoom:67%;" />

**横纵坐标**：

- **横坐标（APE (m)）**：绝对位姿误差（APE）的统计值，单位为米（m）。
- **纵坐标**：误差统计指标，包括标准差（std）、均方根误差（rmse）、最小值（min）、中位数（median）、平均值（mean）、最大值（max）。

**图例**：

- **KITTI_00_ORB（蓝色条形）**：ORB-SLAM 系统在 KITTI_00 数据集上的误差统计结果。
- **KITTI_00_SPTAM（绿色条形）**：SPTAM 系统在 KITTI_00 数据集上的误差统计结果。

##### 3、小提琴图

<img src="https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/res_violin.png" alt="evo" style="zoom:67%;" />

**横纵坐标**：

- **横坐标**：不同的 SLAM 系统（KITTI_00_ORB 和 KITTI_00_SPTAM）。
- **纵坐标（APE (m)）**：绝对位姿误差（APE），单位为米（m）。

**图例**：

- **KITTI_00_ORB（蓝色）**：ORB-SLAM 系统在 KITTI_00 数据集上的误差分布。
- **KITTI_00_SPTAM（绿色）**：SPTAM 系统在 KITTI_00 数据集上的误差分布。

**小提琴图**：

- **外形轮廓**：表示误差的分布形状和概率密度。轮廓越宽的地方表示该误差值的出现频率越高。

- **中间的白点**：表示数据的中位数。

- **黑色粗线**：表示数据的上下四分位数（即 25th 和 75th 百分位数）。

- **细线（须）**：表示数据的范围（通常是 1.5 倍的四分位距以内的数据点）。

# 二、rpg (ATE + RPE)

##### 安装

1. 安装虚拟环境python2.7

   ```bash
   conda create -n rpg python=2.7
   conda activate rpg
   ```

2. 安装依赖

   ```bash
   pip install numpy
   pip install matplotlib 
   pip install colorama 
   pip install ruamel.yaml
   ```

3. 下载源码、编译安装

   ```bash
   mkdir -p rpg_eval_ws/src
   cd rpg_eval_ws/src
   git clone https://github.com/uzh-rpg/rpg_trajectory_evaluation
   git clone https://github.com/catkin/catkin_simple
   cd ..
   catkin_make
   source devel/setup.bash
   rosrun rpg_trajectory_evaluation analyze_trajectory_single.py <result_folder>
   ```


##### 可用的数据格式：

`EuRoc`格式

##### 示例： 作为 ROS 包

```bash
source devel/setup.bash
rosrun rpg_trajectory_evalution analyze_trajectory_single.py <result_folder>
```

##### 示例：作为 python 脚本

```bash
python2 analyze_trajectory_single.py <result_folder> 
```

`<result_folder>`存放真值和估计值，名称要对应

- `stamped_groundtruth.txt`
- `stamped_traj_estimate.txt`



##### 测评多个轨迹：

参考

[SLAM轨迹精度测评(TUM格式)-CSDN博客](https://blog.csdn.net/weixin_41469272/article/details/119885449?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1-119885449-blog-113811380.235^v43^pc_blog_bottom_relevance_base8&spm=1001.2101.3001.4242.2&utm_relevant_index=4)

## 结果图解释：

##### 1、`rel_translation_error.pdf`

![mh05_rel_translation_error](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/mh05_rel_translation_error.png)

**含义**：相对平移误差（Relative Translation Error，RTE）

- **描述**：展示了轨迹中相邻时间点之间的平移（均方根）误差。
- **用途**：评估SLAM算法短时间尺度内的平移精度，适用于局部一致性的评估

##### 2. `rel_translation_error_perc.pdf`

<img src="https://raw.githubusercontent.com/dearmoonnnnnn/typoraImage/main/img/image-20240910155148394.png" alt="image-20240910155148394" style="zoom:67%;" />

**含义**：相对平移误差的百分比表示（Relative Translation Error percentage）

- 相对平移误差的百分比表示，通常是相对于运动距离的百分比。这是相对平移误差的归一化版本，用于消除因不同轨迹长度导致的误差范围不同。
- 有助于不同轨迹的平移误差比较，特别是当运动范围不同或不同数据集的运动距离差异较大时。

##### 3. `rel_yaw_error.pdf`

<img src="https://raw.githubusercontent.com/dearmoonnnnnn/typoraImage/main/img/image-20240910155303990.png" alt="image-20240910155303990" style="zoom: 67%;" />

**含义**：Relative Yaw Error

- 相邻帧之间的相对偏航角误差。
  - 偏航角：绕 z 轴的旋转，RYE 衡量的是轨迹估计中的方向误差。

##### 4.  `rotation_error_sim3_-1.pdf`

<img src="https://raw.githubusercontent.com/dearmoonnnnnn/typoraImage/main/img/image-20240910155805401.png" alt="image-20240910155805401" style="zoom: 67%;" />

**含义**：Rotation Error, SIM(3）

- 使用 SIM(3) **对齐**方法计算的旋转误差。SIM(3) 不仅考虑旋转和平移，还考虑尺度变换。
- 用于处理不同尺度的估计和真实轨迹，特别是重建场景或位姿的尺度不一致时。

##### 5. `scale_error_sim3_-1.pdf`

<img src="https://raw.githubusercontent.com/dearmoonnnnnn/typoraImage/main/img/image-20240910160102877.png" alt="image-20240910160102877" style="zoom: 67%;" />

**含义**：Scale Error(SIM3)

- SIM(3) 对齐中的尺度误差。SIM(3)考虑了轨迹对齐时可能存在的缩放差异，尺度误差表示在轨迹估计与真实轨迹之间的尺度不一致性。
- 用于测量 SLAM 系统在全局尺度估计中的偏差，特别是在基于视觉或稀疏特征的 SLAM 系统中，尺度误差通常反映了深度估计的不准确性。

##### 6. `trajectory_side_sim3_-1.pdf`

<img src="https://raw.githubusercontent.com/dearmoonnnnnn/typoraImage/main/img/image-20240910160605784.png" alt="image-20240910160605784" style="zoom:67%;" />

**含义**：Trajectory Side View（sim3）

- 基于SIM(3) 对齐的轨迹侧视图，显示了估计轨迹和真实轨迹在侧面视角上的差异。

##### 7. `trajectory_top_sim3_-1.pdf`

<img src="https://raw.githubusercontent.com/dearmoonnnnnn/typoraImage/main/img/image-20240910161257168.png" alt="image-20240910161257168" style="zoom: 67%;" />

**含义**：Trajectory Top View

- 基于 SIM(3) 对齐的轨迹顶视图，显示估计轨迹和真实轨迹在俯仰角上的差异。
- 便于分析轨迹在二位平面上的位置误差。

##### 8. `translation_error_sim3_-1.pdf`

<img src="https://raw.githubusercontent.com/dearmoonnnnnn/typoraImage/main/img/image-20240910161319761.png" alt="image-20240910161319761" style="zoom:67%;" />

**含义**：Translation Error (SIM3)

- 基于 SIM(3) 对齐的平移误差，表示轨迹估计中平移部分的误差。
- 用于评估在不同位置上的平移误差，尤其在包含尺度调整过的对齐过程中，平移误差的表现。

# 三、格式转换

##### 1. `bag` 转为 `tum`

若使用ROS版

```bash
# 提取轨迹到 TUM 格式的 txt 文件（时间戳 + xyz + 四元数）
rosrun evo_ros bag_topic_to_txt /path/to/your.bag /slam_pose slam_traj_tum.txt --tum
```

- `/path/to/your.bag` ：存储轨迹的 `bag` 文件
- `/slam_pose` : 轨迹的话题



若使用 python 版

```bash
evo_traj bag /path/to/your.bag /topic_name --save_as_tum
```



##### 2. `.txt` 转为 `.tum`

- 只需直接更改文件后缀为 `.tum`

- 注意事项

  - 数据格式是否为 `tum` 格式，即每一行表示 

    `timestamp tx ty tz qx qy qz qw`

  - 四元数是否已经归一化
  - 是否已经去除标题行
    - 若第一行是 `timestamp tx ty tz qx qy qz qw`，则需要去除。



# 四、评估 4DRadarSLAM

## 1. 导出 SLAM 轨迹

[导出轨迹](./4DRadarSLAM导出轨迹.md)：

- 数据格式：tum 格式 
  - timestamp tx ty tz qx qy qz qw



真值：

- 数据格式：tum 格式 
  - timestamp tx ty tz qx qy qz qw

## 2. 绘制轨迹对比图

### 2.1  `rpg` 

```bash
source devel/setup.bash
python2 analyze_trajectory_single.py <result_folder> 
```

`<result_folder>`存放真值和估计值，名称要对应

- `stamped_groundtruth.txt`
- `stamped_traj_estimate.txt`

### 2.2 evo 

将存储的轨迹 `bag` 文件转换为 `evo` 可用格式

```bash
evo_traj bag XRGB.bag /radar_graph_slam/aftmapped_odom --save_as_tum
```

绘制轨迹

```bash
evo_traj tum aft_mapped_to_init.tum -p
```

- 增加真值

  ```bash
  evo_traj tum aft_mapped_to_init.tum --ref=ground_truth.tum -p --plot_mode=xzy
  ```

  

## 3. 定量数据评估

### 3.2 evo

#### 3.2.1 计算 ATE | 绝对轨迹误差

```bash
evo_ape tum ground_truth.tum slam_traj.tum -va --plot --save_results results/ate.zip
```

- 关键参数
  - `-va` : 显示详细的统计信息（RMSE、均值、中位数等）
  - `--plot`  ：生成轨迹对比图和误差分布图
  - `--save_result` : 保存结果以便后续分析

#### 3.2.2 计算 RPE | 相对轨迹误差

```bash
evo_rpe tum ground_truth.tum slam_traj.tum -va --plot --save_results results/rpe.zip
```

#### 3.2.3 常见问题

##### 问题 1：时间戳不同步

- **现象**：轨迹和真值的时间戳未对齐，导致评估错误。

- 解决：使用 `--t_offset` 和 `--t_max_diff` 调整时间偏移：

  ```bash
  evo_ape tum ground_truth.tum slam_traj.tum --t_offset 0.5 --t_max_diff 0.02
  ```

  - `--t_offset`：时间偏移补偿（例如真值比轨迹晚 0.5 秒）。
  - `--t_max_diff`：允许的最大时间戳差异（超出此值的帧会被忽略）。

##### 问题 2：坐标系方向不一致

- **现象**：轨迹与真值的坐标系方向不匹配（例如 z 轴朝上 vs. y 轴朝上）。

- 解决：使用 `--transform` 参数对齐坐标系：

  ```bash
  evo_ape tum ground_truth.tum slam_traj.tum --transform_gt 0,0,0,0,0,0,1,0,0,0,1,0
  ```

  - `--transform_gt`：对真值应用变换矩阵（参数为 12 维，对应 4x4 变换矩阵的前三行）。

##### 问题 3：单目 SLAM 的尺度问题

- **现象**：单目 SLAM 轨迹缺少绝对尺度，导致轨迹与真值尺度不一致。

- 解决：使用 `--align` 和 `--correct_scale` 进行尺度对齐：

  ```bash
  evo_ape tum ground_truth.tum slam_traj.tum --align --correct_scale
  ```

  
