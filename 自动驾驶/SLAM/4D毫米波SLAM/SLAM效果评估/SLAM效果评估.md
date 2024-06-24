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

![60e107aec977f9fc709f645a18eede79](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/60e107aec977f9fc709f645a18eede79.png)

- ATE和RTE需要轨迹对齐
  - 左边是未对齐直接计算轨迹的误差
  - 右边是对齐后的误差
- APE和RPE不进行轨迹对齐，直接计算位姿的误差

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


# 一、evo (APE + RPE)

APE+RPE

##### 安装：

1. 创建虚拟环境`python3.8`

2. pip安装

   ```bash
   sudo python -m pip install evo --upgrade --no-binary evo  # or python2, python3...
   ```

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

##### 简单示例：

```cpp
source devel/setup.bash
rosrun rpg_trajectory_evalution analyze_trajectory_single.py <result_folder>
```

`<result_folder>`存放真值和估计值

## 结果图解释：

##### 1、`rel_translation_error.pdf`

![mh05_rel_translation_error](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/mh05_rel_translation_error.png)

**含义**：相对平移误差（Relative Translation Error，ATE）

- **描述**：展示了轨迹中相邻时间点之间的平移（均方根）误差。
- **用途**：评估SLAM算法短时间尺度内的平移精度
