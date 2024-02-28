CONTAINER ID

5a5cf5912b0d

# MLIR环境搭建

操作系统：Ubuntu 18.04.5

MLIR官方环境搭建：https://mlir.llvm.org/getting_started/

需要的软件/工具：Cmake、git、ninja、C++ toolchain（可能是指GCC或者clang）



**一、容器的使用**

为了获得root权限，同时避免不同用户相互影响，创建容器，并在容器中搭配环境。

**1、列出本地镜像**

```
docker images
```

![img](https://cdn.nlark.com/yuque/0/2021/png/22889654/1633784562300-8b83e9a3-16d0-45f2-83b1-8c9947e47cff.png)

**2、创建容器**

记录下所需镜像的ID，使用`docker run`命令创建容器

```
docker run -itd --gpus=all -p 13212:22 --name ty_docker 4eb8f7c43909
```

![img](https://cdn.nlark.com/yuque/0/2021/png/22889654/1633784737608-5bc57104-5975-448b-843b-9c56f251ac5a.png)

**-p:** 指定端口映射，格式为：**主机(宿主)端口:容器端口**

**--name:** 指定容器名称

**3、列出容器**

```
docker ps
```

![img](https://cdn.nlark.com/yuque/0/2021/png/22889654/1633785383922-84d4ee2d-f647-43b0-a9cd-e10f7eb7834d.png)

根据、容器的名称，找到容器ID（9ac205eed408）

PS：有时可能找不到创建的容器（被隐藏了），使用`docker ps -all`列出所有容器

**4、进入容器**

```
docker exec -it 9ac205eed408 bash
```

PS：创建好的容器默认只有apt工具，没有git、python等工具包\依赖包。

**5、退出容器**

```
exit
```



**二、搭建环境**

参考官方文档 https://mlir.llvm.org/getting_started/



**1、先更新apt包**

```
apt-get update
apt-get upgrade
```



**2、下载llvm**

```
apt install git
git clone https://github.com/llvm/llvm-project.git
```



**出现的问题：**下载速度过慢

**解决方法：**将文件传到gitee后再下载

```
git clone -b main https://gitee.com/tong-yuan10/llvm-project.git
```



**3、下载cmake**

使用`apt install cmake`下载的版本过低，不符合要求.



下载压缩包

```
wget https://cmake.org/files/v3.13.4/cmake-3.13.4-Linux-x86_64.tar.gz
```

解压

```
tar zxvf cmake-3.12.2-Linux-x86_64.tar.gz
```

创建软链接

```
mv cmake-3.13.4-Linux-x86_6 /opt/cmake-3.13.4
ln -sf /opt/cmake-3.13.4/bin/* /usr/bin/
```

查看版本

```
cmake --version
```

![img](https://cdn.nlark.com/yuque/0/2021/png/22889654/1633790300327-7166c4dc-4d29-4092-9874-ea7b525ea9cb.png)

说明安装成功



**可能出现的问题：**下载速度过慢导致下载中断

**解决方法：**参考 https://blog.csdn.net/qq_28822933/article/details/83857357  先下载到宿主主机，解压后上传到容器根目录中，再进入容器创建软连接



**4、准备编译**

下载 ninja

```
apt install ninja-build
```

安装编译器（官方推荐为clang lld）

```
apt-get install clang lld
```

安装python3.6（或以上)

```
apt-get install python3.6
```

**5、编译**

```

mkdir llvm-project/build
cd llvm-project/build
cmake -G Ninja ../llvm \   
   -DLLVM_ENABLE_PROJECTS=mlir \    
   -DLLVM_BUILD_EXAMPLES=ON \    
   -DLLVM_TARGETS_TO_BUILD="X86;NVPTX;AMDGPU" \   
   -DCMAKE_BUILD_TYPE=Release \   
   -DLLVM_ENABLE_ASSERTIONS=ON \
```



```
cmake --build . --target check-mlir
```



**可能出现的错误1：**

![img](https://cdn.nlark.com/yuque/0/2021/png/22889654/1633789662616-fc98e36e-0838-411d-bd0b-681174b90013.png)

错误原因：未安装make

解决方法：`apt install make`



**6、编译成功**

![img](https://cdn.nlark.com/yuque/0/2021/png/22889654/1633789765157-24067a83-c99f-4422-980a-d6762b092354.png)



至此，环境搭建完成！
