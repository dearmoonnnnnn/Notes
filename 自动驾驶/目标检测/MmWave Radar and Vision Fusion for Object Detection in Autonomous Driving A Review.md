MmWave Radar and Vision Fusion for Object Detection in Autonomous Driving: A Review

# 一、摘要

自动驾驶正处于蓬勃发展的阶段，复杂场景下的精确目标检测是保证自动驾驶安全的重要手段。毫米波(mmWave)雷达与视觉融合是精确障碍物检测的主流解决方案。本文详细介绍了毫米波雷达和基于视觉融合的障碍物检测方法。首先，介绍了自动驾驶目标检测的任务、评价标准和数据集。然后将毫米波雷达与视觉融合的过程分为传感器部署、传感器标定和传感器融合三个部分，对这三个部分进行了综合评述。

具体来说，我们将融合方法分为数据级、决策级和特征级融合方法。此外，我们还介绍了三维目标检测、激光雷达与视觉在自动驾驶中的融合以及多模态信息融合等具有发展前景的技术。最后，对本文进行总结。

# 二、基于毫米波雷达和视觉融合的目标检测过程

![image-20221104145044613](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221104145044613.png)

1. 毫米波雷达与视觉融合的过程包括传感器选择、传感器标定和传感器融合三个部分。

2. 为了实现毫米波雷达目标检测和视觉融合的预期性能，需要解决以下挑战。

   - 时空标定:融合的前提是在同一时空，这意味着需要对毫米波雷达和视觉信息进行标定。

   - 信息融合:融合不同传感器的传感信息以实现最佳性能的目标检测算法是必不可少的。

   为了解决上述挑战，首先要**分析不同传感器的特性**，选择合适的传感器。其次，需要对传感器标定进行研究，包括不同传感器之间的**坐标变换**、**无效传感信息的滤波**和**误差校准**。最后，需要研究**感知信息融合**(数据级，决策级，特征级)，通过不同传感器的互补来实现感知能力的提高，这涉及到毫米波雷达和视觉融合方案。

# 三、目标检测的任务、评价标准和公共数据集

## 3.1 任务

二维目标检测任务：二维边界框，分类+定位（图像中目标的定位而不是现实中目标相对于车的位置）

三维目标检测任务：三维边界框，分类+定位（定位不仅是选定的目标在图像中的定位，还决定了现实世界中目标的姿态和位置）

## 3.2 评价标准

当样本分布极不平衡的情况下，准确率不能很好地反应模型的性能。

一般来说mAP针对整个数据集而言的；AP针对数据集中某一个类别而言的；而percision和recall针对单张图片某一类别的

### 3.2.1 R 召回率

![在这里插入图片描述](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/20191023211437116.png)召回率：预测的真正样本占实际正样本总数的比值

### 3.2.2 P 精确率

![在这里插入图片描述](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/20191023211940880.png)精确率：预测的真正样本占预测的所有正样本的比例

### 3.2.3 AP 平均精度

![image-20221104160708600](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221104160708600.png)

![image-20221104160553920](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221104160553920.png)

![image-20221104161212699](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221104161212699.png)

==平均平均精度（mean AP）==：平均平均精度(mean average precision, mAP)表示检测模型的综合结果，可通过计算各类AP的平均值得到

## 3.3 数据集

![image-20221104162832729](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221104162832729.png)

KITTI：基本上传统的自动驾驶任务都能用这个数据集做，包括2D和3D目标检测，slam等。

nuScense：用于语义理解和物体检测跟踪较多。目前最大的数据集，唯一一个包含普通雷达数据的数据集。6个摄像头、5个雷达、1个激光雷达。它提供的3D边框标注不仅包含23个类，还包含行人姿势、车辆状态等8个属性。

![image-20221106140642005](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221106140642005.png)

# 四、传感器部署

## 4.1 传感器配置

![image-20221105103011443](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221105103011443.png)

- 综上所述，采用雷达与视觉融合的传感解决方案是当前自动驾驶汽车障碍物检测领域的主流趋势。原因是雷达和相机具有互补的特性。

## 4.2 传感器选择

![image-20221105103141164](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221105103141164.png)

### 4.2.1 激光雷达和毫米波雷达的比较

- 毫米波：1、探测距离远（250m，激光雷达150m）。2、成本低。 3、可应对恶劣天气。4、可探测动态目标（利用多普勒效应探测车速，精确度可达0.1m/s）
- 激光雷达：1、相比于毫米波雷达具有更高的角度分辨率和探测精度。2、测量数据包含更多语义信息，满足先进自动驾驶的感知需求。3、激光的传输受杂波影响较小，而毫米波雷达不能完全过滤杂波，导致雷达信号处理错误。

