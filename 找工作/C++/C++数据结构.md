# 0、概念和问题

## 概念

##### 1、数据结构

## 问题

##### 1、声明和定义的区别

- 声明
  - 告诉编译器变量存在，但不一定分配内存
  - eg
    - `int a`只是声明，告诉编译器`a`是个`int`类型的变量，但不分配内存
- 定义
  - 同时做声明和分配内存，并且通常进行初始化
  - eg
    - `int a = 1`是定义，声明声明变量`a`并且为它分配内存和初始化

# 1、std::list双向列表

# 2、enum

枚举是用户自定义的数据类型，可为一组相关的常量赋予名称。

可以在类或命名空间中定义，也可在全局作用域中定义。

##### 基本语法：

```c++
enum EnumName {
    ENUM_VALUE1,
    ENUM_VALUE2,
    ENUM_VALUE3,
    // ...
};
```

- `EnumName`
  - 枚举类型

- `ENUM_VALUE1`、`ENUM_VALUE2`...
  - 枚举类型的枚举成员（`enumerators`）

- 每个枚举值默认从0开始，依次递增。
- 也可以显示地为枚举值指定具体的整数值。

##### 示例：

```c++
#include <iostream>

enum Color {
    RED,    // 0
    GREEN,  // 1
    BLUE    // 2
};

int main() {
    Color myColor = GREEN;

    if (myColor == GREEN) {
        std::cout << "The color is green!" << std::endl;
    }

    return 0;
}
```

- `Color`相当于变量类型，`RED`，`GREEN`，`BLUE`相当于变量可取的值。
