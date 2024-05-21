# 零、相关资料、问题和概念

## 相关资料

##### 视频：

https://www.bilibili.com/video/BV1d841117SH?p=2&vd_source=ddc1bcb1cabb9c324d56d94a9b4b6e93

##### 文档：

http://www.seestudy.cn/

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


## 概念：

##### 1、进程

进程就是运行中的程序。

##### 2、线程

线程就是进程中的进程。

##### 3、线程安全

如果多线程程序每一次的运行结果和单线程运行的结果始终是一样的，那么线程就是安全的。

##### 4、async

C++11引入的一个函数模板， 用于异步执行一个函数，返回一个`std::future`对象，表示异步操作的结果。

##### 5、同步操作与异步操作

同步操作

- 任务按**顺序执行**，一个任务必须等待前一个任务完成后才能开始执行。
- 会阻塞调用线程，直到任务完成

异步操作

- 任务可以**并发执行**，调用线程不必等待任务完成，可以继续执行其他任务。
- 不会阻塞调用线程

##### 6、std::future

- `std::future`是用于表示异步操作结果的一种机制。

- 它使得我们可以在稍后某个节点访问异步计算结果。

- `std::future`对象会与一个异步任务相关联，这个任务可能由`std::async`、`std::promise`、`std::packaged_task`启动。

##### 7、packaged_task

- `packaged_task`是一个类模板。

- 用于将一个**可调用对象**封装成一个异步操作。
  - 可调用对象：
    - 函数
    - 函数对象
    - Lambda表达式
- 返回一个`std::future`对象，表示异步操作的结果。

##### 8、promise

- 类模板
- 用于在线程中产生一个值，并在另一个线程中获取这个值。
- 通常与`future`和`async`一起使用。

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

# 三、互斥量解决多线程数据共享问题

## 1、数据共享问题分析

多个线程共享数据时，需要注意线程安全问题。

- 如果多个线程同时访问同一个变量，并且**至少一个**线程对该变量进行了**写操作**，那么就会出现数据竞争问题。
  - 可能会导致程序崩溃、产生未定义的结果，或者得到错误结果。

##### 举例：

```c++
#include<iostream>
#include<thread>

int a = 0;

void func(){
	for (int i = 0; i <	1000; i++)
		a += 1；
}

int main(){
    std::thread t1(func);
    std::thread t2(func);
    
    t1.join();
    t2.join();
    
    std::cout << a << std::endl;
    return 0;
}
```



- 对于变量a=0，线程1对a进行+10000操作，线程2对a也进行+10000操作。
- 预期线程1、2执行完后，a=20000。
  - 由于两个线程同时访问变量a，产生数据竞争，最终a <  20000，且每次得到的结果不一样。

##### 解决方法：互斥锁

线程1对变量a进行操作时，不允许其他线程访问该数据。

```c++
#include<iostream>
#include<thread>
#include<mutex>

int a = 0;
std::mutex mtx;

void func(){
	for (int i = 0; i <	1000; i++)
    {
        // 写操作之前加锁
		mtx.lock();
        a += 1；
        // 写操作结束之后解锁
		mtx.unlock();
    }
}

int main(){
    std::thread t1(func);
    std::thread t2(func);
    
    t1.join();
    t2.join();
    
    std::cout << a << std::endl;
    return 0;
}
```

## 2、互斥量死锁

##### 问题举例：

```c++
#include<iostream>
#include<thread>
#include<mutex>

std::mutex m1;
std::mutex m2;

void func_1(){
    for (int i = 0; i < 50; i++){
		m1.lock();	// 获取互斥锁m1
   	 	m2.lock();	// 获取互斥锁m2
    	   
    	m1.unlock();  	// 释放互斥锁m1
    	m2.unlock();	// 释放互斥锁m2
    }
}

void func_2(){
    for (int i = 0; i < 50; i++){
		m2.lock();	// 获取互斥锁m2
    	m1.lock();	// 获取互斥锁m1
    
    	m1.unlock();	// 释放互斥锁m1
    	m2.unlock();	// 释放互斥锁m2
    }
}


int main(){
    std::thread t1(func_1);
    std::thread t2(func_2);
    
    t1.join();
    t2.join();
    
    std::cout << "over" << std::endl;
    return 0;
}
```

- 运行结果：
  - 程序卡死，无法输出`over`
- 原因分析：
  - 线程`t1`获取互斥锁`m1`的同时，线程`t2`同时获取了互斥锁`m2`。
  - 这时线程`t1`希望获取互斥锁`m2`，而线程`t2`希望获得互斥锁`m1`，两者都在等待对方释放互斥锁，造成死锁。

##### 解决方法：

让两个线程都先获取m1，再获取m2

# 四、lock_guard与unique_lock

## 1、lock_guard

