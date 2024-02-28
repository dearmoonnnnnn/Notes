# 一、绪论

## 1、字节码

![image-20220724111941116](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20220724111941116.png)



计算机只能理解二进制的目标代码（object code），人类难以掌握目标代码（object code），于是有了源代码（source code）。源代码通过编译被转化为目标代码，但是同一段源代码在不同操作系统下编译产生的目标代码不能互相兼容。

java语言编译时，源代码先被转化成可以兼容各操作系统的字节码（byte code），当我们需要在具体操作系统上运行代码时，字节码被java虚拟机（J·V·M）转化为相应的目标代码。

- 源代码Main.java  通过`javac Main.java`生成字节码 Main.class![image-20220727104410146](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20220727104410146.png)

  - 在intelliJ中打开.class 文件，并不会直接显示字节码，而是会将字节码转换成源代码后再显示

    ![image-20220727105858473](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20220727105858473.png)查看字节码参考链接如下：https://www.cnblogs.com/zwtblog/p/15618833.html

- 


## 2、JDK

![image-20220724113309431](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20220724113309431.png)



JDK： java开发配套工具

JRE： java运行环境，

# 二、数据类型

![image-20220731095847641](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20220731095847641.png)

- 基本数据类型一定是小写开头    `int age = 20; `

![1](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/1.png)

- 引用数据类型一定是大写开头    `String name = new String("Luke");`

- 引用数据类型和基本数据类型的区别

  - 基本数据类型，将一个变量赋值给另一个变量是拷贝。

    ```
    int a = 10;
    int b = a;
    ```

    ![image-20220731131423384](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20220731131423384.png)

  - 引用数据类型，赋值一个对象给变量，附的是一引用，这两个对象指向同一个存储块。

    ```
    Person alex = new Person("alex");
    Person mariam = alex;
    ```

    ![image-20220731131202161](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20220731131202161.png)























# 一、数据结构

![](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaV94aWFuc2hlbmc=,size_16,color_FFFFFF,t_70.png)

## 1、HashSet

[HashSet如何保证元素不重复](https://baijiahao.baidu.com/s?id=1719715985021473542&wfr=spider&for=pc)

详解 https://baijiahao.baidu.com/s?id=1719715985021473542&wfr=spider&for=pc

### 1.1、基本用法

创建： 

```
//HashSet<变量类型> 名称 = new HashSet<变量类型>();
HashSet<int> occ = new HashSet<int>();
```

| 操作     | 含义                          | 举例            |
| -------- | ----------------------------- | --------------- |
| add      | 添加                          | occ.add(1)      |
| remove   | 删除                          | occ.remove(1)   |
| contains | 是否包含，返回结果true或false | occ.contains(1) |
| size     | 返回集合数量                  | occ.size()      |

## 2、HashMap

HashMap是java中最常用的集合类框架，也是Java语言中非常典型的数据结构

https://blog.csdn.net/shi_xiansheng/article/details/117792691

### 2.1
