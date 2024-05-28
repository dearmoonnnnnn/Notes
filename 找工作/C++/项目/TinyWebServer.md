# 零、相关资料、问题和概念

## 相关资料：

- sql

  https://dev.mysql.com/doc/refman/5.7/en/what-is.html

- TinyWebSerer

  - 项目地址

    https://github.com/letMeEmoForAWhile/TinyWebServer

  - 文档

    - 微信公众号

      https://mp.weixin.qq.com/s?__biz=MzAxNzU2MzcwMw==&mid=2649274278&idx=3&sn=5840ff698e3f963c7855d702e842ec47&chksm=83ffbefeb48837e86fed9754986bca6db364a6fe2e2923549a378e8e5dec6e3cf732cdb198e2&scene=0&xtrack=1#rd

    - 一文读懂

      https://huixxi.github.io/2020/06/02/%E5%B0%8F%E7%99%BD%E8%A7%86%E8%A7%92%EF%BC%9A%E4%B8%80%E6%96%87%E8%AF%BB%E6%87%82%E7%A4%BE%E9%95%BF%E7%9A%84TinyWebServer/#more

## 概念

### 数据库相关

##### 1、MySQL

MySQL是一种关系型数据库管理系统。使用标准的SQL（Structured Query Language，结构化查询语言）进行数据管理和查询。

##### 2、连接池

**主要思想：**

- 预先创建一定数据的数据库连接，并将它们保存在一个连接池中。
- 当应用程序需要访问数据库时，不再每次都去创建新的数据库连接，而是从连接池中取出一个空闲的连接，并在使用完毕后放回连接池。

**优势：**

- 减少连接的创建和销毁开销，提高系统性能。
- 资源利用率高
  - 根据实际需求灵活管理接连的数量，使得系统资源得到更合理的利用，避免了连接过多或过少的问题
- 避免数据库资源耗尽
  - 连接池能够限制同时打开的连接数量，防止因为连接过多导致数据库资源不足或崩溃的情况发生。

##### 3、数据库连接

数据库连接是指应用程序与数据库之间的**通信通道**，用于发送查询、更新数据等操作。

**要素**：

- **连接字符串（Connection String）**
  - 包含了连接数据库所需的各种信息，如数据库服务器的地址、端口号、用户名、密码、要连接的数据库名称等。
- **连接对象（Connection Object）**
  - 是在应用程序中表示数据库连接的抽象对象。
  - 应用程序通过连接对象来建立、管理和关闭数据库连接。
  - 通常由数据库提供的API或驱动程序（如JDBC、ODBC、ADO.NET等）创建。
- **数据库会话（Database Session）**
  - 数据库会话是指应用程序与数据库之间的交互会话，包含了应用程序发送的SQL语句、数据库返回的结果集、事务信息等。
  - 每个数据库连接通常会关联一个数据库会话。

##### 4、RAII

Resource Acquisition is Initialization，资源获取即初始化。

核心思想

- 将资源的获取和释放绑定在对象的生命周期中
  - 通过构造函数获取资源
  - 通过析构函数释放资源

优点

1. 资源管理自动化
   - 减少内存泄漏和资源泄漏的风险
2. 异常安全
   - 确保资源在异常发生时也能被正确释放，从而避免资源泄漏
3. 代码简介清晰
   - 资源管理逻辑集中在构造函数和析构函数中，使得代码简洁、更易维护。

##### 5、Web Server

是一种软件或服务，运行在网络的计算机上，负责接收来自客户端的HTTP请求，并向客户端发送HTTP响应。

##### 5、CGI( Common Gateway Interface) 校验

CGI，Common Gateway Interface，通用网关接口

CGI校验是指对CGI脚本进行验证和检查，以确保脚本的安全性和可靠性。

包括以下几个方面：

- 输入验证
  - 对CGI脚本接收到的用户输入进行验证，包括参数、表单数据、请求头等。
  - 验证的目的是为了确保安全，防止恶意输入引起的安全漏洞，如SQL注入、跨站脚本攻击(XSS)、命令注入等
- 安全设置
  - 确保CGI脚本在执行时具有必要的安全设置，如运行权限、文件权限、环境变量等
