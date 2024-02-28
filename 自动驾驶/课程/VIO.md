# 1、概念

##### 1、VIO

Visual-Inertial Odometry，视觉与IMU融合实现里程计

##### 2、IMU

Inertial Measurement Unit，惯性测量单元（陀螺仪+加速度计）

- 典型6轴IMU以较高频率（≥100Hz）返回被测量物体的**角速度**与**加速度**。
- 受自身温度、零偏、振动等因素干扰，积分得到的平移和旋转容易漂移。

##### 3、视觉

Visual Odometry，以图像形式记录数据，频率较低（15-60Hz居多）。通过图像特征点或像素推断相机运动。



# 2、简介![image-20230522112149987](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230522112149987.png)

- IMU适合计算短时间、快速的运动。
- 视觉适合计算长时间、慢速的运动。

## 2.1 IMU的误差

确定性误差：

- 可以事先标定确定，包括bias，scale ...

  - bias。理论上，当没有外部作用时，IMU传感器的输出应该是0。但是，实际数据存在一个偏置b。

  - scale。可以看成是实际数值和传感器输出值之间的比值。![image-20230523170304569](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230523170304569.png)

  - Nonorthogonality/Misalignment Errors

    多轴IMU传感器制作的适合，由于制作工艺的问题，会使得xyz轴可能不垂直。

  - 其他确定性误差eg：run to tun bias/scale factor 

随机误差：

- 通常假设噪声服从高斯分布，包括高斯白噪声，bias随机游走
  - 高斯白噪声，IMU数据连续时间上受到一个均值为0，方差为σ，各时刻之间相互独立的高斯过程。
    - 实际上，IMU传感器获取的数据为离散采样，离散和连续高斯白噪声的方差之间存在转换关系，相差$\frac{1}{\sqrt{Δt}}$

### 2.1.1 标定的方法

六面标定法

