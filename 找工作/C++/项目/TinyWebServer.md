# 官方文档：

https://dev.mysql.com/doc/refman/5.7/en/what-is.html

# 零、问题和概念

# 概念

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

##### 5、CGI



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

## 1、连接池

### sql_connection_pool.h

定义两个类：

- connection_pool
- connectionRAII