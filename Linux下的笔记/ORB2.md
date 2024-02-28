

# 一、安装与配置

https://www.bilibili.com/video/BV1hQ4y127xJ?p=8&vd_source=ddc1bcb1cabb9c324d56d94a9b4b6e93

## 1.1 opencv

当前电脑环境：cuda11.3 + 3.10.2 

###  错误一

Cmake Error：CUDA_nppi_LIBRARY(ADVANCED)

##### 原因解析：

https://gitlab.kitware.com/cmake/cmake/-/commit/1d9f2f9714af3cd9f43975456c4be03c2df463ad

在CUDA 11.0中，移除了这个库。但是CMAKE好像没及时更新。

##### 解决方法1：

https://blog.csdn.net/dawn_chen121/article/details/82828629

失败

##### 解决方法2：

更换openc版本为3.4.1

失败

##### 解决方法3：

更换cuda版本为10.2

卸载cuda：

```
cd  /usr/local/cuda/bin
sudo ./uninstall_cuda_x.x.pl
或
sudo ./cuda-uninstaller
```

根据教程安装cuda10.2

https://blog.csdn.net/qq_41619524/article/details/116613925

https://developer.nvidia.com/cuda-10.2-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=runfilelocal

安装完成后`cmake`成功,但make出现错误

### 错误二：make错误

```
/home/dearmoon/shares/share_ubuntu/library/opencv-3.4.1/modules/cudacodec/src/precomp.hpp:60:18: fatal error: dynlink_nvcuvid.h: 没有那个文件或目录
         #include <dynlink_nvcuvid.h>
                  ^~~~~~~~~~~~~~~~~~~
```

https://www.freesion.com/article/68371362181/

解决方法1：

在cmake末尾添加`-DWITH_CUDA=OFF`

## 编译ORB-slam2

### 错误一：

0:65: error: ‘slots_reference’ was not declared in this scope

### 尝试一：

https://blog.csdn.net/BigHandsome2020/article/details/123435000

根据博客将cmake.list中的c++11改称c++14

失败，出现新且原来不会发生的错误：无法找到pangolin

### 尝试二

https://blog.csdn.net/weixin_40817208/article/details/126528620

降低pangolin版本

下载地址：

https://pan.baidu.com/s/1ISc0pUhrWiuwNMEZ2cb0bw?login_type=qzone&pwd=bili&_at_=1686902205398

由于该源码已被编译过，需要删除build文件夹。

## 运行ORB-Slam2

下载数据

https://cvg.cit.tum.de/rgbd/dataset/freiburg1/rgbd_dataset_freiburg1_xyz.tgz

运行数据集