- 错误处理
  - 包括捕获异常、输出错误信息、记录日志等
- 输出过滤
  - 对CGI脚本输出的内容进行过滤和清理，确保输出的数据安全可靠

**编译：**

将`.cpp`文件编译成`.cgi`文件。

**运行：**

- 运行地点：
  - `cgi`程序通常通过客户在其浏览器上点击一个`button`时运行。

- 运行内容：
  - cgi通常执行一些信息搜索、存储等任务，而且通常会生成一个动态的HTML网页来相应客户的HTTP请求。
- 项目中的`sign.cpp`就是CGI程序。
  - 它将用户请求中的用户名和密码保存在一个`id_passwd.txt`中，通过将数据库中的用户名和密码存到一个`map`中用于校验。
  - 主程序中 `execl(m_real_file, &flag, name, password, NULL);`这句命令来执行CGI文件
    - 这里的CGI程序仅用于校验，并未直接返回用户响应。

##### 6、epoll

`epoll`的名称源于`event poll`，指**事件轮询**。

- **event（事件）**
  - epoll是基于事件驱动的模型，关注文件描述符（即套接字）上的事件，如数据可读、数据可写、连接建立等。
- **poll（轮询）**
  - 传统的`poll`和`select`基于轮询的方式来检查文件描述符上是否有事件发生。
  - `epoll`更加高效，只有在事件发生时通知应用程序。

**主要特点：**

- 支持大量并发连接
  - `epoll`可以处理成千上万个并发连接，而不会随着连接数量增加导致性能下降。
- 高效的事件通知
  - 通过==事件通知==的方法告诉应用程序哪些文件描述符发生了I/O事件
- 两种触发方式
  - 边缘触发（Edge Triggered）
    - 只在文件描述符状态发生变化时通知一次
  - 水平触发（Level Triggered）
    - 会一直通知直到文件描述符的状态变为不可读或不可写
- 适用于==非阻塞I/O==
  - `epoll`通常与非阻塞I/O搭配使用，可以有效地处理大量并发连接而不阻塞线程或进程。

代码举例：使用`epoll`处理基本的多客户端连接

```c++
#include <sys/epoll.h>
#include <netinet/in.h>
#include <unistd.h>
#include <fcntl.h>
#include <iostream>

#define MAX_EVENTS 10
#define PORT 8080
#define BUFFER_SIZE 1024

// 设置文件描述符为非阻塞模式
void set_non_blocking(int fd) {
    int flags = fcntl(fd, F_GETFL, 0);
    fcntl(fd, F_SETFL, flags | O_NONBLOCK);
}

int main() {
    
    // 创建监听套接字并绑定
    int listen_fd = socket(AF_INET, SOCK_STREAM, 0);
    set_non_blocking(listen_fd);

    sockaddr_in server_addr{};
    server_addr.sin_family = AF_INET;
    server_addr.sin_addr.s_addr = INADDR_ANY;
    server_addr.sin_port = htons(PORT);
    bind(listen_fd, (sockaddr*)&server_addr, sizeof(server_addr));
    listen(listen_fd, SOMAXCONN);

   	// 创建epoll实例并添加监听套接字
    int epoll_fd = epoll_create1(0);
    epoll_event ev{}, events[MAX_EVENTS];
    ev.events = EPOLLIN;
    ev.data.fd = listen_fd;
    epoll_ctl(epoll_fd, EPOLL_CTL_ADD, listen_fd, &ev);

    // 事件循环
    while (true) {
        int nfds = epoll_wait(epoll_fd, events, MAX_EVENTS, -1);
        for (int i = 0; i < nfds; ++i) {
            if (events[i].data.fd == listen_fd) {
                while (true) {
                    sockaddr_in client_addr{};
                    socklen_t client_len = sizeof(client_addr);
                    int conn_fd = accept(listen_fd, (sockaddr*)&client_addr, &client_len);
                    if (conn_fd < 0) {
                        break;
                    }
                    set_non_blocking(conn_fd);
                    ev.events = EPOLLIN | EPOLLET;
                    ev.data.fd = conn_fd;
                    epoll_ctl(epoll_fd, EPOLL_CTL_ADD, conn_fd, &ev);
                }
            } else {
                int client_fd = events[i].data.fd;
                char buffer[BUFFER_SIZE];
                while (true) {
                    ssize_t count = read(client_fd, buffer, sizeof(buffer));
                    if (count <= 0) {
                        close(client_fd);
                        break;
                    }
                    write(client_fd, buffer, count);
                }
            }
        }
    }

    close(listen_fd);
    close(epoll_fd);
    return 0;
}

```

