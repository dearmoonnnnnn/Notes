# 零、相关资料、问题和概念

## 相关资料

##### 视频：

https://www.bilibili.com/video/BV1d841117SH?p=2&vd_source=ddc1bcb1cabb9c324d56d94a9b4b6e93

##### 文档：

http://www.seestudy.cn/?list_9/31.html

## 问题

##### 1、线程和进程的区别

1. **定义**
   - **进程（Process）**是程序的一次执行过程，是**程序**在执行时分配和管理资源的基本单位。每个进程拥有**独立的内存空间**和**系统资源**。
     - 系统资源
       - 硬件：CPU、内存、硬盘、网络接口、输入输出
       - 软件：文件系统、系统库和服务(C/C++库、图形界面库等等)
   - **线程（Thread）**是进程内部的一个独立执行单元，也是CPU调度的基本单位。同一个进程中的多个线程**共享进程的内存空间和系统资源**
2. **资源分配**
   - **进程**有独立的内存空间和系统资源，不同进程之间的内存和资源相互独立。
   - **线程**共享一个进程的内存空间和系统资源，所以线程之间的通信和数据共享更加方便。
3. **创建和销毁**
   - **进程**的创建更加消耗资源，因为需要分配独立的内存空间和系统资源。
     - 通常调用操作系统的系统调用（例如`fork()`）来创建进程。
   - **线程**的创建不需要额外的内存空间和系统资源，只需要在进程内部**分配线程的控制块**。
     - C++11及以上版本，可以使用`<thread>`头文件提供的线程库来创建和管理。
4. **调度和同步**
   - **进程**之间的调度由**操作系统**负责，调度单位是进程。
   - **线程**的调度由进程内部的**线程调度器**负责，调度单位是线程。
     - 线程之间的同步通常由**线程同步机制**来实现，包括互斥锁、信号量、条件变量等。

##### 2、为什么要使用（多）线程

提高进程的运行效率

##### 3、最多可以开几个线程取决于什么

CPU的核数

## 定义：

##### 1、进程

进程就是运行中的程序。

##### 2、线程

线程就是进程中的进程。

# 一、线程库的基本使用

## 1、创建线程

##### 基本语法：

```c++
#include <thread>
std::thread t(function_name, args...);
```

- `function_name`
  - 线程入口点的函数或可调用对象
- `args...`
  - 传递给函数的参数

##### 举例：

```c++
#include <iostream>
#include <thread>
void print_message() 
{    
    std::cout << "Hello, world!" << std::endl;
}

int main() 
{    
    std::thread t(print_message);
    t.join();
    return 0;
}
```

## 2、传递参数

thread中的第二个参数来给函数传递参数

```c++
#include <iostream>
#include <thread>
void print_message(const std::string& message) 
{    
	std::cout << message << std::endl;
}

void increment(int& x) 
{    
	++x;
}

int main() 
{    
	std::string message = "Hello, world!";    
	std::thread t(print_message, message);    
	t.join();    
	
	int x = 0;   
    // 传递引用
	std::thread t2(increment, std::ref(x));    
	t2.join();    
	std::cout << x << std::endl;    

	return 0;
	
}
```

## 3、等待子线程完成

##### 动机：

若子线程还没完成，主线程（main）就结束了，可能会报错。

因此主线程需要等待子线程完成后再继续执行。

##### 举例：

```c++
#include <iostream>
#include <thread>

void print_message(const std::string& message) 
{    
    std::cout << message << std::endl;
}

int main() 
{    
    std::thread t1(print_message, "Thread 1");   
    std::thread t2(print_message, "Thread 2");    
    t1.join();   // 阻塞，线程t1执行完成后，主线程继续  
    t2.join();    
    std::cout << "All threads joined" << std::endl;   
    
    return 0;
}
```

## 4、分离线程

##### 动机：

有时候不需要等待线程完成，而是希望它在后台运行。

分离线程后，主线程结束，子线程未结束时也不会报错。

##### 举例：

```c++
#include <iostream>
#include <thread>
void print_message(const std::string& message) 
{    
    std::cout << message << std::endl;
}

int main() 
{    
    std::thread t(print_message, "Thread 1");    
    t.detach();    
    std::cout << "Thread detached" << std::endl;    
    return 0;
}
```

## 5、joinable()

##### 动机：

如果我们试图对一个不可调用`join()`或`detach()`的线程调用`join()`或`detach()`，则会抛出一个`std::system_error`异常。

因此在调用`join()`或`detach()`之前使用`joinable()`判断

##### 举例：

```c++
#include <iostream>
#include <thread>

void foo() {
    std::cout << "Thread started" << std::endl;
}

int main() {
    std::thread t(foo);
    if (t.joinable()) {
        t.join();
    }
    std::cout << "Thread joined" << std::endl;
    return 0;
}
```

