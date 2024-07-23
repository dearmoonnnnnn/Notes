# 零、相关链接 & 问题 & 概念

## 相关链接

https://github.com/RainerKuemmerle/g2o

## 问题



## 概念



# 一、数据结构

## 1. 顶点（Vertices）

- **`g2o::VertexSE2`**：适用于 2D 平面上的位姿
- **`g2o::VertexSE3`**：适用于 3D 空间上的位姿
- **`g2o::VertexPointXYZ`**：适用于 3D 空间中的点
- **`g2o::VertexSim3Expmap`**：适用于同时进行位姿和尺度优化的相似变换。

## 2. 边（Edges）

- **`g2o::EdgeSE2`**：适用于 2D 平面上的位姿约束
- **`g2o::EdgeSE3`**：适用于 3D 空间中的位姿约束

- **`g2o::EdgePointXYZ`**：适用于 3D 点之间的约束
- **`g2o::EdgeSE3PointXYZ`**：适用于 3D 位姿和 3D 点之间的约束
- **`g2o::EdgeSim3`**：适用于相似变换（Sim(3)）约束

## 3. 其他

- **`g2o::SparseOptimizer`**: 用于构建和优化图的主要类。
- **`g2o::HyperGraph`**: 表示图的基类。
- **`g2o::JacobianWorkspace`**: 用于存储雅可比矩阵。
- **`g2o::SparseBlockMatrix`**: 稀疏矩阵表示，用于存储优化过程中使用的矩阵。

# 二、优化算法

- **`g2o::OptimizationAlgorithmLevenber`**：实现了 `Levenberg-Marquardt` 优化算法
- **`g2o::OptimizationAlgorithmGaussNewton`**：实现了 `Gauss-Newton` 优化算法
- **`g2o::OptimizationAlgorithmDogleg`**: 实现了 `Dogleg` 优化算法

# 三、核函数（Robust Kernels）

- **`g2o::RobustKernelHuber`**: Huber核函数，减少离群点的影响。
- **`g2o::RobustKernelCauchy`**: Cauchy核函数。
- **`g2o::RobustKernelDCS`**: Dynamic Covariance Scaling核函数。

