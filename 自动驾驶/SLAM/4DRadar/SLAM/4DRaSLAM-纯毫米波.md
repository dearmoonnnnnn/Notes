# 零、概念和问题

## 概念

##### 1、地面点

hdl_graph_slam算法中的地面点提取采用的是将地面点以上和地面点以下的点滤除掉剩下的就是地面点，思路比较简单，但是地面的定义是根据实际情况自己定义，例如hdl中根据传感器距离地面的安装高度，划定了一个高度范围，认为在此区域内的点是地面点。

##### 2、主成分分析法

##### 3、随机样本共识

##### 4、(关键帧)子地图

 关键帧子地图是代表环境一部分的参考点云。它被用作对齐后续雷达点云的参考。

##### 5、==NDT匹配==

正态分布变换匹配，使用统计方法来估计两个点云之间的转换。

##### 6、多普勒速度

测量点和雷达之间沿径向的相对速度。

##### 7、径向速度

又称视向速度，即物体或天体在观察者视线方向的运动速度。

##### 8、最小二乘估计

最小二乘估计法是 SLAM 中用于参数估计的常用技术。 它涉及最小化观测数据与基于模型的预测值之间的平方误差之和。

#  一、前言

标题：使用4D雷达的基于自我速度预积分的图优化SLAM

**传统雷达分为两类**：

扫描雷达和汽车雷达。

**扫描雷达**可以360度扫描环境，而汽车雷达的视场，提供稀疏的平面点云。

 

**4D雷达**具有与汽车雷达相似的视场，但提供更密集的3D点云和更多信息。

 

4D雷达SLAM的挑战：

由于数据特性的差异，传统的雷达SLAM系统不再适用于4D雷达。

数据集有限。现有的数据集更侧重于目标检测。

 

主要贡献：三点

1、提出一个用于4D雷达数据的SLAM框架，第一个只基于4DRadar雷达的SLAM（武汉大学的集合imu）

2、提出一个滤波方法，减少噪声。提出自我速度预积分因子，提高图优化效果。

3、收集了一个4DRadar数据集并开源，实验验证了该框架的鲁棒性和准确性。

 

# 二、系统架构

![image-20230827102207151](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230827102207151.png)

 三个模块：4D雷达滤波器，自我速度评估、位姿图优化

## 2.1 4D雷达滤波器

### 动机：

原始4D雷达数据通常被噪声和杂波污染。

两种噪声：

- 鬼影检测：由多径射线引起，指的是由环境中不存在的物体反射引起的错误检测。
- 随机点：由虚反射引起，指与环境中任何实际物体都不对应的噪点。

### 算法：

![image-20230827102730221](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230827102730221.png)

##### 对于鬼影检测：

1. 对于当前点云P只保留距离小于阈值$\delta_r$，并且高度与雷达的安装高度H相差$\delta_h$以内的点。
   - 背后的理由：只有这些点可能包含SLAM的兴趣点，即地面点。
2. 然后，使用==主成分分析法(PCA)==计算这些点的法向量，并且只保留法向量接近垂直的点，将其命名为$G_k$
3. 组后，使用==随机样本共识(RANSAC)==从G中提取地面点，如图3(c)所示，这将移除地下的鬼点。

##### 对于随机点：

随机点是典型的虚假目标，因此可以利用两个连续雷达点云之间的不一致性来区别真实目标和随机噪声。

1. 首先，计算上一个点云和当前点云之间的平移和旋转。
2. 然后使用指数图将上一个点云转换到当前帧，构造一个用于固定半径邻居搜索的k维树。
3. 如果当前点云中的某个点在一定半径范围内与k维树中的任何点都不相邻，则其为随机点。

##### ![image-20230827111309000](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230827111309000.png)

- 图a、b为原始点云。c是过滤鬼影检测后的结果，d为去除随机点后的结果

## 2.2 自我速度评估

##### 目标：

根据点云多普勒速度估计车辆的速度。

##### 两种类型的速度：

- 线自我速度：指车辆在直线上的速度。
- 角自我速度：指车辆方向的变化率。

对于世界框架中的一个静态点，它的相对速度是雷达的相反数。

##### 方法：

1. 雷达点云中i点的径向速度$v_{r,i}$与雷达自我速度$v_s$之间的关系：$v_{r,i} = -d_i·v_s$
   - $d_i = (cos𝜃_𝑖cos𝜑_𝑖,sin𝜃_𝑖 cos𝜑_𝑖 , sin𝜑_𝑖 )^T$表示雷达方向

2. 雷达速度在车辆坐标系下的表达：

   ![image-20230828101840888](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230828101840888.png)

   - $\widetilde{v}$和$\widetilde{\omega}$是车辆的线速度和角速度。
   - $t_s$和$R_S$雷达在车辆坐标系下的位置和旋转

3. 将$v_s$带入，可以得到车辆的自我速度和多普勒速度之间的关系。

   ![image-20230828113008705](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230828113008705.png)

**note:**满足上述关系的前提是雷达点静止。这里使用==RANSAC==来除掉动态点。

## 2.3 位姿图优化

##### 目标：

得到准确的位姿。

解决非线性最小二乘问题：$X = arg\space \underset{X}min\space e_𝑂 + e_𝑉 + eL$

得到每个点的旋转和位置(平移)

##### 模块：

- 扫描配准
- 自我速度预积分
- 闭环检测

此外，使用姿态图融合来自子模态的姿态估计，并执行联合优化。如图所示。

