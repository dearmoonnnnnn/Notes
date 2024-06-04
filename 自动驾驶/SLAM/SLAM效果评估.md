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

    - 等等

##### 2、相对位姿误差（RPE）

Relative Pose Error

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

## 问题：

##### 1、如何将自己生成的轨迹转换为txt文档

相关SLAM项目中有输出轨迹的模块。

## 相关资料

- evo：https://github.com/MichaelGrupp/evo

  https://zhuanlan.zhihu.com/p/259349163

  https://zhuanlan.zhihu.com/p/672731463

  https://blog.csdn.net/u010196944/article/details/129384012

- rpg项目地址：

  [uzh-rpg/rpg_trajectory_evaluation: Toolbox for quantitative trajectory evaluation of VO/VIO (github.com)](https://github.com/uzh-rpg/rpg_trajectory_evaluation)

# 一、EVO

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
    tx ty tz r00 r01 r02 r10 r11 r12 r20 r21 r22
    ```

    - `tx, ty, tz`: 表示平移向量的三个分量（单位：米）
    - `r00, r01 ,r02, r10, r11, r12, r20, r21, r22`: 表示旋转矩阵的元素

- EuRoc，CSV

  - 格式

    ```less
    #timestamp, p_RS_R_x [m], p_RS_R_y [m], p_RS_R_z [m], q_RS_w [], q_RS_x [], q_RS_y [], q_RS_z []
    ```

    - `#timestamp`: 时间戳，表示数据采集的时间
    - `p_RS_R_x, p_RS_R_y, p_RS_R_z`: 平移向量的三个分量
    - `q_RS_w, q_RS_x, q_RS_y, q_RS_z`：四元数的旋转部分

- bag

- bag2

PS: 使用evo评估两条轨迹的绝对位姿误差时，待比较的两者文件格式需要一致
