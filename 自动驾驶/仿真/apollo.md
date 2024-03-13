# 零、概念解释、资料链接

1. aem：命令行工具，提供管理Apollo容器的能力。==使用aem，不需要运行Apollo脚本来启动和进入容器，避免了Apollo脚本污染工作空间代码的问题==。
1. dreamview：可视化工具，可播放record数据
1. cyber模块：
1. planning模块：
1. Mainboard: Cyber RT的主入口，可以通过`mainboard - d  xxx.dag`来启动一个模块



安装文档：https://apollo.baidu.com/community/Apollo-Homepage-Document/Apollo_Doc_CN_8_0?doc=%2F%25E5%25AE%2589%25E8%25A3%2585%25E8%25AF%25B4%25E6%2598%258E%2F%25E8%25BD%25AF%25E4%25BB%25B6%25E5%258C%2585%25E5%25AE%2589%25E8%25A3%2585%2F%25E8%25BD%25AF%25E4%25BB%25B6%25E5%258C%2585%25E5%25AE%2589%25E8%25A3%2585



安装时常用的错误总结及使用窍门

https://www.iotword.com/3928.html



感知模块汇总：

https://blog.csdn.net/Liiipseoroinis/article/details/121742310



percetion如何启动、CyberRT如何定义、实现、启动组件（component）：

http://www.manongjc.com/detail/61-ajkjskovpqojfis.html

# 一、aem

## 子命令

类似于git或apt等常见的命令行工具，aem的功能被组织成子命令，如`start`负责启动一个容器，`enter`负责进入一个已启动的容器等等。

子命令可能需要一些参数，可以在子命令后输入 -h、-help来查看详细的参数

# 二、QuickStart-软件包方式

https://apollo.baidu.com/community/Apollo-Homepage-Document/Apollo_Doc_CN_8_0?doc=%2F%25E5%25AE%2589%25E8%25A3%2585%25E8%25AF%25B4%25E6%2598%258E%2F%25E8%25BD%25AF%25E4%25BB%25B6%25E5%258C%2585%25E5%25AE%2589%25E8%25A3%2585%2FQuickStart-%25E8%25BD%25AF%25E4%25BB%25B6%25E5%258C%2585%25E6%2596%25B9%25E5%25BC%258F

通过四个典型场景介绍软件包的典型使用方式，以及如何在安装软件包后，在工作空间下安装Apollo各模块源码。

## 2.1 场景一：使用Dreamiew查看数据包

### 步骤一：进入Apollo Docker环境

1. 创建工作空间(若已创建，可忽略)：

   ```
   mkdir application-demo
   cd application-demo
   ```

2. 启动Apollo环境容器：

   ```
   aem start
   ```

3. 进入Apollo环境容器：

   ```
   aem enter
   ```

   注意：`-f`参数强制重新创建容器，`--name`为容器命名。

4. 初始化工作空间(==下次不用再执行==)：

   ```
   aem init
   ```

   ![image-20230314142640690](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314142640690.png)

### 步骤二：安装Dreamiew

在统一终端，输入以下命令，安装Apollo的DreamView程序

```
sudo apt install apollo-neo-dreamview-dev apollo-neo-monitor-dev
```

安装完成示意图：

![image-20230314143226835](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314143226835.png)

### 步骤三：启动DreamView

```
aem bootstrap start
```

![image-20230314143306139](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314143306139.png)

启动失败，查看日志

没看懂，直接尝试`sudo aem bootstrap start`

![image-20230314144645170](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314144645170.png)

提示cyber没有安装，但是Dreamiew可以成功运行。

在浏览器中打开地址：http://localhost:8888 ，可以看到DreamView成功运行

![image-20230314144828039](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314144828039.png)

尝试安装`cyber-dev`，显示已安装。

![image-20230327153127936](/home/dearmoon/snap/typora/76/.config/Typora/typora-user-images/image-20230327153127936.png)

再次尝试启动dreamiew，不再报错

![image-20230327154122261](/home/dearmoon/snap/typora/76/.config/Typora/typora-user-images/image-20230327154122261.png)

### 步骤四：下载Apollo演示包

Record是Apollo记录数据的一种数据格式，以 `.record`为后缀。

在命令行中输入以下命令，下载record数据包。

```
wget https://apollo-system.cdn.bcebos.com/dataset/6.0_edu/demo_3.5.record
```

![image-20230314145229139](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314145229139.png)

### 步骤五：播放Apollo的演示包

```
cyber_recorder play -f demo_3.5.record --loop
```

选项--loop用于设置循环回放模式。

![image-20230314145452829](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314145452829.png)

在浏览器中打开DreamView页面，可以看到汽车的移动。

![image-20230314145653823](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314145653823.png)

### 步骤六：停止DreamView

```
aem bootstrap stop
```

![image-20230314145903344](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314145903344.png)

## 2.2 场景二：Cyber 组件扩展

以example-componet为例，描述如何简单的扩展开发并编译、运行一个Cyber组件。

Quickstart 工程中的 example-component 的源码是基于 Cyber RT 扩展 Apollo 功能组件的  demo，如果您对如何编写一个 component，dag 文件和 launch 文件感兴趣，您可以在QuickStart工程中找到  example-component 的完整源码。

