# 零、windos安装ubuntu双系统

## 问题：

##### 1、什么是efi系统分区，应该分配多大空间

用于存储引导加载程序和其他与系统引导相关的文件的地方。它是 UEFI（统一扩展固件接口）引导过程的一部分。

大小为200MB至500MB，也可以更大，具体取决于系统和需求。(ubuntu18.04 可用500MB)

##### 2、什么是swap分区，应该分配多大空间

用于操作系统的虚拟内存。当物理内存(RAM)不足时，操作系统可以将不常用的**内存页面**移动到Swap分区中，从而释放物理内存以供其他程序使用。

大小通常为物理内存大小的1到2倍。如果计算机有足够的物理内存，也可以选择不创建或者使用文件代替。这里16G物理内存选择创建16G的swap分区

##### 3、该选择ubuntu18.04还是ubuntu20.04

根据情况选择

ubuntu18.04：

- 更加稳定，对资源要求更低
- 适合**企业环境**(需要稳定性和长期支持的服务器)和**相对旧的硬件**（对硬件要求较低）

ubuntu20.04：

- 更好的桌面体验和更新的软件包
- 适合最新的硬件和希望体验新的GNOME版本的用户

较新的电脑需要最好选择20.04！！！

电脑硬件过新，18.04内核过低，无法识别显示器、声卡、wifi等驱动，安装20.04一劳永逸

# 一、安装 Ubuntu

[Windows 10 安装ubuntu 18.04 双系统（超详细教程）_安装ubuntu双系统-CSDN博客](https://blog.csdn.net/qq_43106321/article/details/105361644)

[【精选】【Ubuntu18.04分区分配方式推荐与详解】-CSDN博客](https://blog.csdn.net/qq_29960631/article/details/123369207)



# 二、删除Ubuntu系统

[【Linux卸载】Win10卸载Ubuntu双系统（不安装任何软件）_怎么卸载ubuntu系统_百里飞洋的博客-CSDN博客](https://blog.csdn.net/qq_51513895/article/details/128614127)



# 三、遇到的问题

### 1、没有无线驱动

- [双系统装完之后，Ubuntu系统连不上WIFI的问题 - 代码先锋网 (codeleading.com)](https://codeleading.com/article/42485056591/)
- [ubuntu18.04安装后没有wifi - CSDN文库](https://wenku.csdn.net/answer/b74e7077e0d19b293da066a3e293277e)

##### 适用于ubuntu20.04和ubuntu22.04

[Ubuntu20.04 无线网卡驱动（未发现wifi适配器）、Nvidia显卡驱动安装一条龙教程【多坑预警】_ubuntuwifi驱动安装-CSDN博客](https://blog.csdn.net/weixin_52490336/article/details/133139105)
直接使用链接中的项目地址，可能会编译出错
在github中搜到了intel官方驱动地址`https://github.com/intel/backport-iwlwifi.git`

将
```bash
git clone https://git.kernel.org/pub/scm/linux/kernel/git/iwlwifi/backport-iwlwifi.git
```
替换成
```bash
git clone https://github.com/intel/backport-iwlwifi.git
```

- 首先明确网卡是intel还是realtek，在windows中查看设备管理器可知本机网卡为 intel AX211

- 下载驱动

  ```bash
  git clone https://github.com/intel/backport-iwlwifi.git
  ```

- 进入该路径

  ```bash
  cd backport-iwlwifi
  
  cd iwlwifi-stack-dev
  ```

- 编译与安装

  ```bash
   sudo make defconfig-iwlwifi-public
   
   sudo make
   
   sudo make install
  ```

- 重启电脑后就能看到无线连接选项。PS：不要删除驱动，后续再出现该问题，直接在对应路径下安装驱动即可。

##### 适用于ubuntu18.04

https://blog.csdn.net/m0_74397934/article/details/134809876

- 更新linux内核
- 下载驱动

### 2、扩展显示屏无法使用

折腾了很久，由于电脑不支持独显直连，最终无法实现扩展屏和内置屏同时使用。

### 3、触摸板失效

![image-20231216192315752](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20231216192315752.png)

触摸板无法使用，设置中无触摸板模块。

1、检查是否存在可用的触摸板驱动程序

```bash
sudo apt update
sudo apt install xserver-xorg-input-synaptics
```

出现问题：

```bash
下列软件包有未满足的依赖关系：
 xserver-xorg-input-synaptics : 依赖: xserver-xorg-core (>= 2:1.18.99.901)
E: 无法修正错误，因为您要求某些软件包保持现状，就是它们破坏了软件包间的依赖关系。
```

先更新xserver-xorg-core

```bash
sudo apt-get install xserver-xorg-core
```

重新安装驱动

```bash
sudo apt install xserver-xorg-input-synaptics
```

失败，且出现登陆成功后无法使用鼠标和键盘的问题。

### 4、浏览器无法播放视频

##### 描述：

浏览器无法在相关网页播放视频，如b站等网页

##### 解决方法：

安装相应插件

```bash
sudo apt install flashplugin-installer

sudo apt install browser-plugin-freshplayer-pepperflash
```

重启浏览器




### 5、无法修改亮度

##### 解决方法：

在显卡配置文件中添加对应内：https://zhuanlan.zhihu.com/p/348624522?utm_id=0


https://www.cnblogs.com/zl-yang/p/13073356.html



https://blog.csdn.net/afgqwjgfjqwgfg/article/details/121084950


升级内核(暂未实现)

https://blog.csdn.net/qq_37416258/article/details/111186832

```bash
uname -r
```

发现内核版本为5.15.0

教程中升级到5.9，这里选择升级到5.19