![image-20230827130823071](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230827130823071.png)

### A、扫描配准

提出一种直接配准方法，基于扫描到关键帧子地图NDT匹配。

##### 目标：

匹配雷达点云来估计相对变换。

##### 方法的具体实现：

1. 构造关键帧子地图：

   - 首先，4D雷达点云密度不足，不能仅匹配两个连续帧来准确估计位姿。因此采用**滑动窗口**，从关键帧中构建更密集的雷达子地图。

   - 如果最新的关键帧到当前点云的平移或旋转超过阈值，则将当前帧选为新的关键帧，并添加到子地图中。

   - 当关键帧数量超过窗口大小，丢弃最旧的关键帧。

2. 将子地图分成网格单元，然后将每个单元中的雷达点建模为局部正态分布。

   - 在计算正态分布的均值和协方差时，将根据每个点的测量不确定性计算其概率密度函数。

3. 扫描配准的误差为估计的相对姿态和地面真实姿态之间的差异。

### B、自我速度预积分

除了扫描配准，自我速度是另一个重要的运动信息源。我们使用它来施加额外的可靠姿态估计，并提高SLAM框架的准确性和稳健性。

受IMU的预积分启发，作者提出了4D雷达预积分。

##### 获得评估的自我速度：

在t时刻，令评估的自我速度中的角速度为$\widetilde{\omega}_t$，线速度为$\widetilde{v}_t$，由于受到时变白噪声$\eta^w_t$和$\eta^v_t$的影响：
$$
\widetilde{\omega}_t=\omega_t+\eta^{\omega}_t
$$

$$
\widetilde{v}_t=R^T_{t} \ _Wv_t +\eta^v_t
$$

其中，

- $\omega_t$表示世界坐标系下的真实角速度。
- $_Wv_t$表示世界坐标系下的真实线速度。
- $R_t$表示车辆在世界坐标下的旋转。

##### 对于一个极短的时间间隔[t,t+△t]，计算旋转和平移量

假设两个速度在这个时间间隔内保持不变
$$
R_{t+\Delta t}=R_tExp(\omega_t \Delta t) = R_tExp((\widetilde{\omega}_t-\eta^w_t) \Delta t )
$$

$$
p_{t + \Delta{t}} = p_t + _Wv_t \Delta t = p_t + R_t(\widetilde{v}_t-\eta^v_t) \Delta t
$$

##### 通过i时刻的关键帧得到j时刻关键帧的旋转和平移

![image-20230828095238207](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230828095238207.png)

##### 得到相对的旋转和平移$\Delta R_{ij}$和相对平移$\Delta p_{ij}$

![image-20230828100848930](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230828100848930.png)

![image-20230828100905441](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230828100905441.png)

$\Delta R_{ij}$和$\Delta p_{ij}$就是图节点i和j的额外约束，他们被定义为自我速度预积分因子。

### C、闭环检测

采用扫描上下文(scan context)。

<img src="https://pic1.zhimg.com/v2-537af97fbb3f0fb7f306c2f5ae7bbe7c_r.jpg" alt="img" style="zoom:67%;" />

用于激光雷达时，使用每个方格中的最大高度作为编码。同4DradarSLAM这篇论文，由于4D毫米波雷达很可能被噪声干扰，在这里使用最大强度作为编码信息。

# 三、数据集

##### 开源网址：

[4D Radar Dataset-Autonomous Robot Laboratory (sjtu.edu.cn)](https://robotics.sjtu.edu.cn/en/xwxshd/1203.html)

##### 传感器：

- ZF FRGen21 4D毫米波雷达，参数如图所示

  ![image-20230827144346848](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230827144346848.png)

- 激光雷达：10Hz，一帧230400个点

- GNSS(全球卫星导航系统)：作为ground truth，50Hz

##### 传感器安装示意图：

![image-20230827144508487](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230827144508487.png)

##### 两个具有挑战性的场景：

- 工业区：工业区很小，狭窄的道路和两侧的墙壁会引起更多的雷达反射噪声。

- 上海交通大学校园：有更多的树和动态物体，因此雷达点云更加稀疏和不稳定。

  ![image-20230827145422005](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230827145422005.png)

# 四、实验

## 定量评估：

根据kitti评估方法。

计算长度 100 m 到 800 m 之间的平均平移和旋转误差，增量为 100 m。 

- 平移误差以百分比 (%) 表示
- 旋转误差以每米度数 (deg/m) 表示。
- 另外，也使用==绝对轨迹误差(absolute trajectory error，ATE)的均方根误差( root-mean-square error，RMSE)==用于评估==全局一致性==

##### 与最先进的雷达里程计/激光雷达SLAM（SC-LeGO-LOAM ）对比：

![image-20230827151547011](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230827151547011.png)

## 定性评估：

##### 大规模性能：

在校园内使用了长度超过9km的校园序列4，该序列穿过了一些无法使用GPS的隧道，ground truth是不连续和不可靠的，只用它来**定性**评估。

![image-20230827152205981](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230827152205981.png)

如图1所示，经过9km以上的长途行驶，雷达地图与卫星地图保持良好匹配，说明该框架在大规模环境中表现良好。

##### 室内性能：

在地下停车场的序列上测试了里程计和SLAM。由于GNSS不能在地下工作，我们选择SC-LeGo-LOAM作为ground truth。结果如图所示。

![image-20230827152551222](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230827152551222.png)
