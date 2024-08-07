- [零、概念、问题和相关资料](#零概念问题和相关资料)
        - [1、CMake](#1cmake)
        - [2、编译、构建（生成）](#2编译构建生成)
        - [3、GDB](#3gdb)
  - [问题](#问题)
        - [1、gcc和g++的区别](#1gcc和g的区别)
  - [相关资料](#相关资料)
- [一、Ubuntu20.04搭建C++开发环境](#一ubuntu2004搭建c开发环境)
  - [1、安装必要的工具](#1安装必要的工具)
  - [2、安装VSCode](#2安装vscode)
  - [3、安装VSCode扩展](#3安装vscode扩展)
  - [4（0）、配置VSCode项目（使用json）](#40配置vscode项目使用json)
    - [4.1 项目架构：](#41-项目架构)
    - [4.2 配置文件：tasks.json](#42-配置文件tasksjson)
        - [作用：](#作用)
        - [自动生成：](#自动生成)
        - [运行生成(构建)任务：](#运行生成构建任务)
        - [运行代码：](#运行代码)
    - [4.3 配置文件：launch.json](#43-配置文件launchjson)
        - [作用：](#作用-1)
        - [自动生成：](#自动生成-1)
  - [4（1） 、配置VSCode项目（使用CMake）](#41-配置vscode项目使用cmake)
        - [1、编写CMakeLists文件](#1编写cmakelists文件)
            - [2、配置CMake Tools扩展](#2配置cmake-tools扩展)
            - [3、生成构建文件](#3生成构建文件)
            - [4、运行构建（生成）任务](#4运行构建生成任务)
            - [5、运行可执行文件](#5运行可执行文件)
            - [6、调试](#6调试)
- [二、插件](#二插件)


# 零、概念、问题和相关资料

##### 1、CMake

是一个开源的构建工具，用于管理软件项目的构建过程。

主要特定：跨平台

##### 2、编译、构建（生成）

- 编译(compile)
  - 将高级语言代码转换为机器语言的过程
- 构建(build)
  - 将源代码（如Java、C++）转换为可执行的文件或库的过程。
  - 编译是构建的一个步骤，构建包含了编译
    - 除了编译外，对库文件的链接，还可能包括映像文件打包、测试、部署等操作

##### 3、GDB

GNU Debugger，是一个功能强大的调试工具，用于调试C、C++、Fortran等语言。

是GNU项目的一部分

## 问题

##### 1. gcc和g++的区别

GCC默认情况下只链接C标准库，而不会自动链接C++标准库。

对于C++程序，需要显示地使用`g++`来编译和链接，而不是`gcc`。

##### 2. CMake 配置时，默认在顶级目录下生成 build ，如何更改为次级目录

将次级目录添加到工作区，不要直接打开顶级目录

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

`Ctrl+Shift+B`，生成一个可执行文件，并且自动补全需要的`tasks.json`文件

- 选择`CMake:生成`
  - 在`build`目录下生成可执行文件
- 或者`g++`
  - 在`src`目录下生成可执行文件

##### 5、运行可执行文件

在终端运行可执行文件

```bash
./helloworld
```

##### 6、调试

- 方案一
  - 同4（0），生成`launch.json`文件进行调试
- 方案二
  1. `Ctrl+Shift+P`
  2. 运行`CMake: Debug`，启动调试会话

# 二、Windows 搭建 C++ 开发环境

## 1. 安装必要的工具

### 1.1 安装编译器

可选三种编译器

1. 安装Visual Studio Build Tools
   - [Microsoft C++ 生成工具 - Visual Studio](https://visualstudio.microsoft.com/zh-hans/visual-cpp-build-tools/)
   - 在里面选中 MSVC 编译器、CMake 和 Windows SDK 

2. MinGW-w64
   - [MinGW-w64 - for 32 and 64 bit Windows download | SourceForge.net](https://sourceforge.net/projects/mingw-w64/)
   - 下载完成后对压缩包进行解压
   - 添加环境变量
     - [MinGW-w64的安装详细步骤(c/c++的编译器gcc、g++的windows版，win10、win11真实可用）-CSDN博客](https://blog.csdn.net/qq_44918090/article/details/132190274)
     - 将 bin 路径添加到 path 变量中
   - 验证安装
     - 在 `cmd` 中 输入 `g++ -v`，查看是否有出现版本信息
3. Clang

### 1.2 安装 CMake

- [Download CMake](https://cmake.org/download/)

- 选择 `.msi` 文件，按照提示完成安装

## 2. 安装 VSCode (插件)

同 Ubuntu 下的操作

## 3. 配置VSCode



#### 3.1 编写 `CMakeLists.txt`

同 ubuntu 

#### 3.2 配置 CMake Kit

- 在 VSCode 中打开命令面板 (`Ctrl+Shift+P`)，运行 `CMake: Select a Kit`

- 选择与编译器相关的 Kit
  -  MinGW-w64 通常显示为类似 `GCC 10.2.0 x86_64-w64-mingw32`