##### 动机：

```c++
#include<iostream>
#include<thread>
#include<mutex>

int shared_data = 0;
std::mutex mtx;

void func(){
	for (int i = 0; i < 10000; i++){
		mtx.lock();
		shared_data++;
		// mtx.unlock();
	}
}

int main(){
	std::thread t1(func);
	std::thread t2(func);
	
	t1.join();
	t2.join();
	
	std::cout << shared_data << std::endl;
	return 0;
}
```

- 加锁和解锁操作必须成对进行，若只加锁不解锁，程序报错

##### 使用lock_guard:

```c++
#include<iostream>
#include<thread>
#include<mutex>

int shared_data = 0;
std::mutex mtx;

void func(){
	for (int i = 0; i < 10000; i++){
		std::lock_guard<std::mutex> lg(mtx);
		shared_data++;
	}
}

int main(){
	std::thread t1(func);
	std::thread t2(func);
	
	t1.join();
	t2.join();
	
	std::cout << shared_data << std::endl;
	return 0;
}
```

- 当`lock_guard`的构造函数被调用时，该互斥量会被自动锁定。
- 当`lock_guard`的析构函数被调用时，该互斥量会被自动解锁。
  - 因为是局部变量，作用域结束后会自动调用析构函数。
- `std:lock_guard`对象不能被复制或移动，因此只能在**局部作用域**中使用。

## 2、unique_lock

##### 优势：

相比于`lock_guard`，除了自动加锁，还能实现

- 延迟加锁
- 条件变量
- 超时

更加灵活

##### 缺陷：

占用资源更多

##### 举例1：自动加锁

```c++
#include<iostream>
#include<thread>
#include<mutex>

int shared_data = 0;
std::mutex mtx;


void func(){
	for (int i = 0; i < 10000; i++){
		std::unique_lock<std::mutex> lg(mtx);
		shared_data++;
	}
}

int main(){
	std::thread t1(func);
	std::thread t2(func);
	
	t1.join();
	t2.join();
	
	std::cout << shared_data << std::endl;
	return 0;
}
```

- 同`lock_guard`
  - 调用构造函数自动加锁
  - 调用析构函数自动解锁

##### 举例2：延迟加锁

```c++
#include<iostream>
#include<thread>
#include<mutex>

int shared_data = 0;
// std::mutex mtx;
std::timed_mutex mtx;	// 支持对时间的操作，延迟加锁

void func(){
	for (int i = 0; i < 10000; i++){
		// 构造但是不加锁
		std::unique_lock<std::mutex> lg(mtx, std::defer_lock);
        // 需要手动加锁
        // lg.lock();
        if (lg.try_lock_for(std::chrono::seconds(5)){
        	shared_data++;    
        }
	}
}

int main(){
	std::thread t1(func);
	std::thread t2(func);
	
	t1.join();
	t2.join();
	
	std::cout << shared_data << std::endl;
	return 0;
}
```

- 调用构造函数，多加了一个常量参数`std::defer_lock`。
  - 这时构造函数不加锁，需要后面手动加锁：`lg.lock()`
- 这时使用`try_lock_for(std::chrono::seconds(5))`
  - 阻塞5秒后仍未获取锁，不再等待，直接返回。
  - `try_lock_until()`类似，传递的参数是时间点。

# 五、call_once与其使用场景

单例设计模式，确保某个类只能创建一个实例。

由于单例实例是全局唯一的，在多线程环境中使用单例模式时要考虑线程安全问题。

## 单例模式：

```c++
#include<iostream>
#include<thread>
#include<mutex>

class Log{
public:
	// 禁用拷贝构造和赋值构造
	Log(const Log& log) = delete;
	Log &operator = (const Log& log) = delete;
	static Log& GetInstance(){
		static Log log;		// 饿汉模式
		return log;
	}
    
    void PrintLog(std::string msg){
        std::cout << _TIME_ << " " << msg << std::endl;
    }
    
private:
    Log(){};
};

int main(){
	
    Log::GetInstance().PrintLog("error");
}
```

- 单例模式有两种

  - 饿汉模式

    - 一开始就创建实例
    - 饿汉模式线程安全，因为实例是在类加载时创建的，而类加载是线程安全的。

  - 懒汉模式

    - 只在使用时创建实例
    - 基本的懒汉模式非线程安全

    ```cassandra
    	static Log& GetInstance(){
    		static Log *log = nullptr;		// 懒汉模式
    		if (!log) log = new Log;
    		
    		return *log;
    	}
    ```

## 懒汉模式下的多线程产生的问题：