- 使用`epoll`进行多路复用以处理多个客户端连接
- 包括创建监听套接字、绑定端口、设置非阻塞模式、创建`epoll`实例、添加文件描述符到`epoll`实例、事件循环以及处理客户端连接和数据。

##### 7、线程池

预先创建一组可复用的线程，并将它们放在池中待命。

- 当有新的任务需要执行时，不是立即创建新的线程，而是从线程池中取出一个空闲线程来执行任务。
- 完成任务时，线程不被销毁，而是返回池中，等待下一个任务。

##### 8、线程

线程是进程中的进程，是资源分配的最小单位。

常见的线程实现包括POSIX线程（pthread）、Windows线程（Win32线程）、java线程等。

如何选择使用哪一种线程

1、考虑平台兼容性

如果程序需要跨平台运行，可以使用POSIX线程（pthead）和 C++11 标准中的`<thread>`头文件提供的线程库

2、考虑编程语言和框架

- 使用C/C++编程，使用POSIX线程（pthread）或`<thread>`头文件提供的线程库。
- 如果是java，使用java的线程实现

### http相关

##### 1、有限状态机（State Machine）

把**有限个变量**描述的状态变化过程，以==可构造可验证==的方式呈现出来。比如，封闭的有向图。

带有状态转移的有限状态机示例代码。

```c++
STATE_MACHINE(){
    State cur_State = type_A;
    while(cur_State != type_C){
        Package _pack = getNewPackage();
        switch(){
            case type_A:
                process_pkg_state_A(_pack);
                cur_State = type_B;
                break;
            case type_B:
                process_pkg_state_B(_pack);
                cur_State = type_C;
                break;
        }
    }
}
```

- 该状态机包含三种状态
  - type_A
    - 初始状态
  - type_B
  - type_C
    - 结束状态
- 状态机当前状态记录在`cur_state`中，逻辑处理时，状态机先通过getNewPackage获取数据包，然后根据当前状态对数据进行处理，最后改变cur_state完成状态转移。

##### 2、http报文格式

http报文分为两种，必须按照特有的格式才能被识别。

- 请求报文，包含四个部分
  - 请求行（request line）
  - 请求头部（header）
  - 空行
  - 请求数据，两种
    - GET
    - POST
- 响应报文，四个部分
  - 状态行
  - 消息报头
  - 空行
  - 响应正文

## 问题

##### 1、一共有几个线程，分别有什么作用

##### 2、同步日志和异步日志的区别

**同步日志：**

- 当调用`log`方法时，程序会立即执行日志写入操作，并等待该操作完成后再执行下一行代码。
- **示例**：
  - 大多数基本的 `console.log()`（在JavaScript中）或类似的简单日志输出函数通常表现为同步行为。调用 `console.log('Hello, World!')` 会立即打印消息到控制台，然后程序继续执行下一条语句。

**异步日志：**

- 当调用`log`方法时，程序不会等待日志操作写入完成，而是立刻返回控制权给调用者，执行下一行代码。
- 实际的日志写入操作会在==后台线程、事件循环==或其他异步机制管理下完成

**区别：**

- 同步日志的日志写入操作会立即执行，会阻塞当前执行流。