demo/example-component 的目录结构如下所示：

```
demo/example_components/
|-- src
|   |-- common_component_example.cc
|   |-- common_component_example.h
|   |-- BUILD
|-- proto
|   |--examples.proto
|   |--BUILD
|-- BUILD
|-- cyberfile.xml
|-- example.dag
|-- example.launch
```

### 步骤一：下载quickstart项目

1. 下载项目代码：

   ```
   git clone https://github.com/ApolloAuto/application-demo.git
   ```

   ![image-20230314151338362](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314151338362.png)

   连接超时，且浏览器无法进入github

   尝试一：将https换成git，**失败**

   https://javaforall.cn/162551.html

   尝试二：找到hosts文件，手动添加github域名解析,**成功**

   查询以下两个网址的ip地址：`github.com`和`github.global.ssl.fastly.net`

   ![image-20230314152639938](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314152639938.png)

   添加到hosts文件末端：

   ![image-20230314153013667](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314153013667.png)

   若使用vim无法保存，退出后在管理员模式下打开并写入即可保存。

   此时执行git成功：

   ![image-20230314153532560](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314153532560.png)

   https://blog.csdn.net/Z18544L/article/details/128352249?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EAD_ESQUERY%7Eyljh-1-128352249-blog-107200570.pc_relevant_landingrelevant&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EAD_ESQUERY%7Eyljh-1-128352249-blog-107200570.pc_relevant_landingrelevant&utm_relevant_index=2

2. 进入项目目录：

   ```
   cd application-demo
   ```

### 步骤二：进入Apollo Docker环境(已进入可忽略)

1. 启动容器：

   ```
   aem start
   ```

2. 输入以下命令进入Apollo：

   ```
   aem enter
   ```

### 步骤三：编译componet

通过以下命令编译 componet

```
buildtool build --packages example_components
```