```c++
#include<iostream>
#include<thread>
#include<mutex>
static Log* log = nullptr;
static std::once_flag once;

class Log{
public:
	// 禁用拷贝构造和赋值构造
	Log(const Log& log) = delete;
	Log &operator = (const Log& log) = delete;
	static Log& GetInstance(){
		static Log *log = nullptr;		// 懒汉模式
		if (!log) log = new Log;
		
		return *log;
	}
    
    void PrintLog(std::string msg){
        std::cout << _TIME_ << " " << msg << std::endl;
    }
    
private:
    Log(){};
};

void print_error()
{
    Log::GetInstance().PrintLog("error");
}

int main(){
	
    std::thread t1(print_error);
    std::thread t2(print_error);
    t1.join();
    t2.join();
    
    return 0;
}
```

- `t1`、`t2`同时调用`PrintLog`函数，同时判断`Log`为空，则会实例化两个`Log`对象

## 使用call_once解决问题：

##### call_once 用法：

确保某个函数只会被调用一次。

只能在线程中使用，不能在`main`中使用

```c++
void call_once(std::once_flag& flag, Callable&& func, Args&&... args);
```

- `flag`
  - `std::once_flag`类型的对象
  - 用于标记函数是否已经被调用
- `func`
  - 需要被调用的函数或可调用对象
- `args`
  - 函数或可调用对象的参数

```c++
#include<iostream>
#include<thread>
#include<mutex>

class Log{
public:
	// 禁用拷贝构造和赋值构造
	Log(const Log& log) = delete;
	Log &operator = (const Log& log) = delete;
	static Log& GetInstance(){
		static Log *log = nullptr;		// 懒汉模式
		std::call_once(&once, init);
		
		return *log;
	}
    
    static void init(){
        if (!log) log = new Log;
    }
    
    void PrintLog(std::string msg){
        std::cout << _TIME_ << " " << msg << std::endl;
    }
    
private:
    Log(){};
};

void print_error()
{
    Log::GetInstance().PrintLog("error");
}

int main(){
	
    std::thread t1(print_error);
    std::thread t2(print_error);
    t1.join();
    t2.join();
    
    return 0;
}
```

- 多个线程进入 `GetInstance()`，只有一个call_once会被调用

# 六、condition_variable与其使用场景

## 1、生产者与消费者模型

![image-20240516155709888](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240516155709888.png)

- 生产者
  - 向任务队列添加任务
- 消费者
  - 从任务队列中取出任务，并且使用**多线程**的方法完成
- 若任务队列为空
  - 消费者等待
  - 生产者往任务队列中添加任务后，通知消费者

## 2、条件变量的使用步骤

1. 创建一个`std::condition_variable`对象
2. 创建一个互斥锁std::mutex对象，用来保护共享资源的访问。
   - 条件变量要和互斥锁共同使用
3. 在需要等待条件变量的地方
   - 使用互斥锁`std::unique_lock<std::mutex>`对象锁定互斥锁
   - 调用`std::condition_variable::wait()`、`std::condition_variable::wait_for()`或`std::condition_variable::wait_until()`函数，等待条件变量。
     - 这时候程序阻塞
4. 在其他线程中，需要通知等待的线程时
   - 调用`std::condition_variable::notify_one()`或`std::condition_variable::notify_all()`函数通知等待的线程。
     - 若生产者只往空的任务队列中加入了一个任务，调用`notify_one()`,通知一个消费者来完成任务。
     - 若生产者往空队列中添加了很多任务，调用`notify_all()`，通知所有消费者完成任务。

##### 代码示例：

```c++
#include<iostream>
#include<thread>
#include<mutex>
#include<string>
#include<condition_variable>	//条件变量头文件 
#include<queue> 				//任务队列头文件

std::queue<int> g_queue;
std::condition_variable g_cv;
std::mutex mtx;

// 生产者
void Producer(){
	for (int i = 0; i < 10; i++)
	{	
        { 
        	std::unique_lock<std::mutex> lock(mtx);
			g_queue.push(i);
        
        	// 通知消费者来取
        	g.cv.notify_noe();
            
            std::cout << "Producer : " << i << std::endl;
        }
	}
		std::this_thread::sleep_for(std::chrono::microseconds(100));
}

// 消费者
void Consumer()
{
	while(1)
	{
        std::unique_lock<std::mutex> lock(mtx);
        
        // 若队列为空，就要等待
        // g_cv.wait(lock, []() {return !g_queue.empty();
    	//	});
        bool isempty = g_queue.empty();
        g_cv.wait(lock,!isempty);
            
		int value = g_queue.front();
		g_queue.pop();
        
        std::cout << "Consumer : " << value << std::endl;
	}
}

int main()
{
    std::thread t1(Producer);
    std::thread t2(Consumer);
    t1.join();
    t2.join();
    
    return 0;
}
```

# 七、C++11 跨平台线程池

## 1、线程池图解

![image-20240518101314980](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240518101314980.png)

- 生产者
  - 往任务队列中加任务
