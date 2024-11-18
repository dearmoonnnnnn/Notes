# 零、问题 & 概念

## 概念

##### 1. 四元数

- 是四个实数构成的复数扩展

$$
q = w + xi + yj + zk
$$

- 用于表示和计算三维空间中的旋转
  - 一个表示旋转的四元数的组成：一个旋转轴 $\mathbf{v} = (x,y,z)$ 和旋转角度 $\theta$

## 问题

##### 1. 九轴和六轴 imu 的区别是什么？

- 九轴
  - 三轴加速度计
    - x、y、z  三个方向上的线性加速度
  - 三轴陀螺仪
    - x、y、z 三个轴上的角速度
- 六轴
  - 三轴加速度计
  - 三轴陀螺仪
  - 三轴磁力计
    - 感知地磁场，确定物体的绝对方位

##### 2. bag 文件中的 %time 和 filed.header.stamp 有什么区别

- %time 表示 bag 文件的时间戳，指消息被记录到 `bag` 文件中的时间

- `filed.header.stamp` 表示消息的时间戳，是传感器采集时的时间戳。

##### 3. 自己的 IMU 四元数数据是否需要坐标旋转

##### 4. 为什么 y 和 z 上的角速度 / 线性加速度处理时取反

# 一、消息格式对比

## 1. bag 文件

`sensor_msgs/Imu`

##### 1.1 头部

- %time
- field.header.seq
- field.header.stamp
- field.header.frame_id

##### 1.2 四元数

- field.orientation.x
- field.orientation.y
- field.orientation.z
- field.orientation.w
- field.orientation_covariance0
- field.orientation_covariance1
- field.orientation_covariance2
- field.orientation_covariance3
- field.orientation_covariance4
- field.orientation_covariance5
- field.orientation_covariance6
- field.orientation_covariance7
- field.orientation_covariance8

##### 1.3 角速度

- field.angular_velocity.x
- field.angular_velocity.y
- field.angular_velocity.z
- field.angular_velocity_covariance0
- field.angular_velocity_covariance1
- field.angular_velocity_covariance2
- field.angular_velocity_covariance3
- field.angular_velocity_covariance4
- field.angular_velocity_covariance5
- field.angular_velocity_covariance6
- field.angular_velocity_covariance7
- field.angular_velocity_covariance8

##### 1.4 线加速度

- field.linear_acceleration.x
- field.linear_acceleration.y
- field.linear_acceleration.z
- field.linear_acceleration_covariance0
- field.linear_acceleration_covariance1
- field.linear_acceleration_covariance2
- field.linear_acceleration_covariance3
- field.linear_acceleration_covariance4
- field.linear_acceleration_covariance5
- field.linear_acceleration_covariance6
- field.linear_acceleration_covariance7
- field.linear_acceleration_covariance8

PS：在 SLAM 中只用了四元数和三个方向上的角速度

## 2. 自己采集的 txt 文件

使用九轴 `Imu`

##### 2.1 姿态角

- IMU.FDI_ROLL
  - 滚转角
- IMU.FDI_PITCH
  - 俯仰角
- IMU.FDI_YAW
  - 航向角

##### 2.2 角速度

- IMU.IMU_RATEX
- IMU.IMU_RATEY
- IMU.IMU_RATEZ

##### 2.3 线性加速度

- IMU.IMU_ACCX
- IMU.IMU_ACCY
- IMU.IMU_ACCZ
- IMU.ACC Magnitude
  - 加速度矢量的模长
- IMU.ACC ROLL
  - 基于加速度数据得到的滚转角
- IMU.ACC PITCH
  - 基于加速度数据得到的俯仰角

##### 2.4 磁场相关

- IMU.IMU_MAGX
  - x 方向上的磁场强度
- IMU.IMU_MAGY
  - y 方向上的磁场强度
- IMU.IMU_MAGZ
  - z 方向上的磁场强度
- IMU.IMU_MAG_YAW
  - 根据磁场信息的航向角
- IMU.MAG Magnitude
  - 磁场矢量的模长

##### 2.5 其他物理量

- IMU.IMU_TEMP
  - 传感器内部的温度
- IMU.FDI_Pressure
  - 气压传感器的值
  - 可用于推算高度信息
- IMU.lastUpdate DLTA
  - 需要查阅文档确定
  - 可能表示两次数据之间的时间间隔。



# 二、slam 中用到的字段

## 1. preprocessing_nodelet.cpp

### 1.1 imu_callback

- 处理原始 `Imu` 数据
- 发布处理后的 Imu 数据
- 发布 `odom` 数据

##### 1.1.1 获取四元数

- `orientation.w`
- `orientation.x`
- `orientation.y`
- `orientation.z`

```cpp
   Eigen::Quaterniond q_ahrs(imu_msg->orientation.w,   // 四元数的姿态信息
                              imu_msg->orientation.x,
                              imu_msg->orientation.y,
                              imu_msg->orientation.z);
    Eigen::Quaterniond q_r =                            // 绕Z轴旋转180度，然后绕Y轴旋转180度，最后绕X轴旋转0度的旋转
        Eigen::AngleAxisd( M_PI, Eigen::Vector3d::UnitZ()) * 
        Eigen::AngleAxisd( M_PI, Eigen::Vector3d::UnitY()) * 
        Eigen::AngleAxisd( 0.00000, Eigen::Vector3d::UnitX());
    Eigen::Quaterniond q_rr =                           // 绕Z轴旋转0度，然后绕Y轴旋转0度，最后绕X轴旋转180度的旋转
        Eigen::AngleAxisd( 0.00000, Eigen::Vector3d::UnitZ()) * 
        Eigen::AngleAxisd( 0.00000, Eigen::Vector3d::UnitY()) * 
        Eigen::AngleAxisd( M_PI, Eigen::Vector3d::UnitX());
    Eigen::Quaterniond q_out =  q_r * q_ahrs * q_rr;    // 校正原始的IMU数据的姿态信息
	imu_data.orientation.w = q_out.w();
    imu_data.orientation.x = q_out.x();
    imu_data.orientation.y = q_out.y();
    imu_data.orientation.z = q_out.z();
```

##### 1.1.2 角速度

```cpp
    imu_data.angular_velocity.x = imu_msg->angular_velocity.x;
    imu_data.angular_velocity.y = -imu_msg->angular_velocity.y;
    imu_data.angular_velocity.z = -imu_msg->angular_velocity.z;
```

##### 1.1.3 线速度

```cpp
    imu_data.linear_acceleration.x = imu_msg->linear_acceleration.x;
    imu_data.linear_acceleration.y = -imu_msg->linear_acceleration.y;
    imu_data.linear_acceleration.z = -imu_msg->linear_acceleration.z;
```

# 三、