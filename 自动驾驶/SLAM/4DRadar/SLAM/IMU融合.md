以下开源的SLAM系统融合了IMU数据

- ORB-SLAM3 - 这是ORB-SLAM2的扩展版本,支持单目、双目、RGB-D相机以及IMU,是一个全能型的系统。Github地址:https://github.com/UZ-SLAMLab/ORB_SLAM3
  - 它使用紧耦合的方式融合IMU数据。首先它会对 IMU 数据进行预积分,然后在优化过程中将预积分约束添加到 bundle adjustment 中,与视觉约束一起优化,实现视觉和 IMU 数据的紧耦合。
- VINS-Mono - 一个视觉-惯性单目SLAM系统,由香港科技大学开发。使用滑动窗口优化,鲁棒性强。支持IMU外参在线标定。Github地址:https://github.com/HKUST-Aerial-Robotics/VINS-Mono
  - 它使用基于滑动窗口的紧耦合方式。IMU 数据被预积分,形成相邻图像帧之间的约束,然后与视觉特征一起在滑动窗口内进行优化。同时,它还会在线标定 IMU 的外参。
- VINS-Fusion - 基于VINS-Mono开发,支持单目、双目、RGB-D相机,同时紧耦合IMU数据。Github地址:https://github.com/HKUST-Aerial-Robotics/VINS-Fusion
  - 它在 VINS-Mono 的基础上,增加了对双目和 RGB-D 相机的支持。融合 IMU 的方式与 VINS-Mono 类似,也是基于滑动窗口的紧耦合优化。
- LIO-SAM - 一个紧耦合的激光视觉惯性里程计,同时利用激光雷达、相机和IMU进行状态估计。Github地址:https://github.com/TixiaoShan/LIO-SAM
  - 它使用基于图优化的方法,将 IMU 预积分约束、激光里程计约束、GNSS 约束等一起添加到因子图中,然后进行优化,实现多传感器融合。
- OKVIS - 由苏黎世联邦理工学院开发的视觉惯性里程计开源项目,利用了后端的关键帧优化。Github地址:https://github.com/ethz-asl/okvis
  - 它使用基于关键帧的紧耦合优化方法。IMU 数据被预积分,形成关键帧之间的约束,然后与视觉特征一起进行优化。它使用了 BRISK 特征点和 GPU 加速来提高效率。
- Maplab - 一个研究性的视觉惯性映射框架,同样由苏黎世联邦理工学院开发。Github地址:https://github.com/ethz-asl/maplab
  - 它使用了与 OKVIS 类似的方法,也是基于关键帧的紧耦合优化。不同的是,它还增加了回环检测和重定位功能,以建立全局一致的地图。
- ROVIO - 一个强大的直接法视觉惯性里程计,由苏黎世联邦理工开发。Github地址:https://github.com/ethz-asl/rovio
  -  它使用了基于 EKF 的紧耦合融合方法。IMU 数据被用来预测相机姿态,然后通过对直接法的残差进行更新,实现视觉和 IMU 的紧耦合。它不需要特征点提取和匹配,因此效率较高。