# 官方文档：

https://dev.mysql.com/doc/refman/5.7/en/what-is.html

# 零、问题和概念

## 概念

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

##### 4、Web Server

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

是linux中用于处理大量并发事件的I/O多路复用机制。

`epoll`的命名包含了两个关键词：`event`和`poll`。

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

##### 7、线程池

预先创建一组可复用的线程，并将它们放在池中待命。

- 当有新的任务需要执行时，不是立即创建新的线程，而是从线程池中取出一个空闲线程来执行任务。
- 完成任务时，线程不被销毁，而是返回池中，等待下一个任务。

## 问题

##### 1、一共有几个线程，分别有什么作用

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

| 变量类型 |    变量名称     |       描述        |
| :------: | :-------------: | :---------------: |
|  `int`   |    ` m_port`    |     监听端口      |
| `char *` |    `m_root`     |    网站根目录     |
|  `int`   | ` m_log_write`  |   日志写入方式    |
|  `int`   | ` m_close_log`  |   日志是否关闭    |
|  `int`   | ` m_actormodel` |     模型选择      |
|  `int`   | ` m_pipefd[2]`  |  管道文件描述符   |
|  `int`   |  ` m_epollfd`   | `epoll`文件描述符 |

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

|         变量类型          |    变量名称    |         描述         |
| :-----------------------: | :------------: | :------------------: |
| `threadpool<http_conn> *` |    `m_pool`    | 线程池实例化对象指针 |
|           `int`           | `m_thread_num` |      线程的数量      |

##### 4、epoll_event

|   变量类型    |      变量名称      |                    描述                    |
| :-----------: | :----------------: | :----------------------------------------: |
| `epoll_event` |      `events`      |            epoll实例化对象数组             |
|     `int`     |    `m_listenfd`    |                 线程的数量                 |
|     `int`     |   `m_OPT_LINGER`   | 在关闭套接字时是否等待未发送数据被发送完毕 |
|     `int`     |    `m_TRIGMode`    |                  触发模式                  |
|     `int`     | `m_LISTENTrigmode` |                  监听模式                  |
|     `int`     |  `m_CONNTrigmode`  |                  连接模式                  |

##### 5、定时器相关

|    变量类型     |   变量名称    | 描述 |
| :-------------: | :-----------: | :--: |
| `client_data *` | `users_timer` |      |
|     `Utils`     |    `utils`    |      |

### B、成员函数

**1、WebServer()**

- 描述
  - 构造函数，创建`http_conn`类对象`users`

- 关键变量
  - `users`
    - `http_conn`类对象
  - `m_root`
    - 当前工作目录与 `/root`拼接而成的路径
  - `users_timer`
    - 定时器

**2、~WebServer()**

- 描述
  - 析构函数
  - 关闭`epoll`、监听套接字和两个管道
  - 释放内存变量的内存空间：`users`、`users_timer`、`m_pool`

**3、init()**

- 描述
  - 传递参数
- 参数
  - `port`
    - 参数类型：`int`
    - 服务器监听端口
  - `user`
  - passWord
  - databaseName
  - log_write
  - opt_linger
  - trigmode
  - sql_num
  - thread_num
  - close_log
  - actor_model

**4、trig_mode()**

- 描述
  - 设置触发模式
- 关键变量
  - `m_TRIGMode`
    - 根据`m_TRIGMode`的值设置不同的触发模式
  - `m_LISTENTrigmode`
    - 监听模式
  - `m_CONNTrigmode`
    - 连接模式

**5、log_write()**

- 描述
  - `m_close_log`为0则对日志示例进行初始化
- 无参数
- 无返回值

**6、sql_pool()**

- 

## 2、log类

### A、成员变量

均为private

|        变量类型         |    变量名称     |                描述                |
| :---------------------: | :-------------: | :--------------------------------: |
|         `char`          | `dir_name[128]` |               路径名               |
|         `char`          | `log_name[128]` |             log文件名              |
|          `int`          | `m_split_lines` |            日志最大行数            |
|       `long long`       |    `m_count`    |      日志计数器，日志行数记录      |
|          `int`          |    `m_today`    | 因为按天分类，记录当前时间是哪一天 |
|        `FILE *`         |     `m_fp`      |         打开log的文件指针          |
|        `char *`         |     `m_buf`     |       指向缓冲区的字符型指针       |
| `block_queue<string> *` |  `m_log_queue`  |              阻塞队列              |
|         `bool`          |  `m_is_async`   |           是否异步标志位           |
|       `locker`类        |    `m_mutex`    |                                    |
|          `int`          |  `m_close_log`  |          关闭日志的标志位          |

### B、成员函数

**1、Log()**

- 描述
  - 构造函数
  - 设置日志技术器为0
  - 异步标志为false

**2、~Log()**

- 描述
  - 析构函数
  - 关闭日志文件

**3、init**

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

**1、block_queue()**

- 描述
  - 构造函数
- 参数
  - `int max_size`
    - 队列最大长度
    - 默认为1000

## 3、sql_connection_pool.h

定义两个类：

- connection_pool
- connectionRAII