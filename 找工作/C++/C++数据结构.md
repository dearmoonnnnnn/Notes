# 0、概念和问题

## 概念

##### 1、数据结构



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