buildtool 是 Apollo的构建工具，具体查阅[buildtool-Apollo构建工具文档](https://apollo.baidu.com/Apollo-Homepage-Document/Apollo_Doc_CN_8_0/%E8%BD%AF%E4%BB%B6%E5%8C%85%E7%AE%80%E4%BB%8B/%E8%BD%AF%E4%BB%B6%E5%8C%85%E5%B7%A5%E5%85%B7%E4%BB%8B%E7%BB%8D/buildtool%20-%20Apollo%20%E6%9E%84%E5%BB%BA%E5%B7%A5%E5%85%B7/)

- --packages 参数指定了编译**工作空间**指定的package的路径，本例中指定了example_componets
- 调用脚本编译命令时，当前所在目录即为**工作空间目录**，请务必在工作空间下使用脚本编译命令。

![image-20230314155133559](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314155133559.png)

出现错误，下载bazel_skylib包超时。

尝试一：搭梯子，**成功**

https://blog.csdn.net/qq_43586192/article/details/119025441

搭梯子教程：

https://zhuanlan.zhihu.com/p/598337110

![image-20230314170046353](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314170046353.png)

### 步骤四：运行component

运行以下命令

```
cyber_launch start example_components/example.launch
```

![image-20230314172547547](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314172547547.png)

这时候可以打开另一个终端，运行cyber_monitor，观察channel中的数据

```
cyber_monitor
```

PS：也要先进入容器

![image-20230314173308157](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314173308157.png)

## 2.3 场景三：学习和试验 planning 模块

本场景介绍如何学习和调整 planning 模块，对 planning 模块进行编译和调试，帮助开发者熟悉 Apollo v8.0-pre 的开发模式。

planning_customization 模块是一个 End-2-End 的场景解决方案（即可以在仿真环境内跑通 Routing  Request 的全部内容），但是其中并没有包含任何源码，只包含一个 cyberfile.xml  文件，描述该场景下依赖的所有组件包（planning-dev、dreamview-dev，routing-dev，task_manager 和  monitor-dev）以及其已入的方式。因为需要对 planning 源码进行修改扩展，所以其中 planning-dev 包是因 “src” 的方式引入，在编译该模块时会自动下载 planning 源码，并复制到工作空间中。

planning_customization 的 cyberfile 如下所示：

```
<package>
  <name>planning-customization</name>
  <version>1.0.0</version>
  <description>
   planning_customization
  </description>
  <maintainer email="AD-platform">AD-platform@baidu.com</maintainer>
  <type>module</type>
  <src_path>//planning_customization</src_path>
  <license>BSD</license>
  <author>Apollo</author>
  <depend>bazel-extend-tools-dev</depend>
  <depend type="binary" repo_name="dreamview">dreamview-dev</depend>
  <depend type="binary" repo_name="routing">routing-dev</depend>
  <depend type="binary" repo_name="task-manager">task-manager-dev</depend>
  <depend type="binary" repo_name="monitor">monitor-dev</depend>
  <depend type="src" repo_name="planning">planning-dev</depend>

  <depend expose="False">3rd-rules-python-dev</depend>
  <depend expose="False">3rd-grpc-dev</depend>
  <depend expose="False">3rd-bazel-skylib-dev</depend>
  <depend expose="False">3rd-rules-proto-dev</depend>
  <depend expose="False">3rd-py-dev</depend>
  <depend expose="False">3rd-gpus-dev</depend>
  
  <builder>bazel</builder>
</package>
```

### 步骤一：下载quickstart项目（在之前的场景已完成）

### 步骤二：进入Apollo Docker 环境（同之前的场景）

### 步骤三：对planning源码包进行编译

```
buildtool build --packages planning_customization
```

Apollo 的编译工具会自动分析所有所需要的依赖，自动下载并生成必要的依赖信息文件，planning 的具体依赖可以在 cyberfile.xml 中查看。

出现问题：多线程编译卡死

尝试一：[添加交换空间](https://blog.csdn.net/vivivivi1996/article/details/128936296)

https://blog.csdn.net/weixin_50151928/article/details/128273597

没有，运行时使用free -m 查看 ，内存和交换空间均充足，不是所在问题。

每次编译都卡死，执行后续步骤，发现已经编译完成。

### 步骤四：对planning进行调试

## 2.4 场景四：感知激光雷达功能测试

### 步骤一：启动Apollo Docker环境并进入

1. 创建工作空间（同上，以创建可忽略）

   ```
   mkdir application-demo
   cd application-demo
   ```

2. 输入以下命令以GPU模式进入容器环境（激光雷达功能必须使用GPU）

   ```
   aem start_gpu -f
   ```

   -f 强制重新创建容器，第二次不要使用

   第一次启动gpu容器环境，会下载依赖.

   ![image-20230317110438060](/home/dearmoon/snap/typora/76/.config/Typora/typora-user-images/image-20230317110438060.png)

3. 输入以下命令进入容器：

   ```
   aem enter
   ```

4. 初始化工作空间

   ```
   aem init
   ```

   ![image-20230317111118698](/home/dearmoon/snap/typora/76/.config/Typora/typora-user-images/image-20230317111118698.png)

### 步骤二：下载record数据包

1. 输入以下命令下载数据包：

   ```
   wget https://apollo-system.bj.bcebos.com/dataset/6.0_edu/sensor_rgb.tar.xz
   ```

   ![image-20230317112248858](/home/dearmoon/snap/typora/76/.config/Typora/typora-user-images/image-20230317112248858.png)

2. 创建目录并将下载好的安装包解压到该目录中：

   ```
   sudo mkdir -p ./data/bag/
   sudo tar -xzvf sensor_rgb.tar.xz -C ./data/bag/
   ```

### 步骤三：安装DreamView

在同一个终端，输入以下命令，安装DreamView（==和场景二的安装有什么区别==）

```
buildtool install --legacy dreamview-dev monitor-dev
```

### 步骤四：安装 transform、perception 和 localization

在同一个终端，输入以下命令，安装 perception 程序。

1. ```bash
   buildtool install --legacy perception-dev
   ```

   注：如果您对感知包有二次开发的需求，使用下述命令安装 perception 程序。    

   ```
   buildtool install perception-dev
   ```

   使用上述命令 perception 源码会被下载至工作空间中的 modules 中，对源码进行二次开发后使用下述命令进行编译。                

   ```
   buildtool build --gpu --packages modules/perception
   ```

2. 输入以下命令安装localization、v2x和transform程序

   ```
    buildtool install --legacy localization-dev v2x-dev transform-dev
   ```


### 步骤五：模块运行

1. 在同一个终端，输入以下命令，启动Apollo的DreamView程序

   ```
   aem bootstrap start
   ```

   同场景一

2. 使用 mainboarrd方式启动激光雷达模块：

   ```
   mainboard -d /apollo/modules/perception/production/dag/dag_streaming_perception_lidar.dag
   ```

   出现两个错误：

   - 地图尺寸问题，上网查询，该问题不影响后续流程，忽略![image-20230327154444015](/home/dearmoon/snap/typora/76/.config/Typora/typora-user-images/image-20230327154444015.png)

   - 无法以text模式或者二进制模式打开engine.conf文件

     ![image-20230327154628371](/home/dearmoon/snap/typora/76/.config/Typora/typora-user-images/image-20230327154628371.png)

     按照目录查找，发现cnnseg文件夹下没有cnnseg128_caffe目录

     ![image-20230327155958661](/home/dearmoon/snap/typora/76/.config/Typora/typora-user-images/image-20230327155958661.png)

     - 尝试一：注意到是perception模块，重装该模块，问题未解决

     - 尝试二：替换模型，改用cnnseg64，根据官方文档步骤七，替换模型。

       详细见步骤七

### 步骤六：

1. 播放数据包：需要使用-k参数==屏蔽掉数据包中包含的感知通道数据==。

![image-20230327175025978](/home/dearmoon/snap/typora/76/.config/Typora/typora-user-images/image-20230327175025978.png)

### 步骤七：模型替换

介绍激光雷达检测过程中`MASK_PILLARS_DETECTION、CNN_SEGMENTATION和CENTER_POINT_DETECTION`模型的参数配置和替换流程。配置文件:`lidar_detection_pipeline.pb.txt`
