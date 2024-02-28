论文：

CamLiFlow: Bidirectional Camera-LiDAR Fusion for Joint Optical Flow and
Scene Flow Estimation

(CamLiRAFT)Learning Optical Flow and Scene Flow with Bidirectional Camera-LiDAR Fusion

# 零、概述

## 1、相关概念

##### 1、[**光流**](https://zhuanlan.zhihu.com/p/44859953)：

光流(Optical flow or optic flow)是关于视域中的物体运动检测中的概念。它是空间运动物体在**观察成像平面**上的像素运动的瞬时速度<!--如果观察者目标做相同的运动，则两者相对静止，没有光流运动-->。用来描述相对于观察者的运动所造成的观测目标、表面或边缘的运动。<!--通俗的讲就是一个图片序列的每张图像中每个像素的运动速度和运动方向找出来-->

##### 2、场景流：

scene flow ，空间中场景的三维运动场，即空间中每一点的位置信息和其相对于摄像头的移动；场景流估计的一种方式是光流估计和深度估计的结合。

##### 3、特征金字塔：

用于在不同尺度上检测图像目标的计算机视觉算法。它通过将输入图像通过多次下采样得到不同尺度的图像，然后在这些图像上构建金字塔状的特征图，使得不同尺度上的目标都能有效地检测到。

##### 4、[cost volume](https://blog.csdn.net/longshaonihaoa/article/details/124726727)：

用于存储像素与其相关联的下一帧的对应像素的数据匹配成本。

##### 5、MLP：

https://zhuanlan.zhihu.com/p/63184325

Muti-Layer-Perceptron，多层感知器。

##### 6、Global Average Pooling

该概念出自于network in network。主要用来解决全连接的问题。主要思想是将最后一层的每一个特征图进行均值池化，每个特征图形成一个特征点，最后将这些特征点组合成特征向量，便于后续在softmax中进行计算。

eg：最后一层的数据是10\*6\*6的特征图，global average pooling**在每一张特征图计算所有像素点的均值**，输出一个特征点，10个特征图就会输出10个数据点，将这些数据点组成一个1*10的向量(即特征向量)，后续就可以输入到softmax的分类中计算。

##### 7、EPE

endpoint error，光流估计的标准误差测量方法。它是预测的光流向量和ground truth之间的欧式距离，在所有像素上平均超。

##### 8、[可视化方法](https://blog.csdn.net/tywwwww/article/details/126125681?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~Rate-1-126125681-blog-125721136.235^v35^pc_relevant_increate_t0_download_v2&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~Rate-1-126125681-blog-125721136.235^v35^pc_relevant_increate_t0_download_v2&utm_relevant_index=1)

![image-20230513155315085](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230513155315085.png)

用色相表示角度，饱和度表示模长。

