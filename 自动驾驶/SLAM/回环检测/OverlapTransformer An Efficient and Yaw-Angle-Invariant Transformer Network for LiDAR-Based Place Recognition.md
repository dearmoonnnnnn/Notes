# 零、Question

如何保证描述符的偏航角不变：

如何从原始激光雷达点云获得距离图像：

# 一、概念

##### 1、闭环检测：

指机器人识别曾到达某场景，使地图闭环的能力。

##### 2、偏航角：

实际航向与计划航向的夹角，向右偏为正。

##### 3、距离图像/深度图像：

range image/depth image，指将从图像采集器到场景中各点的距离(深度)作为像素值的图像。从它可以直接得到三维信息。

##### 4、描述符

##### 5、离散化误差

将连续值转换为离散值所带来的误差。

##### 6、置换不变，permutation invariant

https://zhuanlan.zhihu.com/p/617497002

指的是特征之间没有空间位置关系，即使输入的顺序发生变化也不会影响输出结果。

# 二、简介

**标题：**OverlapTransformer: An Efficient and
Yaw-Angle-Invariant Transformer Network for
LiDAR-Based Place Recognition

**必要性：**位置识别是slam中闭环或全局定位等任务的关键组件。

**结论：**与最先进的方法相比，该方法可以有效地检测环路闭合

**贡献：**

- 只使用激光雷达而不使用任何其他信息来检测SLAM的环路闭合候选，并在不进行微调的情况下很好地推广到不同的环境中。

- 提出一种新的新量化网络，利用激光雷达传感器的距离图像表示，实现每帧小于2ms的快速执行

- ==提出一种新的transformer网络，从激光雷达扫描中提取**偏航角不变的全局描述符**==，提高了位置识别的性能，而不是手工制作的描述符。

  <img src="https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230328120656544.png" alt="image-20230328120656544" style="zoom:50%;" />

  如图所示为作者提供的Haomo数据集中的反向回路，由于网络的偏航角不变设计，即使车辆朝不同方向行驶，作者的方法也可以识别位置。

- 提供了一个新的数据集，它包含移动机器人在不同时间重复位置记录的激光雷达序列，可以用来评估长期的位置识别性能。包含三个不同的挑战，长时间跨度的位置识别，反向驾驶和==不同外观场景==。提出方法在该数据集上实现了长期的位置识别。

**训练集：**部分KITTI数据集

**测试集：**KITTI和Ford Campus 数据集

# 三、related work

由于距离信息的高精度和照明不变性，基于激光雷达的位置识别在自动驾驶汽车领域引起了关注。。。。。。

随着深度学习的发展，越来越多的基于学习的方法被用于位置识别，分为两组：

- 基于局部特征的方法。通常有两步。
  1. 提取激光雷达扫描中的局部特征
  2. 基于提取的特征识别位置。
- 基于全局描述符的方法。两种生成全局描述符的方法：
  1. 使用不同类型（多级别）的局部特征生成全局描述符。
  2. 直接生成全局描述符。

最近，也有利用语义信息进行位置识别的工作。。。。。。与这些语义增强的方法相比，作者的方法只使用原始深度信息来实现在线性能，这使得该方法更容易推广到不同的环境和不同激光雷达传感器收集的数据集中。

# 四、方法的实现

pipeline

![image-20230328124828610](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230328124828610.png)

- Range Image Encoder：原始点云的距离图像被送到编码器中，该编码器是一个改进的==OverlapNetLeg==，可以从距离图像中提取特征。
- Transformer Module：编码后的特征量被送到transformer模块，该模块使用transformer来嵌入整个距离图像的**特征相对位置**和**全局信息**。
- Global Descriptor Generator：使用带有NetVLAD和多层感知器（MLP）的全局描述符生成器来压缩特征并生成一个偏航角不变的一维全局描述符。

## 4.1 距离图像编码器

输入：激光雷达的距离图像

输出：距离图像编码器输出的特征量F=RIE（R），大小为c×1×w

### 4.1.1 距离图像的生成：

一个点云P可以被映射为一个距离图像R，R中每个像素包含了一个3D点。每一个点p~i~=（x，y，z）可以被转化为图像左边（u，v）：

![image-20230328140908203](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230328140908203.png)

- r=||p||^2^,表示距离。

- f=f~up~+f~down~，是传感器的垂直视场。

- w,h为距离图像的宽度和高度。

### 4.1.2 对距离图像编码

