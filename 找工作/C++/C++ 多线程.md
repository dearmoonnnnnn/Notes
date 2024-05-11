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

##### 4、C++中`<thread>`库和`<pthread>`库的区别

1. **接口风格**
   - `<thread>`库是C++标准库的一部分，提供了面向对象的多线程接口，包括`std::thread`类和相关的线程管理函数。
   - `pthread`是C语言的库，使用起来更加面向过程，需要使用函数来创建和管理线程，如`pthread_create`、`pthread_join`等。
2. **使用平台**
   - `<thread>`库是C++11标准引入的，在支持C++11标准的编译器和操作系统上可以使用，更加跨平台。
   - pthread是POSIX标准的一部分，可以在支持POSIX的操作系统上使用，如Linux、Unix
      - POSIX：
         - Portable Operating System Interface for Unix，可移植操作系统接口，为了定义操作系统接口和编程环境的规范。
         - POSIX标准的目的是提供一个统一的编程接口，便于开发人员编写可移植的应用程序，这些应用程序在符合POSIX标准的系统上都可以运行。

3. **标准支持：**
   - `<thread>`库是C++11标准的一部分，受到C++标准的约束和规范。
   - pthread是POSIX标准的一部分，受到POSIX标准的约束和规范。


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

# 二、线程函数中的数据未定义错误

## 1、传递临时变量的问题

##### 问题描述：

```c++
#include <iostream>
#include <thread>
void foo(int& x) {
    x += 1;
}
int main() {
    std::thread t(foo, 1); // 传递临时变量
    t.join();
    return 0;
}
```

- 函数`foo`接收一个整数引用作为参数，并将该引用加1。
- 然后，我们创建一个名为`t`的线程，将`foo`函数和一个临时变量`1`作为参数传递给它。
- 线程函数执行时，临时变量`1` 被销毁，从而导致**未定义行为**。

##### 解决方案：

将变量复制到一个持久的对象中，然后将该对象传递给线程。

```c++
#include <iostream>
#include <thread>
void foo(int& x) {
    x += 1;
}
int main() {
    int x = 1; // 将变量复制到一个持久的对象中
    std::thread t(foo, std::ref(x)); // 将变量的引用传递给线程
    t.join();
    return 0;
}
```

## 2、传递指针或引用指向局部变量的问题

##### 问题描述1：临时变量的情况

```c++
#include<iostream>
#include<thread>

std::thread t;
void foo(int& x){
	X += 1;
}

void test(){
	int a = 1;
	t = std::thread(foo, std::ref(a));
}

int main()
{
	test();
	t.join();
	return 0;
}
```

- `a`是`test`函数中的局部变量，只在test中有效，存在于栈中。
- 在线程里无法取到a的地址，报错

##### 问题描述2：指针的情况

```c++
#include<iostream>
#include<thread>

std::thread t;
void foo(int *x){
	std::cout << *x << std::endl;
}

int main()
{
	int* ptr = new int(1);
	std::thread t(foo, ptr);
    delete ptr;
    
    t.join();
    return 0;

}
```

- 将ptr传递给foo函数时，ptr已经被释放，指向的内存未知。
- 容易报错，或者得到错误的输出。

##### 问题描述3：类对象的情况

```c++
#include <iostream>
#include <thread>

class MyClass {
public:
    void func() {
        std::cout << "Thread " << std::this_thread::get_id() 
        << " started" << std::endl;
        // do some work
        std::cout << "Thread " << std::this_thread::get_id() 
        << " finished" << std::endl;
    }
};

int main() {
    MyClass obj;
    std::thread t(&MyClass::func, &obj);
    // obj 被提前销毁了，会导致未定义的行为
    t.join();
    return 0;
}
```

- obj对象在子线程未执行完就被销毁，导致程序崩溃或产生未定义的行为

##### 解决方案1：

使用全局变量，不要使用临时变量。

##### 解决方案2：使用智能指针

```c++
#include <iostream>
#include <thread>
#include <memory>

class MyClass {
public:
    void func() {
        std::cout << "Thread " << std::this_thread::get_id() 
        << " started" << std::endl;
        // do some work
        std::cout << "Thread " << std::this_thread::get_id() 
        << " finished" << std::endl;
    }
};

int main() {
    // 创建指向MyClass对象的指针
    std::shared_ptr<MyClass> obj = std::make_shared<MyClass>();
    std::thread t(&MyClass::func, obj);
    t.join();
    return 0;
}
```

- 当obj不被需要时，智能指针会自动调用析构函数，释放内存。

**提问：为什么这里传入了obj作为参数?**

- 因为线程的回调函数`func`是类的成员函数，类的成员函数有一个隐藏的变量this，表示指向实例化对象本身的指针。
- 这里obj就作为this传入。

## 3、入口函数为类的私有成员函数

##### 问题描述：

```c++
#include<iostream>
#include<thread>
#include<memory>

class A {
	private:
    	void foo(){
    		std::cout << "Hello World" << std::endl;
    	}
}

void thread_foo()
{
	std::shared_ptr<A> a = std::make_shared<A>();
	std::thread t( &A::foo, a);
	t.join();
}

int main()
{
	thread_foo();
}
```

- 由于foo在A中是私有成员函数，无法被访问到

##### 解决方案：使用友元函数

```c++
#include<iostream>
#include<thread>
#include<memory>

class A {
private:
	friend void thread_foo();   
    void foo(){
    	std::cout << "Hello World" << std::endl;
    }
}

void thread_foo()
{
	std::shared_ptr<A> a = std::make_shared<A>();
	std::thread t( &A::foo, a);
	t.join();
}

int main()
{
	thread_foo();
}
```

- 将`thread_foo`设置为友元函数，使其可以访问类的私有成员函数。

