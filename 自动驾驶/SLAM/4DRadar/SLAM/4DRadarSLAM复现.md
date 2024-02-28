# 一、依赖

## 1.1 Ubuntu和ROS

- Ubuntu 64位 18.04 或 20.04
  - 选择ubuntu20.04，代码成功率更高

- ROS Melodic 或 Noetic
  - ubuntu18.04安装ros melodic
  - ubuntu20.04安装ros noetic                                                  


## 1.2 第三方库

- PCL：`sudo apt install ros-XXX-pcl-ros`

- Eigen3：不需要安装

- OpenMP：（安装GCC即可）

- g2o：图优化,`sudo apt install ros-XXX-libg2o`。(`XXX`为`noetic`或`melodic`)

- ndt_omp :在src中，`git clone https://github.com/koide3/ndt_omp.git`

- GTSAM:不能在src中使用`catkin_make`，联合编译，使用源码安装方法，版本选择为`4.0.3`

  ```bash
   git clone -b 4.0.3 https://github.com/borglab/gtsam.git
  ```

  ```bash
  cd gtsam
  mkdir build && cd build
  cmake ..
  cmake --build .
  sudo make install
  ```




### 安装PCL(不需要安装，有ROS版的即可)

相关教程

https://blog.csdn.net/qq_42257666/article/details/124574029 



##### 步骤：

在官网下载pcl源码

```
mkdir build
cd build
cmake ..
```

#### 报错1：

```
-- The imported target "vtk" references the file
   "/usr/bin/vtk"
but this file does not exist.  Possible reasons include:

* The file was deleted, renamed, or moved to another location.
* An install or uninstall procedure did not complete successfully.
* The installation package was faulty and contained
  "/usr/lib/cmake/vtk-6.3/VTKTargets.cmake"
  but not all the files it references.

CMake Error at cmake/pcl_find_vtk.cmake:96 (message):
  Missing vtk modules: vtkRenderingOpenGL2;vtkRenderingContextOpenGL2
Call Stack (most recent call first):
  CMakeLists.txt:392 (include)
```

##### 原因分析:

find_vtk时，找到了ubuntu自带的vtk6.3，版本过低，缺少`vtkRenderingOpenGL2`和`vtkRenderingContextOpenGL2`

##### 解决方法：

- 卸载vtk6：

  ```
  sudo apt remove vtk6
  ```

- 安装vtk8.2.0

  见后续教程

##### 安装VTK8.2.0