使用 OverlapNetLeg编码器，相对于OverlapNet，==卷积滤波器只在垂直维度压缩距离图像，而不在宽度维度上压缩，以避免偏航角等变（yaw equivariance）的离散化误差==。此外，作者提出的架构中没有padding和dropout，以保持每个网络层生成的每个中间特征的yaw equivariance。

## 4.2 Transformer Module

受到NDT-transformer的启发，作者使用一个transformer来提取更独特的特征，用于激光雷达位置识别。如Fig.2的中间所示，transformer被分为三个部分：

- 多头部自我注意，multi-head self-attention，MHSA：
- 前馈网络，feed-forward network，FFN
- 层归一化，layer normalization，LN

与NDT-transformer不同，作者使用一个transformer块，以同时实现高精度和高效率。

MHSA可以学习由==自我注意力机制==捕获的特征之间的关系，MHSA提取的特征量A由如下公式得到：

![image-20230328152656463](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230328152656463.png)

- {Q，K，V}表示query，key和value，它们是由距离图像编码器生成的，沿着通道维度从特征量划分出来的。

A随后被送到FFN和LN来生成最终的特征量S：

![image-20230328153116159](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230328153116159.png)

- Conc(·) 表示跨通道串联技术。

==下面论证transformer 模块也是偏航角等变的。==

## 4.3 全局描述符生成器

### 4.3.1 NetVLAD

最开始提出的场景：以端到端的方式解决基于图像的位置识别问题

之后被用来解决基于激光雷达的位置识别问题，并被证明它具有**置换不变（permutation invariant）**的性质，因此很适合无序的点云。

然而，原始PointNetVLAD不能直接生成点云的偏航角不变全局描述符，因为点云的偏转旋转导致点坐标和特征的不同输入。这使得PointNetVLAD对传感器姿态的旋转或噪声的鲁棒性降低

### 4.3.2 利用置换不变性和偏航角等变性质实现偏航角不变性

在作者的工作中，同时利用了NetVLAD的置换不变性质和他们提取的特征量的偏航角等变性质，实现了偏航角不变的性质，并且生成了一个偏航角不变的描述符。如图Fig.3<img src="https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230328165931733.png" alt="image-20230328165931733" style="zoom: 67%;" />

- 点云的偏航旋转对应于距离图像沿宽度维度的水平偏移。
- 假设第一行是原始激光雷达对应的距离图像，偏航旋转0°，第二行和第三行旋转了90°和180°，它们对应的距离图像偏移¼w和½w。
- OverlapNetLeg和transformer模块的输出也偏移了相同量。
- 生成的全局描述符都是没有偏移的，也就是偏航角不变的。
- 在在线操作中，使用偏航角不变的全局描述符表示激光雷达扫描，并使用两个描述符之间的欧几里得距离来找到最近的参考位置。

## 4.4 网络训练

使用具有计算重叠标签的三元组损失来更好地区分阳性和阴性训练示例。

。。。。。。

# 五、Haomo 数据集

![image-20230329100357348](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230329100357348.png)

动机: 现有的数据集没有用于长期大规模自动驾驶位置识别的重复反向路径。

目前，该数据集中有五个序列，如表一所示

- 序列1-1和1-2从同一路线收集，行驶方向相反。
- ==来自相同路径的附加序列1-3分别用于1-1和1-2的在线查询，以评估正向和反向驾驶的位置识别性能。==
- 序列2-1和2-2沿着序列1-1同一方向的更长路线收集，但它们的日期不同。2-1用作数据库，2-2用作==查询==。这两个序列用于评估大规模长期位置识别的性能。

# 六、实验评估

## 6.1 闭环检测的评估

如果两次扫描的重叠值（Overlap）大于0.3，我们将其视为循环闭合。

![image-20230329104703134](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230329104703134.png)

评价指标用了AUC，F1max，Recall@1，Recall@1%

- F1:精确率和召回率的调和平均数
  $$
  F1=2*\frac{precision*recall}{precision+recall}
  $$

- Recall召回率：也叫查全率，找到实际为正的样本中多少被预测为正
  $$
  R=\frac{TP}{TP+FN}
  $$

- Recall@1:正确检索结果出现在检索结果第一位的比率

结论：

- 在KITTI上，作者的方法优于所有基线方法。
- 网络的训练只在KITTI上的部分数据序列上进行，但是没有任何微调也能在Ford Campus数据集上优于其他方法，只有recall@1%与 Scan Context 方法不相上下，说明模型有很好的泛化能力。

## 6.2 用haomo数据集进行的实验

序列1-2作为数据库，1-3作为查询，对应挑战是短期同向挑战。

![image-20230329113330848](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230329113330848.png)

# 七、代码复现