![在这里插入图片描述](https://img-blog.csdnimg.cn/9f304efd7e1c4cdc8dcdde91e2e537b7.png)

## 2、问题

##### 1、本文的融合方法的创新点：

之前的融合方法是单阶段的，本文的融合方法为多阶段，双向的融合

##### 2、光流和场景流对于slam的帮助。

##### 3、三个挑战是否在slam的融合中也存在

##### 4、多任务损失的作用，为什么用了多任务损失更好。

##### 5、原始PWC框架长什么样

##### 6、融合模块中，1*1的卷积有什么好处。

##### 7、CamLiFlow和CamLiRAFT的区别：

感知融合插值的方法不一样。

CamLiRAFT使用了梯度分离，用来解决两个分支的损失和梯度尺寸不一致的问题。

##### 8、融合模块插入的位置该如何选择

# 一、引言

研究的问题：从同步的2D和3D数据中联合估计光流和场景流。

本文工作：之前的方法往往使用early-fusion或者late-fusion这种单阶段的融合设计。然而单阶段融合不能充分利用每个模态的特征，也不能充分做到模态间的互补。本文提出一种**多阶段**的**双向融合**框架，用更少的参数实现了更好的性能。

![image-20230412113556602](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230412113556602.png)

##### 对图像和点云进行融合会遇到的三个挑战和解决方案：

- 针对图像特征和点特征的数据结构不匹配问题，提出一个可学习的**双向融合模块Bi-CLFM**，它可以融合2D到3D、3D到2D两个方向的特征。
- ==其次是两个分支性能不匹配的问题==，因为采用现有的光流方法(比如RAFT)构建2D分支很容易，但想让3D分支的结构和性能与2D分支相当却较为困难。因此作者重新构建了一个和==RAFT结构==一样的3D分支，并设计了基于point的correlation pytamid，它能同时捕捉samll motion 和large motion
- ==第三是两个分支的梯度尺寸不匹配的问题，如果直接进行融合则会导致训练不稳定，还会出现一种模态主导训练的情况。因此作者设置了将来自另一个模态的梯度截断，让每个模态专注于优化自身。==

融合效果：

作者在==RAFT和PWC两种架构上==实现了提出的融合方案，并取名CamLiRAFT和CamLiPWC。

- 在Flying Things 3D数据集上，CamLiPWC和CamLiRAFT显著优于之前的所有方法；相比于RAFT-3D，作者的==EPE3D误差==减少了48%，并且只用了1/5的参数量。
- 在KITTI排行榜中，之前的SOTA方法都重度依赖==刚体假设==，而**CamLiRAFT在没有利用任何刚体假设的情况下与之前的SOTA方法RIgidMask不想上下（SF-all：4.97% vs 4.89%）；在用上刚体假设之后，作者的方法进一步刷到了4.26%，在所有提交中排行第一**
- 另外，也在Sintel数据集上进行了实验：在没有针对Sintel进行==finetune==的情况下，CamLiRAFT在Sintel的training set的final pass上取得了2.38AEPE的误差，相比于RAFT和RAFT-3D分别降低了12%和18%，这证明了**该论文的方法不仅拥有良好的泛化性，而且可以处理non-rigid运动。**
- CamLiPWC-L和CamLiRAFT-L在LiDAR-only的单模态设定下也大幅超过了之前的方法，在未来的研究生中可以作为一个强大的baseline。

# 二、融合模块

CamLiFlow联合估计相机帧的**密集光流**和激光雷达框架的**稀疏场景流**。

## 2.1 CamLiFlow中的相机激光雷达融合模块

<img src="https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230413133754975.png" alt="image-20230413133754975"  />

- 三个输入
  - 图像特征$F∈R^{H×W×C_{2D}}$
  - 点云特征$G=\{g_i|i=1,...,N\}∈R^{N×C_{3D}}$
  - 点云位置$P=\{p_i|i=1,...N\}∈R^{N×3}$

- 多模态特征的对齐

  - 由于图像特征是稠密的，点特征是稀疏的，我们需要在融合之前将两个模态的特征对齐。

  - 图像特征需要下采样，从而变得稀疏。

  - 点特征需要上采样，从而变得稠密。作者提出一种名为**感知融合插值**的方法解决该问题。

    ![image-20230413144919483](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230413144919483.png)

    - 对于稠密图中的每一个目标像素q，我们在图像平面上的投影点中找到与q最近的**k个投影点**（邻居）。

    - 计算q与x个邻居的坐标偏移量x~i~-q，==2D相似度$S(q,x_i)=F(q)·F(x_i)$。==

    - 将**坐标偏移量**，**2D相似度**和**3D特征g~i~**拼接，进行==MLP==后取平均MEAN，公式化表达如下：
      $$
      D(q)=\frac{1}{k}\sum_{x_i∈N_q}MLP([x_i-q,S(q,x_i),g_i])
      $$


- 2D⇨3D

  - 首先，**将点云投影到图像平面**（表示为$X=\{x_i|i=1,...,N\}∈R^{N×2}$）来检索对应的2D特征：
    $$
    H=\{F(x_i)|i=1,...,N\}∈R^{N×C_{2D}}
    $$
    其中F(x)表示x处的图像特征，并且如果坐标不是整数，可以通过**双线性插值**来检索

  - 其次，将检索到的特征H和输入的3D特征G连接起来。<!--变成两个通道-->

  - 最后，==使用一个1*1的卷积来减少融合后的3D特征的维度==。<!--重新变成一个通道-->

- 3D⇨2D

  - 相似的，**点云先被投影到图像平面**（表示为$X=\{x_i|i=1,...,N\}∈R^{N×2}$）
  - 由于点云是稀疏的，作者提出了**融合感知插值**，以从稀疏的3D特征创建密集特征图$D∈R^{H×W×C_{3D}}$
  - 接下来，将“插值”点云特征与输入的图像特征连接起来。
  - 最后使用一个1*1卷积减少融合后的特征维度。

## 2.2 CamLiRAFT中的相机激光雷达融合模块

![image-20230426122628508](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230426122628508.png)

- 三个输入（与CamLiFlow相同）
- 2个分支
  - 3D->2D
    - 点云先被投影到图像平面。

    - 使用感知融合插值，从稀疏的3D特征创建密集的特征。

    - channel alignment：使用一个1*1的卷积。

    - 提出了基于信道注意力的自适应融合方法。（基于selective Kernel Networks）

  - 2D->3D
    - 点云先被投影到图像平面。


- 多模态特征的对齐

  - 图像特征下采样

  - 点特征上采样，感知融合插值，与CamLiFlow相比有改进

    ![image-20230426130223898](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230426130223898.png)

    - 对于每个像素，我们找到与它最近的k个投影点。
    - 使用ScoreNet（轻量级的MLP接一个sigmoid激活，给出0~1范围内的分数），根据坐标偏移量计算每个投影点的权重。
    - 对k个投影点的特征加权，并通过最大池化层聚合。（==为什么要用池化层聚合==）
    - 相对于CamLiFlow的改进：1）不再使用重型MLP处理相邻偏移量和特征的连接，而是使用轻量级的ScoreNet来生成相邻特征的权值；2）删除了q和它的邻居之间的二维相似性度量，因为作者发现它不再提高性能。

- 两个具有相同架构设计的选择性融合模块，一个用于稠密特征融合(3D->2D),一个用于稀疏特征融合(2D->3D)。（==具体实现==）

- 梯度分离（==论文未说明具体实现==）：

  - 解决的问题：多模态融合中梯度的尺度不匹配，从而使训练不稳定且由一种模态主导。

  - 如图所示，两个分支的损失和梯度尺度差别很大，直接用传统的方法融合，比如元素值相加或者拼接成多通道可能会遇到尺度不匹配的梯度。![image-20230426152059762](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230426152059762.png)

  - 图示为梯度分离的烧灼实验，若没有梯度分离，2D分支占主导地位,损害3D分支的性能。![image-20230426154556107](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230426154556107.png)

# 三、模型架构

## 3.1 CamLiFlow（CamLiPWC）

![image-20230413130149911](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230413130149911.png)

使用Bi-CLFM构建了一个多级双向融合管道。主干基于PWC架构，该架构由多个阶段组成：

- feature extraction
- warping
- cost volume
- flow estimation

在每个阶段，使用模态的特定架构在单独的分支中学习这两种模态。在每个阶段结束时，Bi-CLFM连接两个分支，传递互补信息。

### 3.1.1 特征金字塔

给定一对图像和点云，我们分别为图像分支和点分支生成特征金字塔。==对于每个级别l，使用残差块以因子2对图像特征进行下采样==，==同时,使用**最远点采样**以相同因子对点进行下采样，然后使用pointConv进行聚合。==

==图像金字塔对纹理信息进行编码，而点金字塔对几何信息进行编码。==因此，特征通过BiCLFM在多个级别上进行融合以实现互补。

### 3.1.2 Warping

在每个金字塔级别l，==使用来自较低级别的上采样流，图像特征和点云都朝着参考帧扭曲==。由于扭曲层不引入任何可学习的参数，我们在此阶段不进行特征融合。

### 3.1.3 Cost Volume

该模块存储参考帧和扭曲目标帧（warped target frame）之间的匹配成本。图像分支：参考论文[45],通过将搜索范围限制在每个像素周围的4个像素来构建部分cost volume。点分支：参照[54]构建一个可学习的成本-体积层。基于像素的2D成本体积保持固定的领域范围，基于点的3D成本体积搜索动态范围。

将两个成本体积与Bi-CLFM相融合。

### 3.4 流量估计器

我们为每个分支建立一个流量估计器。流量估计器的输入包括成本体积、参考系的特征和上采样流量。光流估计器参考[45],场景流估计器参考[54]。来自两个估计器的倒数第二层的特征被融合，为了清楚起见，将图三中的最后一层称为“流量估计器”，将其他层称为“流量计解码器”。

## 3.2 CamLiRAFT

![image-20230426165643874](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230426165643874.png)

## 2.4 反向深度缩放（IDS）

使点云分布更加均匀

## 2.5 多任务损失

虽然光流和场景流的估计是高度相关的，但我们将它们公式化为不同的任务。

- 光流损失:$L_{2D}$
- 场景流$L_{3D}$
- 最终的损失：L=$L_{2D}+λL_{3D}$，在本文的所有实验中，λ=1

# 三、实验

