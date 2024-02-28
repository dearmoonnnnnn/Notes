# 1、用户个数对结果的影响

### 表现：

![image-20210805155456828](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20210805155456828.png)

用户数越多。识别准确率越低（包括静态准确率和动态准确率）。

### 原因分析：

随着需要区分的目标数增大，目标特征之间的平均差异越小。

### 手势识别：

可以使用

# 2、不同服装的影响

### 表现：

![image-20210805160510423](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20210805160510423.png)

动态和静态准确率均略微有所下降。但动态情况下降得更少，这说明动态情况比静态情况具有更强的鲁棒性。

### 原因分析：

动态用户特征大部分依靠走路姿势。

相比之下，静态用户特征与身体引起的射频衰减相关性更大，这受到衣服的影响更大

总的来说，识别准确率在不同衣服的影响下，依然达到了92%以上。

### 手势识别：

戴手套？

# 3、不同的行走模式

### 实现：

主要通过三个方面实现不同的行走模式：

1. 不同速度
2. 不同行走轨迹
3. 携带不同的物体

### 表现：

<img src="https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20210805162604307.png" alt="image-20210805162604307" style="zoom:80%;" />

![image-20210805162647079](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20210805162647079.png)

除了快速行走，其他行走模式的平均识别率达到了95.7%.这说明该模型可以有效地提取动态用户特征，并且在不同的行走模式中具有很强的鲁棒性。

### 手势识别：

1. 手势速度
2. 不同的手势轨迹
3. 戴戒指、手表、手环等

# 4、不同环境的影响

### 实现：

1. 走廊入口（new）
2. 办公室入口（new）
3. 默认的实验环境

### 表现：

![image-20210805170232572](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20210805170232572.png)

在走廊和办公室的准确率比默认的实验环境低很多。	

### 原因分析：

提取到的特征具有环境依赖性。因此，在某个一环境中训练的模型，不能很好地工作在另一环境。

### 手势识别：

可以采用不同环境。

# 5、标签数量的影响

### 表现：

![image-20210805171544437](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20210805171544437.png)

随着标签数量的提升，识别准确率有所上升。

### 原因：

更多的标签能提供更多用户的**空间信息**。 

### 手势识别：

可以

# 6、阅读器天线和标签阵列的水平距离

### 实现：

现实中，入口的宽度并不是固定的。入口的一侧放置阅读器天线，另一侧放置标签阵列，入口的宽度决定了它们之间的距离。

水平距离0.8m - 2m ,步长为 0.4m

### 表现:

![image-20210805172407661](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20210805172407661.png)

这说明阅读器和标签的距离对准确率影响不大。距离提升时，准确率略微有所提升。

### 原因：

当距离上升时，静态信号强度比动态信号下降得更快。由于反射的原因，动态信号比静态信号弱很多很多。因此，当两种信号都降低，而静态信号强度降低更多时，两种信号的强度差异变小。因此，动态信号的变化可以引起复合信号更大的变化。

### 手势识别：

可以使用。

# 7、与其他模型的对比

### 实现：

与其他三种最先进的基于WIFI的识别技术对比

### 表现：

![image-20210805193944394](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20210805193944394.png)

​		可以看到RF-Identity表现得更好，同时，上述三种系统不能识别静态的人类目标。

​		同时，复现了WifiU系统，与RF-Identity对比。

![image-20210805195057475](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20210805195057475.png)

​		可以看到RF-Identity比WifiU具有很大的改进，并且用户数量越大，改进量越大。

### 手势识别：

可以与其他模型进行对比。