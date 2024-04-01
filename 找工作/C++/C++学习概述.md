# 需要掌握的知识点：

##### 1、源自知乎

- ##### STL：容器、迭代器、容器适配器、仿函数、算法等知识点，

- ##### C++11特性有哪些，说用过的

- ##### 怎么理解重载与重写

- ##### 怎么理解C++中的static关键字

- ##### vector和list 的区别

- ##### C++虚函数原理

- ##### 比如容器的几大部件：vector、deque、stack/queue、map/set、unordered_map、/unordered_set各自的适用场景

- ##### 迭代器：随机访问迭代器、双向迭代器、前项迭代器

- ##### C++11的新特性：RAII思想、四大(unique_ptr、[weak_ptr](https://www.zhihu.com/search?q=weak_ptr&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2785565454})、auto_ptr、[shared_ptr](https://www.zhihu.com/search?q=shared_ptr&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2785565454})）

- ##### 四种强制类型转换：static_cast、reinterpret_cast、const_cast、dynamic_cast，分别的适用场景

- ##### C和C++的区别：C++面向对象，C面向过程；输入输出流不同：C中使用printf与scanf等；C++使用类如cin、cout；C++中使用new/delete代替C中的malloc/free

- 面向对象三大特性：封装继承多态，其中面向过程则性能高、不易维护

- 类与对象：struct/class对比；友元类与友元函数；六个默认成员函数 