- 异步日志的写入操作被安排在后台执行，不会阻塞当前执行流。日志写入的实际完成通过==非阻塞方式==通知调用者。

  - 非阻塞通知手段：

    - 回调函数（callback）

      日志写入完成时，预先注册的回调函数被异步调用，通知调用者日志操作已完成。

    - 承诺（Promise）

      如果日志库支持Promise API，调用者可以使用`.then()`、`.catch()`等方法指定在日志写入成功或失败时执行的回调。尽管这些回调也是异步的，但相比于传统的回调函数，Promise提供了更优雅的链式调用和错误处理机制。调用者发起日志写入后，会立即获得一个Promise对象，然后可以继续执行其他代码。Promise在日志写入完成后会改变状态（resolve或reject），触发相应的回调。

    - 事件（Event）

      在某些编程环境中，日志库可能会触发特定的事件（如`'logWritten'`），并在事件处理器（event handler）中处理日志写入完成的通知。调用者订阅该事件后，可以继续执行其他任务。当日志写入完成时，系统会触发事件，调用者注册的事件处理器会被调用，从而得知日志已写入完成。

    - 异步编程模式（Future、CompletionStage、async/await）

##### 3、阻塞队列的作用

实现生产者-消费者（Producer-Consumer）模式的经典工具。在这种模式中，生产者线程负责生成数据并将其放入队列，消费者线程则负责从队列中取出数据进行处理。阻塞队列自动处理线程间的同步，确保生产者不会在队列满时继续添加数据，消费者也不会在队列空时尝试取数据，从而实现了生产者和消费者之间的解耦和高效协作。

##### 4、异步机制都有哪些

# 一、环境搭建

- ubuntu20.04
- MySQL 8.0

项目教程里的环境为ubuntu16.04+MySQL 5.7.29，但后者已经不兼容ubuntu20.04

## 1、安装MySQL

在Ubuntu 20.04上安装MySQL的步骤如下：

##### 1、更新软件包列表：

```
sudo apt update
```

##### 2、安装MySQL服务器：

```
sudo apt install mysql-server
```
在安装过程中，系统会提示您设置MySQL root用户的密码。

##### 3、安装完成后，MySQL服务将自动启动。您可以通过以下命令检查MySQL服务的状态：

```
sudo systemctl status mysql
```

##### 4、为了提高MySQL的安全性，运行以下命令并按照提示进行安全配置：

```
sudo mysql_secure_installation
```
这个脚本会引导您完成一些安全相关的设置，如设置root密码、删除匿名用户、禁止root用户远程登录等。

##### 5、现在，您可以使用以下命令以root用户身份登录MySQL：

```
sudo mysql
```

##### 6、如果您想要允许远程访问MySQL，需要编辑MySQL配置文件：

