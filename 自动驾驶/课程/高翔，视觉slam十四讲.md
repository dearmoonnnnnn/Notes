# Question

##### 1、多传感器融合的slam一般使用哪些相机？多传感器融合时谜一般会使用激光雷达，那么是否可以简单地选择单目相机

# 第二讲

我们希望一个机器人拥有自主运动能力，那么它至少需要知道两件事：

1. 我在什么地方？——定位
   - 定位需要的传感器可以分为两类：携带于机器人本体上的，安装与环境中的(导轨、二维码等)
   - 第二类传感器约束了环境，而SLAM强调未知环境，因此第一类更适合用于SLAM。
2. 周围环境是什么样？——建图

## 相机

相机的种类：单目相机、双目相机、深度相机

相机的本质：用二维平面表达三维世界，这个过程中丢失了深度信息。



如何获取深度信息

- 单目：本身没有深度，必须通过**移动相机**产生深度 Moving View Stereo
  - 场景和成像有几何关系
  - **近处**物体的像运动**快**
  - **远处**物体的像运动**慢**

- 双目：通过**视差**计算深度 Stereo
  - 左右眼的微小差异判断远近
  - 同样的，远处物体变化小，近处物体变化大——推算距离**计算量非常大**

- RGBD：通过**物理方法测量**深度
  - 物理手段测量深度
  - 结构光ToF
  - 主动测量，功耗大
  - 深度值较准确
  - 量程较小，易受干扰



## 视觉slam框架

<img src="https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230507155008307.png" alt="image-20230507155008307" style="zoom:67%;" />

- 前端：VO，视觉里程计。通过这个时刻图像和下个时刻图像之间的关系，估计运动，再通过下个时刻和下下个时刻图像之间的关系估计运动，即通过估计局部的运动，叠加得到最终的运动轨迹。缺陷：存在偏移，有累计误差。
  - 特征点法
  - 直接法
- 后端：Optimization。从带有噪声的数据中优化轨迹和地图状态估计问题。
  - 滤波器法,EKF
  - 图优化方法
- 回环检测 Loop Closing。检测机器人是否回到早先位置；识别到达过的场景；计算图像间的相似性
  - 
- 建图 Mapping。用于导航、规划、通讯、可视化、交互等
  - 度量地图vs拓扑地图
  - 稀疏地图vs稠密地图

## slam问题的数学描述

离散时间：t=1,2,...,k

机器人的位置：x~1~,x~2~,...,x~k~

**运动方程：$X_k=f(x_{k-1},u_k,w_k)$**,不一定存在，比如纯视觉中没有用来存放运动的数据。

- x~k-1~：上一时刻
- u~k~：输入
- w~k~：噪声

路标（三维空间点）：y~1~,y~2~,...,y~n~，用于表示环境中存在的东西

传感器在位置x~k~处，探测到了路标y~j~

**观测方程：z~k,j~ = h( x~k~, y~j~, v~k,j~ )**



slam过程使用运动方程和观测方程描述



考虑的问题：

- 位置x~k~是三维的，如何表述
- 观测是相机中的像素点，如何表述
- 已知u，z时，如何推断x，y



1. 

# 第三讲 三维空间刚体运动

a、b向量的外积

![image-20230508085639462](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230508085639462.png)



问题：

1. 坐标系之间是如何变化的
2. 如何计算同一个向量在不同坐标系里的坐标
3. 在slam中
   - 两个坐标系：固定的世界坐标系和移动的机器人坐标系
   - 机器人坐标系随着机器人运动而改变，每个时刻都有新的坐标系



## 1、刚体运动=平移+旋转： $a'=Ra+t$, （R表示旋转，t表示平移）