[ubuntu18.04安装VTK8.2.0](https://blog.csdn.net/weixin_44723106/article/details/103071712)

1、VTK官网下载对应版本VTK源码

https://vtk.org/download/

2、安装依赖

```bash
sudo apt-get install cmake-curses-gui
sudo apt-get install freeglut3-dev
```

2、编译安装

```
mkdir build
cd build
ccmake .. //进入cmake图形化页面

//在图形化页面中设置
CMAKE_BUILD_TYPE=RELEASE \

//设置完成按c configure，之后按g生成文件

//文件生成后
make -j8

sudo make install
```

3、验证

https://kitware.github.io/vtk-examples/site/Cxx/GeometricObjects/CylinderExample/



4、配置路径

```bash
vim ~/.bashrc

//在文件中添加,/path_to_VTK是VTK库的路径
export LD_LIBRARY_PATH=/path_to_VTK/lib:$LD_LIBRARY_PATH

source ~/.bashrc
```

#### 另一种方法：直接使用apt一键安装

安装成功，但后续编译SLAM项目时仍会出错

```bash
sudo apt install libpcl-dev
```

## 1.3 ROS包

- apt安装：

  - geodesy

  - nmea_msgs

  - pcl_ros

  ```bash
  sudo apt-get install ros-XXX-geodesy ros-XXX-pcl-ros ros-XXX-nmea-msgs ros-XXX-libg2o
  ```

  根据ROS版本，将`XXX`替换为`melodic`或者`noetic`

- ROS项目源码安装：
  - [fast_apdgicp](https://github.com/zhuge2333/fast_apdgicp)或者[fast_gicp](https://github.com/SMRT-AIST/fast_gicp)
  - [ndt_omp](https://github.com/koide3/ndt_omp)
  - [barometer_bmp3888](https://github.com/zhuge2333/barometer_bmp388)

# 二、编译

在主目录下

```
mkdir build
cd build
cmake ..
```

## 错误1：没有安装ndt_omp

```
-- Could not find the required component 'ndt_omp'. The following CMake error indicates that you either need to install the package with the same name or change your environment so that it can be found.
CMake Error at /opt/ros/melodic/share/catkin/cmake/catkinConfig.cmake:83 (find_package):
  Could not find a package configuration file provided by "ndt_omp" with any
  of the following names:

    ndt_ompConfig.cmake
    ndt_omp-config.cmake
```

##### 原因分析：

没有安装ndt_omp，无法使用`apt install` 一键安装

##### 解决方法：

在另一个SLAM中找到odt_omp安装方法,并发现之前的项目管理方法错误

https://www.cnblogs.com/chenlinchong/p/11763481.html

以下为正确的项目管理方法

1. 创建ros工作空间

   ```
   // #catkin_ws表示ros的工作空间。在该工作空间中需要创建src文件夹,存放需要的库
   mkdir -p #catkin_ws/src  //该项目中，#catkin_ws具体为catkin_4DRadarSLAM
   ```

2. 在src文件目录下下载我们需要的工程和库

   ```
   cd catkin_4DRadarSLAM/src
   git clone https://github.com/zhuge2333/4DRadarSLAM.git
   git clone https://github.com/koide3/ndt_omp.git //这一步解决上述错误
   ```

3. 初始化工作空间

   ```
   catkin_init_workspace //博客中有这一步，官方教程中无
   ```

4. 使用catkin_make编译

   ```
   cd .. //退出src目录，返回工作空间主目录
   catkin_make 
   ```

   这时出现错误2

## 错误2:没有安装fast_gicp

```
-- Could NOT find fast_gicp (missing: fast_gicp_DIR)
-- Could not find the required component 'fast_gicp'. The following CMake error indicates that you either need to install the package with the same name or change your environment so that it can be found.
CMake Error at /opt/ros/melodic/share/catkin/cmake/catkinConfig.cmake:83 (find_package):
  Could not find a package configuration file provided by "fast_gicp" with
  any of the following names:

    fast_gicpConfig.cmake
    fast_gicp-config.cmake

```

##### 原因分析：

没有安装fast_gicp

##### 解决方法：

github上搜索fast_gicp,然后git clone 到src目录中

```
git clone https://github.com/SMRT-AIST/fast_gicp.git
```

## 错误3：没有安装GTSAM

```
CMake Error at 4DRadarSLAM/CMakeLists.txt:56 (find_package):
  By not providing "FindGTSAM.cmake" in CMAKE_MODULE_PATH this project has
  asked CMake to find a package configuration file provided by "GTSAM", but
  CMake did not find one.
```

##### 原因分析：

没有GTSAM

##### 解决方法1：

同上,github上搜索gtsam,然后git clone 到src目录中

### 显示新错误3.1：

```
CMake Error at /opt/ros/melodic/share/catkin/cmake/catkin_workspace.cmake:100 (message):
  This workspace contains non-catkin packages in it, and catkin cannot build
  a non-homogeneous workspace without isolation.  Try the
  'catkin_make_isolated' command instead.
```

##### 原因分析：

ros中包含catkin_make不支持的包

##### 解决方法：

在工作空间外下载gtsam,单独编译安装

### 新错误3.2：

```
../gtsam/libgtsam.so.4.3.0：对‘std::experimental::filesystem::v1::__cxx11::path::_M_split_cmpts()’未定义的引用
../gtsam/libgtsam.so.4.3.0：对‘std::experimental::filesystem::v1::status(std::experimental::filesystem::v1::__cxx11::path const&)’未定义的引用
../gtsam/libgtsam.so.4.3.0：对‘std::experimental::filesystem::v1::__cxx11::path::_M_find_extension() const’未定义的引用
../gtsam/libgtsam.so.4.3.0：对‘std::experimental::filesystem::v1::__cxx11::path::parent_path() const’未定义的引用
collect2: error: ld returned 1 exit status
examples/CMakeFiles/CameraResectioning.dir/build.make:110: recipe for target 'examples/CameraResectioning' failed
make[2]: *** [examples/CameraResectioning] Error 1
CMakeFiles/Makefile2:21918: recipe for target 'examples/CMakeFiles/CameraResectioning.dir/all' failed
make[1]: *** [examples/CMakeFiles/CameraResectioning.dir/all] Error 2
Makefile:165: recipe fo
```

##### 原因分析：

与c++版本有关，gcc 7仅支持C ++ 17实验filesystem命名空间

##### 解决方法1：

https://blog.csdn.net/qq_34113993/article/details/103245234?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-103245234-blog-121900484.235%5Ev38%5Epc_relevant_anti_vip_base&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-103245234-blog-121900484.235%5Ev38%5Epc_relevant_anti_vip_base&utm_relevant_index=1

g++ -v查看当前版本

安装4.8版本的g++，并修改默认g++版本使用4.8版。

camke 时发现GNU版本仍为7.5

cmake编译时指定编译器版本，此条未实验。

##### 解决方法2：

在github分支上选择gtsam4.0.3版本，编译安装成功。



这时catkin_make,不再提示没有包，但出现新错误

## 错误4：没有安装fast_apdgicp.hpp

```
fatal error: fast_gicp/gicp/fast_apdgicp.hpp: 没有那个文件或目录
 #include <fast_gicp/gicp/fast_apdgicp.hpp>
          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

##### 原因分析：

可能是fast_gicp版本错误

##### 解决方法：

在gtihub上搜索fast_apdgicp,发现作者自己写了一个库：fast_apdgicp

https://github.com/zhuge2333/fast_apdgicp

继续catkin_make，出现

## 错误5：没有barometer包

```
catkin_ws/src/4DRadarSLAM/apps/radar_graph_slam_nodelet.cpp:71:10: fatal error: barometer_bmp388/Barometer.h: No such file or directory
 \#include <barometer_bmp388/Barometer.h>
```

##### 原因分析：

作者没有提供相应文件

##### 解决方法：

在github代码4DRadarSLAM的`Issues`区中发现作者对该问题有解答，提供了一个新的包。

在src目录下

```
git clone https://github.com/zhuge2333/barometer_bmp388.git
```

## 错误6：'std'没有'make_unique'成员

```
error: ‘make_unique’ is not a member of ‘std’
```

##### 原因分析：

是因为 **std**::**make _ unique**在C++14以后新加入的函数。

ubuntu18.04默认的g++版本过低(使用的c++标准过低)。

ubuntu20.04默认的g++版本9.4.0，未出现该问题。

##### 解决方法1：

修改`catkin_workspace\src\4DRadarSlam\CMakeLists.txt`

在开头添加

```cmake
set(CMAKE_CXX_STANDARD 14)  # 使用 C++14 标准
set(CMAKE_CXX_STANDARD_REQUIRED ON)
```

##### 解决方法2：

在执行`catkin_make`时，指定使用`C++14`标准

```bash
catkin_make -DCMAKE_CXX_STANDARD=14
```

##### 解决方法3：

升级g++版本

## 错误7：'std'没有'mutex'成员[]

```bash
/home/dearmoon/projects/4DRadarSlam/src/4DRadarSLAM/include/radar_graph_slam/loop_detector.hpp:98:8: error: ‘mutex’ in namespace ‘std’ does not name a type
```

Ubuntu20.04未出现该问题

##### 原因分析：

`mutex`在c++14后被加入到std库中。

ubuntu18.04默认的g++版本过低(使用的c++标准过低)。

ubuntu20.04默认的g++版本9.4.0，未出现该问题。

##### 解决方法：

在提示错误的文件中添加头文件

```c++
#include <mutex>  
```

##### 解决方法2(未实践)：

升级g++版本

## 至此，catkin_make成功

# 三、运行

下载好作者提供的数据包之后，

修改rosbag_play_radar_carpark1.launch文件中的路径

在工作空间根目录下运行

```bash
roslaunch radar_graph_slam radar_graph_slam.launch
```

出现

## 错误1

```
RLException: [radar_graph_slam.launch] is neither a launch file in package [radar_graph_slam] nor is [radar_graph_slam] a launch file name
The traceback for the exception was written to the log file
```

##### 原因分析：

https://blog.csdn.net/qq_44164791/article/details/130351276

没有将项目的环境变量添加到系统路径中。

##### 解决方法：

方法不唯一，这里直接对环境变量文件进行修改。

1. 打开环境变量文件

   ```bash
   sudo  vim ~/.bashrc
   ```

2. 在文件最后加入语句

   ```bash
   source ~/catkin_ws/devel/setup.bash
   export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:~/catkin_ws/
   ```

   注意：~/catkin_ws/表示自己的ros工作空间目录，

3. 保存并退出后，执行以下命令

   ```bash
   source ~/.bashrc
   ```

## 重新运行，成功



发现闭环检测失败，可能是没有启动某些模块。

