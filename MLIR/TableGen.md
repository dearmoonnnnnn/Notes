https://zhuanlan.zhihu.com/p/102727417

##### **生产线的类比：**

将MLIRGen模块看作是生产线的履带、将Dialect模块看作是生产线的机械臂、将TableGen模块看作是生产线的零件提供者。各Dialect Operation类看作零件。

##### **总体说说：**

TableGen模块使用Operation Definitoin Specification（ODS）框架进行自动化生成代码，整个框架基于TableGen规则来完成相应功能。

##### **发挥作用：**

TableGen是一种声明性编程语言，用于描述MLIR中Operation的类的定义。在源代码中它以`.td`文件的形式存在，在编译时会自动生成C++的相应文件，给Dialect模块文件提供支持。

如果我们使用手动编写的方式，在针对不同编译目标时，我们需要在一系列不同文件中编写一些相同的代码，这就造成了**冗余的开发**。而使用TableGen，我们只需要修改`.td`文件即可实现批量修改，也就解决了上述问题。

1. 定义一个和Toy Dialect的链接，把Dialect中定义的所有Operation整合起来
2. 构造所有Dialect Operation的基类`Toy_Op`，所有的Operation类都将基于此类进行构造
3. 创建Toy Dialect各种Operation的类

##### 生成C++代码

```
$ cd llvm-project/build
$ bin/mlir-tblgen -gen-op-defs ../mlir/examples/toy/Ch2/include/toy/Ops.td -I ../mlir/include/
```

<img src="https://pic2.zhimg.com/80/v2-10f17b753e962b7469c91b5fad8376bd_720w.jpg" alt="img"  />

