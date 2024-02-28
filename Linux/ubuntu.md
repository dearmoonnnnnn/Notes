# 一、Ubuntu 配置clash

https://zhuanlan.zhihu.com/p/598337110

\# 官方网站[clash](https://link.zhihu.com/?target=https%3A//github.com/Dreamacro/clash/releases)下载文件：**clash-linux-amd64-v1.12.0.gz**（也可以下载最新的版本，前缀是clash-linux-amd64即可）

进入该文件所在目录，在页面空白处右键，在终端打开

1. 解压

   ```
   gunzip clash-linux-amd64-v1.12.0.gz
   ```

2. 将clash-linux-amd64-v1.12.0文件重命名为clash

   ```
   mv clash-linux-amd64-v1.12.0 clash
   ```

3. 在此目录下创建文件夹（注意这里用大写Clash只是为了和clash区别开）

  ```
  mkdir Clash
  ```

4. 移动clash文件夹到Clash文件夹

   ```
   mv clash ./Clash
   ```

5. 进入Clash文件夹

   ```
   cd Clash
   ```

6. 下载clash 配置文件config.yaml （注意：这个订阅链接是自己的，替代 [订阅链接]，如果失败了说明订阅链接有问题）

   ```
   wget -O config.yaml [订阅链接]
   ```

   这里下载的config.yaml是乱码，推测原因为文件中存在中文字符

   由于之前在手机上配置过clash for android，可在手机中的clash导出配置文件`配置文件.yaml`

   将其弄到Liunx的Clash目录中，并改名`config.yaml`

7. 下载Country.mmdb

   ```
   wget -O Country.mmdb https://www.sub-speeder.com/client-download/Country.mmdb
   ```

   注意：如果步骤7失败了也没关系，直接跳过这一步，后面也会自动下载。也可以在网址[Country.mmdb](https://link.zhihu.com/?target=https%3A//github.com/Loyalsoldier/geoip/releases/download/202212010123/Country.mmdb)下载。

8. 启动clash

   ```
   ./clash -d .
   ```

   注意：如果提示权限不足，先执行 `chmod +x clash` ，再执行 `./clash -d .`  出现如下表示成功，并保持此终端打开。

   ![image-20230314170958315](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314170958315.png)

9. 打开系统设置，选择网络，点击网络右边的⚙按钮，选择手动，填写HTTP和HTTPS
   代理为127.0.0.1:7890，填写Socks主机为127.0.0.1:7891，即可启用系统代理。

   ![image-20230314171334255](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314171334255.png)

10. 访问  http://clash.razord.top/

    这个页面要求提供，Host,Port两个输入：

    - Host: 127.0.0.1
    - 
      Port: 9090 

# 二、调整分区大小

## Question：

直接用swapfile分配swap空间，为什么安装ubuntu时需要分配swap空间



## 动机：

不小心给swap分区分配太多空间，导致可用磁盘空间过小

https://blog.csdn.net/lt9700/article/details/127979193

1. 下载gparted

   ```
   sudo apt-get install gparted
   ```

2. 启动 

   ```
   sudo gparted
   ```


# 三、双系统home空间不足的解决方法

安装双系统时分配空间过少，复现代码搭载环境，出现home空间内存不足的问题。

解决思路：在windows分出空白卷，并在其中创建共享文件夹，挂在到ubuntu的home路径下

参考链接：https://blog.csdn.net/qq_62517226/article/details/128708656

## 1、 windows下的操作

step1：新建空白卷

1. ```
   win+x，然后打开磁盘管理
   ```

2. 类似于安装双系统的操作，选择磁盘0，右键，压缩卷。

   该过程比较漫长，耐心等待。

3. 将新压缩出来空间设置为新的空白卷E

![image-20230403171714683](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230403171714683.png)

step2：在空白卷下新建一个文件夹，设置属性为共享，并添加**Everyone用户**，以便于使子系统都可以共享该文件夹

![image-20230403173317039](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230403173317039.png)

## 2、linux下的操作

1. 查看磁盘信息，找到新加卷E盘对应的分区

   ```
   sudo fdisk -l
   ```

   ![30f7ea457dfaf04c099735ec2039a45a](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/30f7ea457dfaf04c099735ec2039a45a.png)

   根据内存辨认，这里是60G，对应分区为dev/sda2

2. 查看分区UUID

   ```
   sudo blkid
   ```

   ![3d21188b75b77f4ed43cc360f41c7ebc](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/3d21188b75b77f4ed43cc360f41c7ebc.png)

3. 创建挂载点，在home目录下新建文件夹，用于作挂载点

   ![25090ef8f034a6bce6164fd058b3602a](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/25090ef8f034a6bce6164fd058b3602a.png)

4. 修改fstab文件进行永久挂载

   ```
   sudo vim /etc/fstab
   ```

   ![img](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/A7A_ICD@S{A_6KLM]4Z9HJI.jpg)

   在最后一行加上挂载的信息，第一个是UUID，第二个是新建挂载点的地址，系统文件类型为ntfs

5. 进行挂载操作，执行如下代码实现永久挂载

   ```
   sudo mount -a
   df -kh
   ```

## 3、结语

成功实现共享文件夹在挂载磁盘下的情况，既可以实现软件的迁移使用，又能和windows实现文件的传输。

# 四、Ubuntu安装与卸载软件

## 4.1 安装软件

### A 、使用安装包(dep等)

##### 选择对应的版本

在终端输入

```
uname -m
```

- 若输出为`x86_64`，则系统是基于64位的x86架构，应选择x86或者特别指出位64位的版本
- 若输出为`i686`或`i386`，则系统是基于32位的x86架构，应该选择x86或者32位版本。
  - 32位软件版本
    - x86
  - 64位版本
    - x64
    - x86_64
- 若输出为`armv71`或类似，说明使用的是32位的ARM架构
- 若输出为`aarch64`，则表示系统为64位的ARM架构，选择arm64或者aarch64版本

### B、Ubuntu卸载软件

ubuntu卸载和安装 https://blog.csdn.net/Laney_Midory/article/details/120686618

https://blog.csdn.net/qq_42170079/article/details/130770073

## 4.2 卸载源码安装的软件

在你make install的文件下面输入:

```
sudo make uninstall
```

然后删除源码即可

# 五、NVIDIA驱动

查询推荐安装的驱动版本

```bash
ubuntu-drivers devices
```



## 5.1 安装驱动

三种方法

### A、官方下载

### B、ubuntu自带的“附加驱动”中安装

### C、apt安装

## 5.2 卸载

两种方法

### A、进入系统的路径/usr/bin,然后查看与nvidia相关的命令

```bash
ls nvidia-*
```

若显示结果有`nvidia-uninstall`，则可以使用该命令卸载。

### B、直接使用系统自带的命令卸载

```bash
sudo apt-get remove --purge nvidia*
sudo apt autoremove
```

