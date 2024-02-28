# 0、Questions$ concept

## Questions

##### 1、为什么nvcc -V 与nvidia-smi不一致

因为cuda有两种API，分为运行API和驱动API，只要驱动API版本高于运行时API就没关系。nvidia-smi显示的是驱动API，nvcc -V显示的是运行时API。

# 一、安装cuda

https://blog.csdn.net/qq_41619524/article/details/116613925

https://developer.nvidia.com/cuda-10.2-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=runfilelocal