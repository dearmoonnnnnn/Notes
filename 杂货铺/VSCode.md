# 零、概念、问题和相关资料

##### 1、CMake

是一个开源的构建工具，用于管理软件项目的构建过程。

主要特定：跨平台

##### 2、编译、构建

- 编译
  - 将高级语言代码转换为机器语言的过程
- 构建
  - 将源代码（如Java、C++）转换为可执行的文件或库的过程。
  - 编译是构建的一个步骤，构建包含了编译
    - 除了编译外，对库文件的链接，还可能包括映像文件打包、测试、部署等操作

##### 3、GDB

GNU Debugger，是一个功能强大的调试工具，用于调试C、C++、Fortran等语言。

是GNU项目的一部分

## 问题

##### 1、gcc和g++的区别

GCC默认情况下只链接C标准库，而不会自动链接C++标准库。

对于C++程序，需要显示地使用`g++`来编译和链接，而不是`gcc`。

## 相关资料

windows下搭建C++开发环境

- [【VScode】手把手教你如何搭建C/C++开发环境_vscode创建c++项目-CSDN博客](https://blog.csdn.net/qq_63320529/article/details/130140953)
- [Windows下基于VSCode搭建C++开发环境（包含整合MinGW64、CMake的详细流程）_vscode创建c++项目-CSDN博客](https://blog.csdn.net/X_trans/article/details/131914477)

ubuntu下搭建C++开发环境

- 官方文档
  - https://code.visualstudio.com/docs/cpp/config-linux#_running-the-build
- 官方文档翻译版
  - https://blog.csdn.net/icacxygh001/article/details/120981354
- 视频
  - https://www.bilibili.com/video/BV1Vz4y1w7gS/?spm_id_from=333.337.search-card.all.click&vd_source=ddc1bcb1cabb9c324d56d94a9b4b6e93

# 一、Ubuntu20.04搭建C++开发环境

## 1、安装必要的工具

- GCC/G++ （编译器）
- CMake （构建工具）
- Git （版本控制）

安装命令：

```bash
sudo apt update
sudo apt install build-essential cmake git
```

## 2、安装VSCode

1. 从官网下载deb包

2. 使用终端安装

   ```bash
   sudo apt install ./path-to-downloaded-file/code_*.deb
   ```

## 3、安装VSCode扩展

- C++相关
  - C/C++
  - C/C++ Themes
  - C/C++ Extension Pack
- CMake Tools
  - 处理CMake项目
- Code Runner（可选）
  - 用于快速运行C++代码

## 4（0）、配置VSCode项目（使用json）

### 4.1 项目架构：

```yaml
/my_cpp_project
├── .vscode
│   ├── launch.json           : 调试配置文件
│   ├── tasks.json            : 编译任务配置文件
│   ├── c_cpp_properties.json : C/C++扩展配置文件
├── build
│   └── (编译输出文件)
├── src
│   ├── main.cpp              : 主程序文件
│   ├── example.cpp           : 其他源文件
├── include
│   ├── example.h             : 头文件
├── lib
│   └── (第三方库)
├── tests
│   ├── test_main.cpp         : 测试文件
├── .gitignore                : Git忽略文件
├── CMakeLists.txt            : CMake配置文件
└── README.md                 : 项目说明文件
```

### 4.2 配置文件：tasks.json

##### 作用：

用于编译

##### 自动生成：

在cpp文件下，点击终端->配置默认生成任务

![image-20240605183142520](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240605183142520.png)

选择g++

![image-20240605183229443](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240605183229443.png)

##### 运行生成(构建)任务：

生成tasks.json后，

在.c或.cpp文件下，执行`ctrl + shift + B`可运行生成任务。

##### 运行代码：

在终端中输入 `./example`, 运行代码

### 4.3 配置文件：launch.json

##### 作用：

用于调试

##### 自动生成：

![image-20240605171504009](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240605171504009.png)

![image-20240605171241403](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240605171241403.png)

![image-20240605172035363](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240605172035363.png)

- 在[]中输入c，找到（gdb）启动
- 确保`request`的值为`launch`
- 修改`program`的值
  - `${workspaceFolder}/helloworld`

## 4（1） 、配置VSCode项目（使用CMake）

##### 1、编写CMakeLists文件

在工作区文件夹中创建`CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.10)

# Set the project name
project(MyCppProject)

# Add an executable
add_executable(my_cpp_project src/main.cpp)
```

##### 2、配置CMake Tools扩展

使用CMake Tools扩展来配置CMake项目：

1. 打开命令面板(`Ctrl+Shift+P`)，然后选择`CMake：Configure`
2. 如果提示选择CMake工具链，选择安装的GCC编译器。

##### 3、生成构建文件

在终端进入项目根目录，输入以下命令

```bash
mkdir build
cd build
cmake ..
```

这将生成一个`Makefile`文件

##### 4、运行构建（生成）任务

`Ctrl+Shift+B`

这会生成一个可执行文件

##### 5、运行可执行文件

在终端运行可执行文件

```bash
./helloworld
```

##### 6、调试

- 方案一
  - 同4（0），生成launch.json文件进行调试
- 方案二
  1. `Ctrl+Shift+P`
  2. 运行`CMake: Debug`，启动调试会话

# 二、插件

通义灵码
