https://mp.weixin.qq.com/s/wCepijP9DpOHsUDqKboT7Q

# IR大致分为3类：

**Structural IR**：使用图结构表示IR，一般使用该类IR进行源到源转换。如 抽象语法树AST

<img src="../../../../AppData/Roaming/Typora/typora-user-images/image-20211013104954857.png" alt="image-20211013104954857" style="zoom:50%;" />

**Linear IR：**类似 abstract machine 的汇编代码，编译器一般使用该类 IR 进行 lowering。编译器中这类 IR 有：three-address code，stack-machine code 等。

<img src="../../../../AppData/Roaming/Typora/typora-user-images/image-20211013105143920.png" alt="image-20211013105143920" style="zoom:50%;" />

# 作用：

进行进一步的处理：转换、分析、优化。

一个良好设计的 IR 应该可以被转换到*多后端* (instruction set architectures)，同时又能被不同的语言前端转换，这样可以简化编译器开发工作量。例如下图中，IR 将 20 个编译器（*5 个**语言 x 4* *个后端*）简化为只需要 *5 个编译器前**端 + 4 个**编译器后端*。

<img src="../../../../AppData/Roaming/Typora/typora-user-images/image-20211013105404639.png" alt="image-20211013105404639" style="zoom: 33%;" />

## 用于代码安全规范检查分析

代码安全检查工具基于IR进行安全检查分析，发现代码中不符合代码规范的问题。

### IR的选择

设计安全检查工具时首先要考虑的问题：在哪些IR上进行哪些检查分析。一般情况下，根据检查场景和IR的特点进行选择

**根据场景：**

<img src="../../../../AppData/Roaming/Typora/typora-user-images/image-20211013110333555.png" alt="image-20211013110333555" style="zoom: 67%;" />

**根据IR特点：**

如果需要更多 high-level 语义信息进行分析，选择 AST/CFG；否则，一般选择在更低层次的 IR 进行分析。

## 用于编译器分析和优化

# 新趋势

介绍IR在两个语言相关的新趋势中的重要应用，DSL和多平台后端。

## # IR 在DSL中的应用

 Domian Specific Architecture（DSA）和 Domian Specific Language（DSL）成为重要趋势。但是DSL的设计和实现非常困难。

目前业界实现DSL主要由两个方案：**MLIR**和**eDSL**

**## eDSL**

embedded DSL，又称内部DSL，或嵌入式DSL，它将一种现有通用编程语言 (GPL) 作为宿主语言，利用其基础设施 (语法、编译器、工具等) 建立专门面向特定领域的语义

## # IR 在多平台后端中的应用

语言支持多平台（即 web、desktop、mobile 等平台）后端已经成为另一个趋势。如新语言 Kotlin、Flutter/Dart

然而，多平台后端面临==平台后端无关==的分析和优化每个平台后端都要实现一遍的问题，如下图所示：

<img src="../../../../AppData/Roaming/Typora/typora-user-images/image-20211013113758713.png" alt="image-20211013113758713" style="zoom: 50%;" />

为了解决这个问题，新语言一般会将提出平台后端无关的 IR，在这个 IR 上进行平台后端无关的分析和优化。例如，Kotlin 提出了 unified IR 