- 平移：用向量表示
- 旋转：旋转矩阵
  - 设某坐标系(e~1~, e~2~, e~3~ )发生了一次旋转，变成了$(e_1',e_2',e_3')$
  - 对于某个固定的向量ɑ(向量不随坐标系旋转)，它的坐标怎么变化？
  - 坐标关系：$\begin{bmatrix}e_1,e_2,e_3\end{bmatrix} \begin{bmatrix}a_1\\a_2\\a_3\end{bmatrix} = \begin{bmatrix}e_1',e_2',e_3'\end{bmatrix} \begin{bmatrix}a_1'\\a_2'\\a_3'\end{bmatrix} $
  - 左乘$\begin{bmatrix}
    e_1^T \\
    e_2^T \\
    e_3^T
    \end{bmatrix}$,得$\begin{bmatrix} a_1\\ a_2\\ a_3 \end{bmatrix} = \begin{bmatrix}e_1^T e_1' & e_1^T e_2' & e_1^T e_3' \\ 
                   e_2^T e_1' & e_2^T e_2' & e_2^T e_3' \\
                   e_3^T e_1' & e_3^T e_2' & e_3^T e_3' \\               \end{bmatrix} 
    \begin{bmatrix} a_1'\\
                    a_2'\\
                    a_3'
    \end{bmatrix} 
    \stackrel{\mathrm{△}}{=} R a'$
  - R就是旋转矩阵，且满足两个性质。（反之，满足这两个性质的矩阵称为旋转矩阵）
    - R是一个正交矩阵
    - R的行列是为1
  - 旋转矩阵描述了两个坐标的变换关系
    - $a_1=R_{12}a_2$
    - $a_2=R_{21}a_1$
    - 于是$R_{21}=R_{12}^{-1}=R_{12}^T$
    - 进一步 $a_3=R_{32}a_2=R_{32}R_{21}a_1=R_{31}a_1$

## 2、 齐次坐标与变换矩阵

问题描述：

- 若发生两次变换：$b = R_1a+t_1,c = R_2b+t_2$
- 这时$c=R_2(R_1a+t_1)+t_2$, 叠加起来过于复杂<!--如果只有旋转变换，多次变换是直接矩阵相乘，但多了平移就比较麻烦-->



解决方法：**使用变换矩阵**

- 旋转+平移的变换方程改写为：$\begin{bmatrix}a' \\ 1\end{bmatrix} = \begin{bmatrix}R & t\\ 0^T & 1 \end{bmatrix} \begin{bmatrix}a \\1\end{bmatrix} \stackrel{\mathrm{△}}{=} T \begin{bmatrix}a \\ 1 \end{bmatrix}$，T∈R^4*4^

- 记 $\widetilde{a}= \begin{bmatrix}a \\1 \end{bmatrix}$

- 多次变换就可以写成:
  $$
  \widetilde{b}=T_1\widetilde{a},\widetilde{c}=T_2 \widetilde{b} \space \Rightarrow \widetilde{c}=T_2T_1\widetilde{a}
  $$

- 这种用四个数表达三维向量的做法称为**齐次坐标**





具体例子：

- 在SLAM中，通常定义世界坐标系T~w~与机器人坐标系T~R~
- 一个点的世界坐标为p~W~，机器人坐标系下为P~R~，那么满足关系$P_R=T_{RW}P_W$
- 反之亦然
- 在实际编程中，可以使用T~RW~或者T~WR~来描述机器人的位姿。



实践：

Elgen库

略

## 3、旋转向量(Rotation Vector)和欧拉角

##### 旋转矩阵的缺点

1. SO(3)的旋转矩阵有9个量，但一次旋转只有3个自由度。因此旋转矩阵是冗余的。同理，变换矩阵用了16个量表达6自由度的变换。
2. 旋转矩阵自身带有约束：它必须是正交矩阵，且行列式为1。变换矩阵同理。估计或者优化旋转矩阵或者变换矩阵时，这些约束会使得求解变得更困难。

##### 旋转向量(或角轴，Axis-Angle)

- 旋转=旋转轴+旋转角

  - 因此可以使用一个向量，其方向与旋转轴一致，长度等于旋转角。即**旋转向量**，变量维数为3维。

  ![image-20231010163453382](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20231010163453382.png)

- 变换矩阵=旋转向量+平移向量

  - 变量维数正好是6维。

- ##### 旋转向量的缺点

  - 对人类来说不直观。

##### 欧拉角

- 非常直观

- 把一个旋转分解为3此绕不同轴的旋转。如XYZ(先绕X轴，再绕Y轴，最后绕Z轴旋转)，ZYX等等。

- 缺点

  - 会出现万向锁的情况，丢失维度信息。

    下图为ZYX转角方式，若第二次旋转为90°，第三次旋转与第一次旋转使用同一个轴。这被称为**奇异性问题**。<!--旋转向量也具有奇异性问题-->

    ![image-20231010170400767](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20231010170400767.png)

    

## 4、四元数

##### 动机：

- **旋转矩阵**用9个量描述3自由度的旋转，具有冗余性。
- **欧拉角**和**旋转向量**是紧凑的，但是具有奇异性。
- 借助复数的知识，复数的乘法可以表示复平面上的旋转：例如，乘上复数i相当于逆时针把一个复向量旋转90°。当我们要将复平面的向量旋转θ角时，可以把这个复向量乘以$e^{iθ}$
  - 二维情况下，旋转由**单位复数**描述。
  - 三维旋转则由**单位四元数**描述。

##### 定义：

一个四元数拥一个实部和三个虚部。
$$
\bold{q}=q_0+q_1i+q_2j+q_3k
$$
三个虚部满足条件
$$
\left\{
\begin{align}
i^2 & = j^2 = k^2 = -1 \\
ij & = k,ji = -k 		 \\
jk & = i,kj = -i		 \\
ki & = j,ik = -j
\end{align}
\right.
$$
也可用一个标量+一个向量来表示：
$$
\bold{q}= [s, \bold{v}]^T, s= q_0 ∈\mathbb{R}, \bold{v} = [q_1, q_2, q_3]^T ∈ \mathbb{R}^3
$$

- $s$称为四元数的实部，而$v$称为虚部。
- 如果一个四元数的虚部为0，则称为实四元数。若其实部为0，则称为虚四元数。

##### 相关运算

设
$$
\left\{
\begin{align}
\bold{q}_a & = s_a + x_ai + y_aj + z_ak\\
\bold{q}_b & = s_b + x_bi + y_bj + z_bk
\end{align}
\right.
$$
也可表示为
$$
\left\{
\begin{align}
\bold{q}_a & = [s_a, \bold{v}_a ]^T\\
\bold{q}_b & = [s_b, \bold{v}_b ]^T
\end{align}
\right.
$$

- 加法
  $$
  \bold{q}_a ± \bold{q}_b = [s_a ± s_b, \bold{v}_a ± \bold{v}_b]^T
  $$

- 乘法

  - 把$\bold{q}_a$和$\bold{q}_b$每一项相乘，最后相加，根据虚部满足的条件，整理得
    $$
    \begin{align}
    \bold{q}_a \bold{q}_b = & s_a s_b - x_a x_b - y_a y_b - z_a z_b\\
    						& +(s_a x_b + x_a s_b + y_a z_b - z_a y_b)i\\
    						& +(s_a y_b - x_a z_b + y_a s_b + z_a x_b)j\\
    						& +(s_a z_b + x_a y_b - y_a x_b + z_a s_b)k.
    \end{align}
    $$

- 模长

  - $\left\|\bold{q}\right\|^2 = \sqrt{s^2 + x^2 + y^2 + z^2}$

- 共轭

  - $\bold{q}^* = s - xi - yj - zk = [s, -\bold{v}]^T$

- 逆

  - $\bold{q}^{-1}=\bold{q}^* / \left\|\bold{q}\right\|^2$

- 

##### 用四元数表示旋转

- 在复数中，乘以i意味着旋转90°
- 在四元数中，乘以i意味着绕i轴旋转180°。$i^2=-1$。

设一个空间三维点$\bold{p} = [x,y,z]∈\mathbb{R}^3$，一个由**单位四元数**$\bold{q}$指定的旋转，三维点$\bold{p}$旋转后得到$\bold{p}'$。

- 如果用矩阵描述，那么$\bold{p}'=\bold{R}\bold{p}$。

- 用四元数表示

  - 首先，把三维空间点用一个虚四元数描述：
    $$
    \bold{p}=[0,x,y,z]^T=[0,\bold{v}]^T.
    $$
    四元数的三个虚部与空间中的三个轴相对应。

  - 旋转后的点$\bold{p}'$
    $$
    \bold{p}'=\bold{q} \bold{p} \bold{q}^{-1}.
    $$