包括：构造函数（缺省构造函数、explicit、初始化列表）、[拷贝构造函数](https://www.zhihu.com/search?q=拷贝构造函数&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"2989662911"})、析构函数、类和对象的知识点还包括：inline内联函数、类的静态成员、类和对象的实例化 

##### 2、源自claude

作为一名计算机专业的学生,想要以C++作为主力语言找工作,需要学习和掌握以下知识:

1. **C++核心语言特性**
   - 数据类型、运算符、流程控制语句、函数、数组、指针等基础知识
   - 面向对象编程:类、对象、继承、多态、虚函数等概念
   - 标准模板库(STL):容器、算法、迭代器、函数对象等
   - 内存管理:new/delete、智能指针、内存泄漏检测
   - 异常处理机制
   - 语言新特性(C++11/14/17/20):auto、lambda、移动语义等

2. **系统编程**
   - 文件I/O操作
   - 多线程编程
   - 进程间通信(IPC)
   - 网络编程(Socket编程)

3. **面向对象设计与设计模式**
   - 面向对象设计原则(SOLID)
   - 常见设计模式(单例、工厂、观察者、代理等)
   - UML建模

4. **算法与数据结构**
   - 基本数据结构(链表、树、栈、队列、哈希表等)及相关算法
   - 高级数据结构(并查集、Trie树、线段树等)
   - 常见算法思想(递归、分治、贪心、动态规划等)
   - 算法复杂度分析

5. **C++标准库**
   - 标准模板库(STL)的深入理解和应用
   - 其他标准库(正则表达式、多线程、文件系统等)

6. **框架与工具**
   - 构建工具(Make、CMake、Ninja等)
   - 单元测试框架(Google Test、Catch2等)
   - 日志库(spdlog、glog等)
   - 其他常用库(Boost、Eigen、OpenCV等)

7. **软件工程实践**
   - 版本控制(Git)
   - 代码风格与规范
   - 代码审查
   - 持续集成(CI/CD)
   - 调试技巧
   - 性能分析与优化

8. **具体应用领域知识**
   - 根据你未来的职业规划,如游戏开发、嵌入式系统、机器学习等,需要学习相应的知识

以上列出了作为C++开发者需要掌握的主要知识点,学习路径建议是先夯实C++语言基础,然后学习数据结构与算法,再逐步深入系统编程、面向对象设计、C++标准库等领域。同时要多实践,做项目积累经验,并持续跟进C++新标准和新技术的发展。

这是一个漫长的学习过程,需要持之以恒的努力和热情。我建议你制定一个学习计划,系统地、有重点地逐步攻克,并找到适合自己的学习方式,相信最终一定能够达到目标,祝你好运!

##### 3、源自2010年8月《程序员》刊登的《C++强大背后》

C++学习建议 C++缺点之一，是相对许多语言复杂，而且难学难精。许多人说学习C语言只需一本K&R《C程序设计语言》即可，但C++书籍却是多不胜数。我是从C进入C++，皆是靠阅读自学。在此分享一点学习心得。个人认为，学习C++可分为4个层次：     

- 第一层次，C++基础：挑选一本入门书籍，如《C++  Primer》、《C++大学教程》、或Stroustrup撰写的经典《C++程序设计语言》或他一年半前的新作《C++程序设计原理与实践》，而一般C++课程也止于此，另外《C++ 标准程序库》及《The C++ Standard Library Extensions》可供参考；    

- 第二层次，正确高效地使用C++：此层次开始必须自修，阅读过《(More)Effective  C++》、《(More)Exceptional C++》、《Effective STL》及《C++编程规范》等，才适宜踏入专业C++开发之路；    

- 第三层次，深入了解C++：关于全局问题可读《深入探索C++对象模型》、《Imperfect  C++》、《C++沉思录》、《STL源码剖析》，要挑战智商，可看关于模版及模版元编程的书籍如《C++  Templates》、《C++设计新思维》、《C++模版元编程》；    

- 第四层次，研究C++：阅读《C++语言的设计和演化》、《编程的本质》(含STL设计背后的数学根基)、C++标准文件《ISO/IEC  14882:2003》、C++标准委员会的提案书和报告书、关于C++的学术文献。 

由于我主要是应用C++，大约只停留于第二、三个层次。然而，C++只是软件开发的一环而已，单凭语言并不能应付业务和工程上的问题。建议读者不要强求几年内“彻底学会C++的知识”，到达第二层左右便从工作实战中汲取经验，有兴趣才慢慢继续学习更高层次的知识。虽然学习C++有难度，但也是相当有趣且有满足感的。

# 问题：

```
int* data = new int[10];	//分配10个整数的内存
data[0] = 42;				//使用该内存
delete[] arr;				//释放该内存
```

##### 1、cpp和c++有什么区别

"cpp" 可能指的是几种不同的东西，取决于上下文。以下是可能的含义：

1. **C++ 编程语言：** 最常见的含义是 C++ 编程语言。C++ 是一种通用编程语言，是 C 语言的扩展，它支持面向对象编程、泛型编程和其他高级编程特性。C++ 通常用于开发各种类型的软件应用程序，包括系统软件、应用程序、游戏等等。

2. **C++ 源代码文件扩展名：** 在计算机文件系统中，".cpp" 是用于存储 C++ 源代码的文件的扩展名。当你在编写 C++ 程序时，你通常会将源代码保存在以 ".cpp" 结尾的文件中，然后编译器会将这些源代码文件编译成可执行文件。

3. **C Preprocessor（C 预处理器）：** 在 C 语言中，"cpp" 是 C 预处理器的命令，用于执行预处理任务，如包含头文件、宏替换等。这个命令通常用于在编译 C 程序之前对源代码进行预处理。

区别：

cpp可能和c++一样指的是编程语言，也可能指的是源代码文件扩展名，或者C预处理器。

# 相关链接：

- 阿秀的学习笔记

  https://interviewguide.cn/notes/07-resources/01-free/04-schoolSchample.html

# 项目：

当你在简历上列出一些C++项目时，最好选择那些能够突出你的技能和经验的项目。以下是一些你可以考虑写在简历中的C++项目，它们涵盖了不同领域和技术：

1. [**Awesome C++**：这是一个GitHub上的项目合集，已经获得了**52,000**颗星，其中也有中文版。它包括了C++的标准库、Web应用框架、人工智能、数据库、图片处理、机器学习、日志、代码分析等](https://zhuanlan.zhihu.com/p/568543941)[1](https://zhuanlan.zhihu.com/p/568543941)。
2. [**CppGuide**：这是一个涵盖大部分C++程序员所需掌握知识的项目，包括入门、进阶、深入、校招和社招相关的内容](https://zhuanlan.zhihu.com/p/568543941)[1](https://zhuanlan.zhihu.com/p/568543941)。
3. [**TinyWebServer**：这是一个Linux下的轻量级C++ Web服务器，适合初学者快速实践网络编程，搭建自己的服务器。它使用线程池、非阻塞socket、epoll等技术，支持解析HTTP请求，实现了用户注册、登录功能和同步/异步日志系统](https://zhuanlan.zhihu.com/p/568543941)[1](https://zhuanlan.zhihu.com/p/568543941)。
4. [**cpp_redis**：这是一个C++11编写的轻量级Redis客户端，具有异步、线程安全、无依赖、pipelining、跨平台等特性。它的代码量不大，适合学习如何编写高效的网络通信客户端程序](https://zhuanlan.zhihu.com/p/568543941)[1](https://zhuanlan.zhihu.com/p/568543941)。
5. [**MIT 6.828**：这是一门操作系统课程，你可以跟着B站的MIT 6.828视频做，实现一个简单的操作系统内核。每个实验都有对应的知识点，边学边做，效果更高效](https://zhuanlan.zhihu.com/p/568543941)[1](https://zhuanlan.zhihu.com/p/568543941)。
6. [**dbg-macro**：这是一个打日志的C++宏函数，受到Rust语言中的`dbg`启发，提供比`printf`和`std::cout`更好的调试方式](https://zhuanlan.zhihu.com/p/568543941)[1](https://zhuanlan.zhihu.com/p/568543941)。
7. [**Json解析库**：你可以尝试从零开始编写一个JSON库，这对于理解数据解析和序列化非常有帮助](https://zhuanlan.zhihu.com/p/568543941)[1](https://zhuanlan.zhihu.com/p/568543941)。
8. [**自己的STL**：基于C++11的tinySTL，实现了大部分STL中的容器与函数，适合作为新手练习](https://zhuanlan.zhihu.com/p/568543941)[1](https://zhuanlan.zhihu.com/p/568543941)。
9. [**自己的服务器框架**：这个项目包含了日志模块、配置模块、线程模块、协程模块、IO协程调度模块等，适合学习服务器开发](https://zhuanlan.zhihu.com/p/568543941)[1](https://zhuanlan.zhihu.com/p/568543941)。

[无论你选择哪个项目，记得在写简历时整理出项目中的技术点，思考如何向面试官介绍你的项目，以经得起他们的提问。](https://zhuanlan.zhihu.com/p/568543941)[1](https://zhuanlan.zhihu.com/p/568543941)[2](https://blog.csdn.net/mazamu/article/details/119899923)

<iframe name="298f942b-2da1-6326-d4f0-d784d005c3bd" style="width:100%;height:0px;" class="frame" src="https://www.bing.com/search?showonlyads=1&amp;codex_conid=51D%7CEdgeCopilot%7C9927227C2790C63129A0E9F8C594E1175C2CD74C2521B5D58398A9B79BE3684C&amp;codex_summ=%5B%7B%22author%22%3A%22user%22%2C%22text%22%3A%22github%E4%B8%8A%E6%9C%89%E4%BB%80%E4%B9%88C%2B%2B%E9%A1%B9%E7%9B%AE%EF%BC%8C%E5%8F%AF%E4%BB%A5%E5%86%99%E5%9C%A8%E7%AE%80%E5%8E%86%E9%87%8C%EF%BC%8C%E4%BE%BF%E4%BA%8E%E6%89%BE%E5%B7%A5%E4%BD%9C%22%7D%2C%7B%22author%22%3A%22bot%22%2C%22text%22%3A%22%E5%BD%93%E4%BD%A0%E5%9C%A8%E7%AE%80%E5%8E%86%E4%B8%8A%E5%88%97%E5%87%BA%E4%B8%80%E4%BA%9BC%2B%2B%E9%A1%B9%E7%9B%AE%E6%97%B6%EF%BC%8C%E6%9C%80%E5%A5%BD%E9%80%89%E6%8B%A9%E9%82%A3%E4%BA%9B%E8%83%BD%E5%A4%9F%E7%AA%81%E5%87%BA%E4%BD%A0%E7%9A%84%E6%8A%80%E8%83%BD%E5%92%8C%E7%BB%8F%E9%AA%8C%E7%9A%84%E9%A1%B9%E7%9B%AE%E3%80%82%E4%BB%A5%E4%B8%8B%E6%98%AF%E4%B8%80%E4%BA%9B%E4%BD%A0%E5%8F%AF%E4%BB%A5%E8%80%83%E8%99%91%E5%86%99%E5%9C%A8%E7%AE%80%E5%8E%86%E4%B8%AD%E7%9A%84C%2B%2B%E9%A1%B9%E7%9B%AE%EF%BC%8C%E5%AE%83%E4%BB%AC%E6%B6%B5%E7%9B%96%E4%BA%86%E4%B8%8D%E5%90%8C%E9%A2%86%E5%9F%9F%E5%92%8C%E6%8A%80%E6%9C%AF%EF%BC%9A%5Cn%5Cn1.+**Awesome+C%2B%2B**%EF%BC%9A%E8%BF%99%E6%98%AF%E4%B8%80%E4%B8%AAGitHub%E4%B8%8A%E7%9A%84%E9%A1%B9%E7%9B%AE%E5%90%88%E9%9B%86%EF%BC%8C%E5%B7%B2%E7%BB%8F%E8%8E%B7%E5%BE%97%E4%BA%86**52%2C000**%E9%A2%97%E6%98%9F%EF%BC%8C%E5%85%B6%E4%B8%AD%E4%B9%9F%E6%9C%89%E4%B8%AD%E6%96%87%E7%89%88%E3%80%82%E5%AE%83%E5%8C%85%E6%8B%AC%E4%BA%86C%2B%2B%E7%9A%84%E6%A0%87%E5%87%86%E5%BA%93%E3%80%81Web%E5%BA%94%E7%94%A8%E6%A1%86%E6%9E%B6%E3%80%81%E4%BA%BA%E5%B7%A5%E6%99%BA%E8%83%BD%E3%80%81%E6%95%B0%E6%8D%AE%E5%BA%93%E3%80%81%E5%9B%BE%E7%89%87%E5%A4%84%E7%90%86%E3%80%81%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E3%80%81%E6%97%A5%E5%BF%97%E3%80%81%E4%BB%A3%E7%A0%81%E5%88%86%E6%9E%90%E7%AD%89%5B%5E1%5E%5D%E3%80%82%5Cn%5Cn2.+**CppGuide**%EF%BC%9A%E8%BF%99%E6%98%AF%E4%B8%80%E4%B8%AA%E6%B6%B5%E7%9B%96%E5%A4%A7%E9%83%A8%E5%88%86C%2B%2B%E7%A8%8B%E5%BA%8F%E5%91%98%E6%89%80%E9%9C%80%E6%8E%8C%E6%8F%A1%E7%9F%A5%E8%AF%86%E7%9A%84%E9%A1%B9%E7%9B%AE%EF%BC%8C%E5%8C%85%E6%8B%AC%E5%85%A5%E9%97%A8%E3%80%81%E8%BF%9B%E9%98%B6%E3%80%81%E6%B7%B1%E5%85%A5%E3%80%81%E6%A0%A1%E6%8B%9B%E5%92%8C%E7%A4%BE%E6%8B%9B%E7%9B%B8%E5%85%B3%E7%9A%84%E5%86%85%E5%AE%B9%5B%5E1%5E%5D%E3%80%82%5Cn%5Cn3.+**TinyWebServer**%EF%BC%9A%E8%BF%99%E6%98%AF%E4%B8%80%E4%B8%AALinux%E4%B8%8B%E7%9A%84%E8%BD%BB%E9%87%8F%E7%BA%A7C%2B%2B+Web%E6%9C%8D%E5%8A%A1%E5%99%A8%EF%BC%8C%E9%80%82%E5%90%88%E5%88%9D%E5%AD%A6%E8%80%85%E5%BF%AB%E9%80%9F%E5%AE%9E%E8%B7%B5%E7%BD%91%E7%BB%9C%E7%BC%96%E7%A8%8B%EF%BC%8C%E6%90%AD%E5%BB%BA%E8%87%AA%E5%B7%B1%E7%9A%84%E6%9C%8D%E5%8A%A1%E5%99%A8%E3%80%82%E5%AE%83%E4%BD%BF%E7%94%A8%E7%BA%BF%E7%A8%8B%E6%B1%A0%E3%80%81%E9%9D%9E%E9%98%BB%E5%A1%9Esocket%E3%80%81epoll%E7%AD%89%E6%8A%80%E6%9C%AF%EF%BC%8C%E6%94%AF%E6%8C%81%E8%A7%A3%E6%9E%90HTTP%E8%AF%B7%E6%B1%82%EF%BC%8C%E5%AE%9E%E7%8E%B0%E4%BA%86%E7%94%A8%E6%88%B7%E6%B3%A8%E5%86%8C%E3%80%81%E7%99%BB%E5%BD%95%E5%8A%9F%E8%83%BD%E5%92%8C%E5%90%8C%E6%AD%A5%2F%E5%BC%82%E6%AD%A5%E6%97%A5%E5%BF%97%E7%B3%BB%E7%BB%9F%5B%5E1%5E%5D%E3%80%82%5Cn%5Cn4.+**cpp_redis**%EF%BC%9A%E8%BF%99%E6%98%AF%E4%B8%80%E4%B8%AAC%2B%2B11%E7%BC%96%E5%86%99%E7%9A%84%E8%BD%BB%E9%87%8F%E7%BA%A7Redis%E5%AE%A2%E6%88%B7%E7%AB%AF%EF%BC%8C%E5%85%B7%E6%9C%89%E5%BC%82%E6%AD%A5%E3%80%81%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8%E3%80%81%E6%97%A0%E4%BE%9D%E8%B5%96%E3%80%81pipelining%E3%80%81%E8%B7%A8%E5%B9%B3%E5%8F%B0%E7%AD%89%E7%89%B9%E6%80%A7%E3%80%82%E5%AE%83%E7%9A%84%E4%BB%A3%E7%A0%81%E9%87%8F%E4%B8%8D%E5%A4%A7%EF%BC%8C%E9%80%82%E5%90%88%E5%AD%A6%E4%B9%A0%E5%A6%82%E4%BD%95%E7%BC%96%E5%86%99%E9%AB%98%E6%95%88%E7%9A%84%E7%BD%91%E7%BB%9C%E9%80%9A%E4%BF%A1%E5%AE%A2%E6%88%B7%E7%AB%AF%E7%A8%8B%E5%BA%8F%5B%5E1%5E%5D%E3%80%82%5Cn%5Cn5.+**MIT+6.828**%EF%BC%9A%E8%BF%99%E6%98%AF%E4%B8%80%E9%97%A8%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E8%AF%BE%E7%A8%8B%EF%BC%8C%E4%BD%A0%E5%8F%AF%E4%BB%A5%E8%B7%9F%E7%9D%80B%E7%AB%99%E7%9A%84MIT+6.828%E8%A7%86%E9%A2%91%E5%81%9A%EF%BC%8C%E5%AE%9E%E7%8E%B0%E4%B8%80%E4%B8%AA%E7%AE%80%E5%8D%95%E7%9A%84%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E5%86%85%E6%A0%B8%E3%80%82%E6%AF%8F%E4%B8%AA%E5%AE%9E%E9%AA%8C%E9%83%BD%E6%9C%89%E5%AF%B9%E5%BA%94%E7%9A%84%E7%9F%A5%E8%AF%86%E7%82%B9%EF%BC%8C%E8%BE%B9%E5%AD%A6%E8%BE%B9%E5%81%9A%EF%BC%8C%E6%95%88%E6%9E%9C%E6%9B%B4%E9%AB%98%E6%95%88%5B%5E1%5E%5D%E3%80%82%5Cn%5Cn6.+**dbg-macro**%EF%BC%9A%E8%BF%99%E6%98%AF%E4%B8%80%E4%B8%AA%E6%89%93%E6%97%A5%E5%BF%97%E7%9A%84C%2B%2B%E5%AE%8F%E5%87%BD%E6%95%B0%EF%BC%8C%E5%8F%97%E5%88%B0Rust%E8%AF%AD%E8%A8%80%E4%B8%AD%E7%9A%84%60dbg%60%E5%90%AF%E5%8F%91%EF%BC%8C%E6%8F%90%E4%BE%9B%E6%AF%94%60printf%60%E5%92%8C%60std%3A%3Acout%60%E6%9B%B4%E5%A5%BD%E7%9A%84%E8%B0%83%E8%AF%95%E6%96%B9%E5%BC%8F%5B%5E1%5E%5D%E3%80%82%5Cn%5Cn7.+**Json%E8%A7%A3%E6%9E%90%E5%BA%93**%EF%BC%9A%E4%BD%A0%E5%8F%AF%E4%BB%A5%E5%B0%9D%E8%AF%95%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E7%BC%96%E5%86%99%E4%B8%80%E4%B8%AAJSON%E5%BA%93%EF%BC%8C%E8%BF%99%E5%AF%B9%E4%BA%8E%E7%90%86%E8%A7%A3%E6%95%B0%E6%8D%AE%E8%A7%A3%E6%9E%90%E5%92%8C%E5%BA%8F%E5%88%97%E5%8C%96%E9%9D%9E%E5%B8%B8%E6%9C%89%E5%B8%AE%E5%8A%A9%5B%5E1%5E%5D%E3%80%82%5Cn%5Cn8.+**%E8%87%AA%E5%B7%B1%E7%9A%84STL**%EF%BC%9A%E5%9F%BA%E4%BA%8EC%2B%2B11%E7%9A%84tinySTL%EF%BC%8C%E5%AE%9E%E7%8E%B0%E4%BA%86%E5%A4%A7%E9%83%A8%E5%88%86STL%E4%B8%AD%E7%9A%84%E5%AE%B9%E5%99%A8%E4%B8%8E%E5%87%BD%E6%95%B0%EF%BC%8C%E9%80%82%E5%90%88%E4%BD%9C%E4%B8%BA%E6%96%B0%E6%89%8B%E7%BB%83%E4%B9%A0%5B%5E1%5E%5D%E3%80%82%5Cn%5Cn9.+**%E8%87%AA%E5%B7%B1%E7%9A%84%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A1%86%E6%9E%B6**%EF%BC%9A%E8%BF%99%E4%B8%AA%E9%A1%B9%E7%9B%AE%E5%8C%85%E5%90%AB%E4%BA%86%E6%97%A5%E5%BF%97%E6%A8%A1%E5%9D%97%E3%80%81%E9%85%8D%E7%BD%AE%E6%A8%A1%E5%9D%97%E3%80%81%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%9D%97%E3%80%81%E5%8D%8F%E7%A8%8B%E6%A8%A1%E5%9D%97%E3%80%81IO%E5%8D%8F%E7%A8%8B%E8%B0%83%E5%BA%A6%E6%A8%A1%E5%9D%97%E7%AD%89%EF%BC%8C%E9%80%82%E5%90%88%E5%AD%A6%E4%B9%A0%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%BC%80%E5%8F%91%5B%5E1%5E%5D%E3%80%82%5Cn%5Cn%E6%97%A0%E8%AE%BA%E4%BD%A0%E9%80%89%E6%8B%A9%E5%93%AA%E4%B8%AA%E9%A1%B9%E7%9B%AE%EF%BC%8C%E8%AE%B0%E5%BE%97%E5%9C%A8%E5%86%99%E7%AE%80%E5%8E%86%E6%97%B6%E6%95%B4%E7%90%86%E5%87%BA%E9%A1%B9%E7%9B%AE%E4%B8%AD%E7%9A%84%E6%8A%80%E6%9C%AF%E7%82%B9%EF%BC%8C%E6%80%9D%E8%80%83%E5%A6%82%E4%BD%95%E5%90%91%E9%9D%A2%E8%AF%95%E5%AE%98%E4%BB%8B%E7%BB%8D%E4%BD%A0%E7%9A%84%E9%A1%B9%E7%9B%AE%EF%BC%8C%E4%BB%A5%E7%BB%8F%E5%BE%97%E8%B5%B7%E4%BB%96%E4%BB%AC%E7%9A%84%E6%8F%90%E9%97%AE%E3%80%82%5B%5E1%5E%5D+%5B%5E2%5E%5D%22%7D%5D&amp;IG=931948ECA158405E966146F17AC4DF77&amp;IID=SERP.5044&amp;cw=306&amp;ch=698&amp;form=codexx&amp;dissrchswrite=1&amp;kseed=7000&amp;SFX=&amp;q=github%E4%B8%8A%E6%9C%89%E4%BB%80%E4%B9%88C%2B%2B%E9%A1%B9%E7%9B%AE%EF%BC%8C%E5%8F%AF%E4%BB%A5%E5%86%99%E5%9C%A8%E7%AE%80%E5%8E%86%E9%87%8C%EF%BC%8C%E4%BE%BF%E4%BA%8E%E6%89%BE%E5%B7%A5%E4%BD%9C&amp;iframeid=298f942b-2da1-6326-d4f0-d784d005c3bd&amp;cdxpc=Underside&amp;cdxafr=1&amp;brid=d2d0ef98-4e02-7fdc-3161-73e90dc86a21&amp;codex_src=sq">
        </iframe>

​              