```
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
找到 `bind-address` 行，将其注释掉或更改为 `0.0.0.0`。

##### 7、重启MySQL服务使更改生效：

```
sudo systemctl restart mysql
```

现在，您已经在Ubuntu 20.04上成功安装并配置了MySQL。您可以开始创建数据库、表，并插入数据了。记住要为您的应用程序创建单独的MySQL用户并授予适当的权限，而不是一直使用root用户。

# 二、项目架构

![img](https://camo.githubusercontent.com/326c456073716c6a81d925154df43a7787cf4088b794590c76a0f122274e7ef4/687474703a2f2f7777312e73696e61696d672e636e2f6c617267652f303035544a3263376c79316765306a3161747135686a33306736306c6d3077342e6a7067)

## 文件架构：

##### CGImysql

- sql_connection_pool.cpp
- sql_connection_pool.h

##### http

- http_conn.cpp
- http_conn.h

##### lock

- locker.h

##### log

- block_queue.h
- log.cpp
- log.h

##### ==root==

- 

##### test_pressure

- webbench-1.5

##### threadpool

- threadpool.h

##### timer

- lst_timer.cpp
- lst_timer.h

##### config.cpp

##### config.h

##### main.cpp

##### webserver.cpp

##### webserver.h



# 三、源码解析

## 0、main

设置数据库用户名、密码、数据库名称

命令行解析，得到相关参数的值

实例化`WebServer`对象

- 初始化
  - 传递相关参数的值给WebServer对象
- 日志
- 数据库
- 线程池
- 触发模式
- 监听
- 运行

## 1、webserver类

### A、成员变量

##### 1、基础变量

|    变量名称     | 变量类型 |       描述        |
| :-------------: | :------: | :---------------: |
|    ` m_port`    |  `int`   |     监听端口      |
|    `m_root`     | `char *` |    网站根目录     |
| ` m_log_write`  |  `int`   |   日志写入方式    |
| ` m_close_log`  |  `int`   |   日志是否关闭    |
| ` m_actormodel` |  `int`   |     模型选择      |
| ` m_pipefd[2]`  |  `int`   |  管道文件描述符   |
|  ` m_epollfd`   |  `int`   | `epoll`文件描述符 |

##### 2、数据库相关变量

-  `connection_pool *m_connPool`

- `string m_user`
  - 登陆数据库用户名

- `string m_passWord`
  - 登陆数据库密码

- `string m_databaseName`
  - 使用数据库名

- `int m_sql_num`
  - 数据库连接池数量

##### 3、线程池相关变量

|    变量名称    |         变量类型          |         描述         |
| :------------: | :-----------------------: | :------------------: |
|    `m_pool`    | `threadpool<http_conn> *` | 线程池实例化对象指针 |
| `m_thread_num` |           `int`           |      线程的数量      |

##### 4、epoll_event

|      变量名称      |   变量类型    |                    描述                    |
| :----------------: | :-----------: | :----------------------------------------: |
|      `events`      | `epoll_event` |            epoll实例化对象数组             |
|    `m_listenfd`    |     `int`     |                 线程的数量                 |
|   `m_OPT_LINGER`   |     `int`     | 在关闭套接字时是否等待未发送数据被发送完毕 |
|    `m_TRIGMode`    |     `int`     |                  触发模式                  |
| `m_LISTENTrigmode` |     `int`     |                  监听模式                  |
|  `m_CONNTrigmode`  |     `int`     |                  连接模式                  |

##### 5、定时器相关

|   变量名称    |    变量类型     | 描述 |
| :-----------: | :-------------: | :--: |
| `users_timer` | `client_data *` |      |
|    `utils`    |     `Utils`     |      |

### B、成员函数

##### 1、WebServer()

- 描述
  - 构造函数，创建`http_conn`类对象`users`

- 关键变量
  - `users`
    - `http_conn`类对象
  - `m_root`
    - 当前工作目录与 `/root`拼接而成的路径
  - `users_timer`
    - 定时器

##### 2、~WebServer()

- 描述
  - 析构函数
  - 关闭`epoll`、监听套接字和两个管道
  - 释放内存变量的内存空间：`users`、`users_timer`、`m_pool`

##### 3、init()

- 描述
  - 传递参数
- 参数
  
  |    变量名称    | 变量类型 |                  描述                   |
  | :------------: | :------: | :-------------------------------------: |
  |     `port`     |  `int`   |             服务器监听端口              |
  |     `user`     | `string` |           数据库连接的用户名            |
  |   `passWord`   | `string` |            数据库连接的密码             |
  | `databaseName` | `string` |               数据库名称                |
  |  `log_write`   |  `int`   |   日志写入设置，控制日志是否写入磁盘    |
  |  `opt_linger`  |  `int`   | ==SO_LINGER选项设置，控制连接关闭行为== |
  |   `trigmode`   |  `int`   | ==服务器触发模式，如轮询、边缘触发等==  |
  |   `sql_num`    |  `int`   |          SQL连接池中的连接数量          |
  |  `thread_num`  |  `int`   |            服务器工作线程数             |
  |  `close_log`   |  `int`   |            是否关闭日志功能             |
  | `actor_model`  |  `int`   |    是否使用==actor模型==进行并发处理    |

##### 4、trig_mode()

- 描述
  - 设置触发模式
- 关键变量
  - `m_TRIGMode`
    - 根据`m_TRIGMode`的值设置不同的触发模式
  - `m_LISTENTrigmode`
    - 监听模式
  - `m_CONNTrigmode`
    - 连接模式

##### 5、log_write()

- 描述
  - `m_close_log`为0则对日志示例进行初始化
- 无参数
- 无返回值

##### **6、**sql_pool**()**

- 描述
  - 数据库连接池函数，管理数据库连接

##### 7、thread_pool()

- 描述
  - 线程池

##### 8、eventListen()

- 描述
  - 启动服务器的监听进程。

## 2、log类

### 日志系统

两个模块：

- 日志模块
- 阻塞队列模块
  - 完成异步写入日志的问题

关键内容

- 自定义阻塞队列
- 单例模式创建日志
- 同步日志
- 异步日志
- 实现按天、超行分类

### A、成员变量

均为private

|    变量名称     |        变量类型         |                描述                |
| :-------------: | :---------------------: | :--------------------------------: |
| `dir_name[128]` |         `char`          |               路径名               |
| `log_name[128]` |         `char`          |             log文件名              |
| `m_split_lines` |          `int`          |            日志最大行数            |
|    `m_count`    |       `long long`       |      日志计数器，日志行数记录      |
|    `m_today`    |          `int`          | 因为按天分类，记录当前时间是哪一天 |
|     `m_fp`      |        `FILE *`         |         打开log的文件指针          |
|     `m_buf`     |        `char *`         |       指向缓冲区的字符型指针       |
|  `m_log_queue`  | `block_queue<string> *` |              阻塞队列              |
|  `m_is_async`   |         `bool`          |           是否异步标志位           |
|    `m_mutex`    |       `locker`类        |                                    |
|  `m_close_log`  |          `int`          |          关闭日志的标志位          |

### B、成员函数

##### **1、Log()**

- 描述
  - 构造函数
  - 设置日志技术器为0
  - 异步标志为false

##### **2、~Log()**

- 描述
  - 析构函数
  - 关闭日志文件

##### **3、init**

- 描述
  - 初始化多个参数
    - `max_queue_size`大于1，则设置为异步



## 2.1、block_queue,阻塞队列

循环数组实现的阻塞队列，`m_back = (m_back + 1) % m_max_size`

线程安全:每个操作前都要先加互斥锁，操作完后，再解锁

### A、成员变量

|  变量类型  |   变量名称   |        描述        |
| :--------: | :----------: | :----------------: |
| `locker`类 |  `m_mutex`   | `locker`实例化对象 |
|  `cond`类  |   `m_cond`   |  `cond`实例化对象  |
|   `T *`    |  `m_array`   |       模板类       |
|   `int`    |   `m_size`   |    队列当前长度    |
|   `int`    | `m_max_size` |    队列最大长度    |
|   `int`    |  `m_front`   |      队首位置      |
|   `int`    |   `m_back`   |      队尾位置      |

### B、成员函数

##### **1、block_queue()**

- 描述
  - 构造函数
- 参数
  - `int max_size`
    - 队列最大长度
    - 默认为1000

## 3、CGImysql

定义两个类：

### connection_pool类

- 梅耶单例模式，保证唯一
- list实现连接池
- 连接池为静态大小
- 互斥锁实现线程安全

#### A、成员变量

##### 私有：

|   变量名称   |    变量类型     |        描述        |
| :----------: | :-------------: | :----------------: |
| `m_MaxConn`  |      `int`      |     最大连接数     |
| `m_CurConn`  |      `int`      | 当前已使用的连接数 |
| `m_FreeConn` |      `int`      |  当前空闲的连接数  |
|    `lock`    |   `locker`类    |     互斥锁对象     |
|  `connList`  | `list<MYSQL *>` |       连接池       |
|  `reserve`   |     ==sem==     |       信号量       |

##### 公有：

|     变量名称     | 变量类型 |       描述       |
| :--------------: | :------: | :--------------: |
|     `m_url`      | `string` |     主机地址     |
|     `m_Port`     | `string` |   数据库端口号   |
|     `m_User`     | `string` | 登陆数据库用户名 |
|   `m_PassWord`   | `string` |  登陆数据库密码  |
| `m_DatabaseName` | `string` |   使用数据库名   |
|  `m_close_log`   |  `int`   |     日志开关     |

#### B、成员函数

##### 1、connection_pool()

- 描述
  - 构造函数
  - 当前已使用的连接数和当前空闲的连接数设置为0

##### 2、GetInstance()

- 描述
  - 梅耶单例模式
- 返回值
  - `&connPool`，静态实例的引用

##### 3、init()

- 描述
  - 初始化数据库连接参数，即上述公有变量
  - 循环创建指定数量的数据库连接
  
- 输入参数

  |  变量名称   | 变量类型 |             描述              |
  | :---------: | :------: | :---------------------------: |
  |    `url`    | `string` |         数据库的`url`         |
  |   `User`    | `string` |      连接数据库的用户名       |
  | `PassWord`  | `string` |       连接数据库的密码        |
  |  `DBName`   | `string` |       要连接的数据库名        |
  |   `Port`    |  `int`   |         数据库端口号          |
  |  `MaxConn`  |  `int`   |      连接池中最大连接数       |
  | `close_log` |  `int`   | 是否关闭日志记录，0表示不关闭 |

##### 4、GetConnection()

- 描述：
  - 当有请求时，从数据库连接池中返回一个可用连接，更新使用和空闲连接数。
- 相关参数
  - `reserve`
    - 信号量实例
- 返回值
  - con
    - MYSQL 指针，指向一个MYSQL数据库的连接实例。

##### 5、ReleaseConnection()

- 描述
  - 释放当前使用的连接
  - connList
- 参数
  - `con`
    - `MYSQL`指针
- 返回值
  - `bool`
    - `true`：释放成功

##### 6、DestoryPool()

- 描述
  - 销毁数据库连接池
    - 遍历数据库连接池，对每个连接执行`mysql_close()`

##### 7、GetFreeConn()

- 描述
  - 获取当前的空闲连接数

##### 8、~connection_pool()

- 描述
  - 析构函数
    - 执行`DestroyPool()`

### connectionRAII类

#### 成员变量

| 变量名称 |     变量类型      |        描述        |
| :------: | :---------------: | :----------------: |
| conRAII  |      MYSQL *      | 数据库连接对象指针 |
|   pool   | connection_pool * |   连接池对象指针   |

##### 9、connectionRAII()

- 描述
  - 构造函数
    - 创建对象时，从指定的数据库连接池中获取一个连接。

##### 10、~connectionRAII()

- 描述
  - 析构函数
    - 销毁对象时，将连接放回数据库连接池。

## 4、http连接处理类

##### 流程图

![图片](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/640)

##### 通过**主从状态机**封装http连接类：

- 主状态机
  - 在内部调用从状态机

- 从状态机
  - 将处理状态和数据传给主状态机


##### 流程：

1. 客户端发出http连接请求。
2. **从状态机**读取数据，更新自身状态和接收数据，传给**主状态机**。
3. 主状态机根据从状态机状态，更新自身状态，决定相应请求还是继续读取。

### A、成员变量

##### public

|      变量名称       |      变量类型      |                             描述                             |
| :-----------------: | :----------------: | :----------------------------------------------------------: |
|   `FILENAME_LEN`    | `static const int` |                       文件名的最大长度                       |
| `READ_BUFFER_SIZE`  | `static const int` |                        读缓冲区的大小                        |
| `WRITE_BUFFER_SIZE` | `static const int` |                        写缓冲区的大小                        |
|      `METHOD`       |       `enum`       |     `HTTP`请求方法的枚举<br />eg, `GET`、`POST`、`HEAD`      |
|    `CHECK_STATE`    |       `enum`       | 检查HTTP连接状态的枚举<br />eg, `CHECK_STATE_REQUESTLINE`、`CHECK_STATE_HEADER` |
|     `HTTP_CODE`     |       `enum`       | HTTP响应状态码的枚举<br />eg, `NO_REQUEST`、`GET_REQUEST`、`BAD_REQUEST` |
|    `LINE_STATUS`    |       `enum`       | 行状态的枚举，用于检查HTTP请求或响应的行是否有效<br />eg,  `LINE_OK`、`LINE_BAD`、`LINE_OPEN` |

##### public

|    变量名称    |   变量类型   |                   描述                    |
| :------------: | :----------: | :---------------------------------------: |
|  `m_epollfd`   | `static int` | `epoll`文件描述符，用于监听所有连接的事件 |
| `m_user_count` | `static int` |          当前使用该类的用户数量           |
|    `mysql`     |  `MYSQL *`   |              `MYSQL`对象指针              |
|   `m_state`    |    `int`     |               读为0，写为1                |

##### private

|             变量名称             |         变量类型          |                      描述                       |
| :------------------------------: | :-----------------------: | :---------------------------------------------: |
|            `m_sockfd`            |           `int`           |                套接字文件描述符                 |
|           `m_address`            |    `sockadd_in` 结构体    |              存储客户段的地址信息               |
|  `m_read_buf[READ_BUFFER_SIZE]`  |        `char` 数组        |       读取缓存区，存储从客户端接收的数据        |
|           `m_read_idx`           |          `long`           |   读取索引，用于标记当前在读取缓存区中的位置    |
|         `m_checked_idx`          |          `long`           |                    检查索引                     |
|          `m_start_line`          |           `int`           |                   起始行索引                    |
| `m_write_buf[WRiTE_BUFFER_SIZE]` |        `char` 数组        |       写入缓存区，存储要发送给客户的数据        |
|          `m_write_idx`           |           `int`           |                    写入索引                     |
|         `m_check_state`          |   `CHECK_STATE`枚举类型   |      检查状态，用于标记数据检查的当前状态       |
|            `m_method`            |     `METHOD`枚举类型      |                 HTTP请求的方法                  |
|   `m_real_file[FILENAME_LEN]`    |        `char` 数组        |                存储实际文件路径                 |
|             `m_url`              |         `char *`          |               指向URL字符串的指针               |
|           `m_version`            |         `char *`          |            指向HTTP版本字符串的指针             |
|             `m_host`             |         `char *`          |             指向主机名字符串的指针              |
|        `m_content_length`        |          `long`           |     内容长度变量，用于表示HTTP请求体的长度      |
|            `m_linger`            |          `bool`           |      linger标志，==用于控制连接关闭行为==       |
|         `m_file_address`         |         `char *`          |             指向文件存储地址的指针              |
|          `m_file_state`          |   `struct stat` 结构体    |             用于存储文件的状态信息              |
|            `m_iv[2]`             | `struct iovec` 结构体数组 |        用于存储要发送的多个数据块的信息         |
|           `m_iv_cont`            |           `int`           | `iovec`计数，用于表示`m_iv`中已填充的数据块数量 |
|              `cgi`               |           `int`           |        `cgi`标志，用于表示是否启用`POST`        |
|            `m_string`            |         `char *`          |                 存储请求头数据                  |
|         `bytes_to_send`          |           `int`           |                 需要发送字节数                  |
|        `bytes_have_send`         |           `int`           |                  已发送字节数                   |
|            `doc_root`            |         `char *`          |              文档根目录字符串指针               |
|            `m_users`             |   `map<string, string>`   |    用户映射，用于存储用户名和密码的映射关系     |
|           `m_TRIGMode`           |           `int`           |   `TRIG`模式标志，用于控制某种特定的处理模式    |
|          `m_close_log`           |           `int`           |              控制是否关闭日志记录               |
|         `sql_user[100]`          |        `char` 数组        |             定义`SQL`数据库的用户名             |
|        `sql_passwd[100]`         |        `char`数组         |              定义`SQL`数据库的密码              |
|         `sql_name[100]`          |        `char` 数组        |              定义`SQL`数据库的名称              |

### B、成员函数

#### public

##### 1、init()有参

- 描述
  - 初始化连接，外部调用初始化套接字地址
  - 参数初始化
  - 最后调用无参`init()`
- 参数
  - `sockfd`
  - `addr`
  - `root`
  - `TRIGMode`
  - `close_log`
  - `user`
  - `passwd`
  - `sqlname`
- 返回值
  - 无


##### 2、close_conn()

- 描述
  - 关闭一个连接，客户总量减一
- 返回值
  - 无


##### 3、==process()==

- 描述
  - 处理HTTP连接的请求
  - 调用`process_read()`，尝试读取HTTP请求
    - 如果没有读到请求，将此连接标记为可读
  - 调用`process_write()`，尝试写操作
    - 若写操作失败，关闭此连接
  - 无论写操作成功与否，最后都标记此连接为可写，调用`modfd()`
- 返回值
  - 无


##### 4、read_once()

- 描述
  - 循环读取客户数据，直到无数据可读或对方关闭连接
  - 非阻塞ET工作模式下，需要一次性将数据读完
- 返回值
  - `bool`

##### 5、write()

- 描述