- 线程池
  - 即左边的矩阵框部分
  - 调用线程数组取任务队列中取任务并完成

##### 代码:

```c++
#include<iostream>
#include<thread>
#include<mutex>
#include<string>
#include<condition_variable>	//条件变量头文件 
#include<queue> 				//任务队列头文件
#include<vector>
#include<function>

class ThreadPool{
public:
    ThreadPool(int numThreads) : stop(false){
        for (int i = 0; i < numThreads; i++){
            // emplace 直接调用成员的构造函数，更加节省资源
            // push 调用成员的拷贝构造
            // 因为是队列，先进先出，不能用emplace_back和push_back
            threads.emplace_back([this]{
                while(1){
                    std::unique_lock<std::mutex> lock(mtx);
                    conition.wait(lock, (this){
                        return !tasks.empty() || stop;
                    });
                    
                    if (stop){
                        return;
                    }
                    
                    std::function<void()> task(std::move(tasks.front()));
                    tasks.pop();
                    
                    lock.unlock();
                    task();
                }
            	});
        }
    } 
    
    ~ThreadPool(){
        {
            std::unique_lock<std::mutex> lock(mtx);
            stop = true;
        }
        
        condition.notify_all();
        for (auto& t : threads){
            t.join();
        }
    }
    
    // 往任务列表中加任务，因为任务列表中的函数不知道有几个参数，所以使用模板
    template<calss F,class... Args>
    // 在模板中 && 是万能引用，根据实际参数为左值引用还是右值引用来决定是哪一种引用
    void enqueue(F &&f, Args&&... args){
        std::function<void()>task = 
            std::bind(std::forward<F>(f), std::forward<Args>((args)...);
        {
            std::unique_lock<std::mutex> lock(mtx);
            tasks.emplace(std::move(task));          
        }
        condition.notify_one();
	} 
    
private:
    std::vector<std::thread> threads; // 线程数组
    std::queue<std::function<void>> tasks; // 任务队列
    
    std::mutex mtx; // 互斥锁
    std::condition_variable condition; // 条件变量
    
    bool stop; // 线程池终止
};
                      
                      
int main() {
    // 线程池中有4个线程
    ThreadPool pool(4);
	// 
    for (int i = 0; i < 10; i++){
        pool.enqueue([i]{
            std::cout<< "task : " << i << "is running" << std::endl;
            std::this_thread::sleep_for(std::chrono::seconds(1));
        	});
    }
    
    return 0;
}
```

- 代码存在的问题
  - `cout`没加锁，打印比较混乱。

# 八、异步并发

## 1、`std::async`与`std::future`

```c++
#include<iostream>
#include<future>
using namespace std;

int func() {
    int i;
	for (i = 0; i < 100; i++){
		i++
	}
	
	return i;
}

int main(){
	
    /*
     * 相当于多线程	 
     */
    
    // 将func传给async时，func已经开始运行，相当于一个线程
	std::future<int> future_result = std::async(std::launch::async, func);
	
    // 这里的func在主线程运行，相当于是第二个线程
	cout << func() << endl;
    
    // 使用get获取函数结果
	cout << future_result.get() << endl;
	
	return 0;
}
```

## 2、`std::packaged_task`

```c++
#include<iostream>
#include<future>
using namespace std;

int func() {
    int i;
	for (i = 0; i < 100; i++){
		i++
	}
	
	return i;
}

int main(){
	
    /*
     * 相当于多线程	 
     */
    
    // task 将func函数的地址包裹成对象，不会自动开启一个线程，因此需要手动开启线程
	std::package_task<int()> task(func);
    auto future_result = task.get_future();
    std::thread t1(std::move(task));
   
    // 这里的func在主线程运行，相当于是第二个线程
	cout << func() << endl;
    t1.join();
    
	cout << future_result.get() << endl;
	
	return 0;
}
```

- 将函数封装成`package_task`
- 通过`package_task`获得一个`future`对象
- 主线程和线程`t1`都运行`func`函数
  - 主线程直接打印运行结果
  - 线程`t1`将运行结果放在`future`对象中，通过`get()`方法获取结果。

## 3、promise

在一个线程中产生一个值，在另一个线程中获取这个值。

```c++
#include<iostream>
#include<future>
using namespace std;

void func(std::promise<int> & f) {
    
    f.set_value(1000);
}

int main(){
	
    std::promise<int> f;
	auto future_result = f.get_future();
    
    std::thread t1(func, std::ref(f));
    
    t1.join();
    
    cout << future_result.get() << std::endl;
    
	return 0;
}
```

- 在子线程中使用`promise`将`f`设置为1000
- 在主线程中使用`get_future()`函数获取`f`的`future`对象。
  - 使用`future`对象的`get()`函数获取函数的值。

# 九、原子操作