### 4.2.2 雷达和相机的比较

- 雷达：优：1、是探测**距离**和**径向速度**的最佳传感器。2、全天都能使用，特别是夜间仍能正常工作。缺：无法区分颜色，对目标分类能力差。
- 相机：优：具有良好的色彩感和分类能力，同时它的角度分辨能力很强。缺：测速和测距能力一般，图像处理需要的计算资源大。

# 五、传感器标定

原因：由于不同传感器的空间位置和采样频率的差异，不同传感器对同一目标的传感信息可能不匹配。

三个方面：包含坐标标定（coordinate calibration），雷达点过滤（radar point filtering），误差校准（error calibration）

![image-20221105113322131](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221105113322131.png)

- 以nuScenes数据集的摄像机和毫米波雷达数据作为例子。数据集本身已经帧同步，不需要进行时间同步。该图是经过空间坐标变换得到。（==有无经过雷达点过滤==）

## 5.1 坐标标定

目的：将雷达点与图像中的目标进行匹配。

最常用的三种方法：坐标变换法，传感器校验法、基于视觉的方法

- 坐标变换法：通过矩阵运算将雷达信息和视觉信息统一在一个坐标系下。

  ![image-20221106153937056](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221106153937056.png)

  - 文献[46]通过毫米波雷达和相机空间位置的坐标来求得变换矩阵，使用线程同步方法解决传感器采样率不一致导致的时间不一致问题。
  - [文献[45]](https://blog.csdn.net/weixin_43253464/article/details/120875285)使用基于==伪逆的点对齐方法获得变换矩阵==，利用了最小二乘法
  - ==文献[53]，在不使用特殊工具和雷达反射强度的情况下，将真实坐标投影到雷达探测图中，减少了对标定误差的依赖==。

- 传感器校验法

  - 利用同一物体上不同传感器信息对多个物体进行校验。


## ==5.2 雷达点过滤==

## ==5.3 误差校准==

由于使用的是开源数据集，不需要误差校准。若是自制的数据集，一定要经过雷达过滤和误差校准。

# 六、传感器融合

以车辆检测为背景，使用毫米波雷达和视觉的融合。

三个级别：数据级、决策级、特征级

- 数据级融合将传感器探测到的数据进行融合，数据丢失最小，可靠性最高
- 决策级融合将不同传感器的探测结果融合
- 特征级融合提取雷达特征信息，然后与图像特征进行融合

![image-20221105130402275](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221105130402275.png)

## 6.1 数据级融合

目前不是主流的研究方向

![image-20221105185339798](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221105185339798.png)![image-20221105185339798](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221105190045516.png)

- 利用雷达点生成感兴趣的区域(ROI,region of interest)
- 然后利用特征提取器和分类器对图像进行识别

优：缩小了目标检测的搜索空间，节省了计算资源。

缺点：有效雷达点的数量直接影响检测结果。没有有效点的图像部分被忽略，存在安全隐患。

## 6.2 决策级的融合

**目前主流的融合方案**

**两个部分：**

1. 传感器信息处理
2. 决策融合
   - 基于贝叶斯理论：
     - 贝叶斯规划，利用概率推理方法解决多传感器融合问题。
     - 基于贝叶斯网络的动态融合方法，提高各个融合算法的可重用性。
   - 基于卡尔曼滤波
   - ==基于证据理论==



![image-20221105191207970](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221105191207970.png)<img src="https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221105191842424.png" alt="image-20221105191842424" style="zoom:80%;" />

优点：兼顾视觉传感器和雷达的优势。

## 6.3 特征级融合

![image-20221106111438274](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221106111438274.png)

- 特征提取
  - 视觉特征：Faster-RCNN、YOLO、SSD、RetinaNet
  - 雷达特征：将雷达信息转化为类似图像的矩阵信息
- 特征融合
  - 拼接法：==将雷达特征矩阵和图像特征矩阵连接成一个多通道矩阵==（79两种都包含）
  - 元素相加法：将两个矩阵合并成一个矩阵
  - 在[79]中设置了拼接和元素相加两种融合方法，实验结果表明，两种融合方法都提高了检测性能。基于元素的添加方法在手动标记的测试集上执行得更好，而连接方法在生成的测试集上执行得更好。参考文献。[80]、[81]均采用串联法。在[82]中，提出了一种新的传感器特征融合块——空间注意力融合(space attention fusion, SAF)。利用SAF块生成注意权重矩阵，融合雷达和视觉特征。同时，[82]将SAF方法与基于元素的加法、乘法和拼接三种方法进行了比较，结果表明SAF的性能最好。此外，[82]对更快的R-CNN进行了泛化实验，SAF模型也提高了检测性能。

![image-20221106112035276](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221106112035276.png)为什么
