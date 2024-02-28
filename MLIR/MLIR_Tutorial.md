参考资料： https://www.zhihu.com/people/CHUNerr/posts

# 1 名词解释

- MLIR: Multi-Level Intermediate Representation 多级别中介码

- [LLVM](https://blog.csdn.net/water1307/article/details/81390175): Low Lever Virtual Machine 它的命名源自底层虚拟机，但它已经不是底层虚拟机了。LLVM是一个架构编译器的框架系统，包含LLVM中介码（LLVM IR）、LLVM除错工具、LLVM C++标准库等一套工具，和传统底层虚拟机并没什么关系。

- [pass](https://blog.csdn.net/water1307/article/details/81390175)：LLVM的pass框架是LLVM系统的一个很重要的部分。LLVM的优化和转换工作就是由多个pass来一起完成得。类似流水线操作一样，每个pass完成特定的优化工作。要想真正发挥LLVM的威力，掌握pass是不可或缺的一环。

  LLVM中pass架构的可重用性和可控制性都非常好，这允许用户自己开发pass或者关闭一些默认提供的pass。

- DSL: domain-specific language 邻域特定语言

- AST：抽象语法树

- IR：中间表达 

- SSA：静态单赋值（Static Single Assignment）

- [Round-trip](https://mlir.llvm.org/getting_started/Glossary/#round-trip):  从源格式转换成目标格式，再从目标格式转换为源格式的过程。（待考证）TensorFlow Graph（源格式）->各个dialect（目标格式）->LLVM IR(源格式)

- tablegen：分为三个部分，Dialect链接、Operation基类、自定义Operation。基于Operation Definition Specification(ODS)框架进行编写以及发挥作用。

  ![image-20211012100559700](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211012100559700.png)

- ODS（Operation Definition Specification）：operation定义的规范，按照这样的规范来编写自定义operation，可使定义在编译时发挥作用，也就是说`.td`文件中编写的operation定义可以扩展为等价的`mlir::Op`的C++代码。这样一来，可以将Dialecr的各种Operation集中发在TableGen模块中处理。

- DRR: Table-driven Declarative Rewrite Rule 简单理解为重写规则

# 2 解决的问题

https://www.bilibili.com/video/BV1Wp4y1z72d?from=search&seid=11620721230078650892&spm_id_from=333.337.0.0

## 2.1 编译流程

https://blog.csdn.net/zhang971105/article/details/109074766

![image-20211008211902913](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211008211902913.png)



![image-20211008211805292](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211008211805292.png)

用高级语言编写TensorFlow程序，用TensorFlow Graf的方式呈现。将TensorFlow的图变成XLA的图，后续将其变成LLVM IR。后面可以使用LLVM后端的各种基础架构，往不同的平台的分发。

## 2.2 存在的问题

![image-20211008212708007](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211008212708007.png)

前面两种是基于图的IR，后面更低层的IR (eg: LLVM IR) 是基于”三地址码“的IR。

- 组合爆照，很多组件无法重用。最后转换成面向各种平台的不同IR时，所需要的转换过程中，很多组件无法重用。
- 各个层次内部优化无法迁移。面向”图“的IR优化和面向”三地址码“的IR优化，不能协同。（谁也看不见谁）
- 从XLA HLO到LLVM IR跨度太大，实现开销大

## 2.3 MLIR如何解决问题

![image-20211008213326552](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211008213326552.png)

![image-20211008213446958](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211008213446958.png)

![image-20211008213501768](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211008213501768.png)

1. 将TensorFlow Graph 翻译为 用TensorFlow Dialect描述的MLIR文件。
2. 将MLIR文件进行多次逐级向下抽象（Multi-level），一步一步变成LLVM IR Dialect。
3. 将其生成到LLVM IR。
4. 再进行后续步骤

优点：

- 语义同一，所有Dialect共享同一套MLIR语义，组件可以重用
- 共享生态，各个层次可以协调优化

- 多层逐级向下抽象，层级之间跨度小，方便实现

解决的问题：MLIR希望为各种DSL提供一种中间表达形式，将他们集成为一套生态系统，使用一种一致性强的方式编译到特定硬件平台的汇编语言上。

关键的MLIR概念：operations，regions，dialects

# 3 Toy Language

https://mlir.llvm.org/docs/Tutorials/Toy/

通过介绍toy语言的编译流程，介绍MLIR的概念。

具体来说，Toy Tutorial展示Dialect是如何在Toy语言的分析、表达式变型、Lowering到LLVM的过程中发挥作用。

## 3.1 各章节介绍

<img src="C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211011102047952.png" alt="image-20211011102047952" style="zoom: 67%;" />

1. 定义Toy语言，并介绍其抽象语法树（AST）。

2. **添加Dialect描述**，分析AST并且生成**MLIR表达式**（最初步的表达式）。

3. 处理表达式的冗余，针对各个冗余的operation进行表达式变型。

4. **使用已有的接口完成泛化的表达式变型**。步骤2中的表达式变型是针对operation的，针对性强，重用性不足。但是一些表达式变型是需要重用的，当大多数operation都需要该变型时，使用接口更加方便。

5. MLIR表达式进行**部分Lowering**，并进行优化。

   将Toy Dialect的**部分**Operation映射到Affine Dialect Operation ，然后对Affine MLIR表达式进行优化。这一章节体现了**多次逐级向下抽象（multi-level）**的思想。一部分一部分映射，而不是映射整个表达式，解决了跨度比较大的问题。

6. 将上一步得到的**混合Dialect MLIR表达式**，进一步Lowering到**LLVM IR Dialect MLIR表达式**，最后翻译到LLVM IR表达式。

7. 在现有的编译流程中添加自定义的数据类型。

## 3.2 具体过程

示例文件存放位置：

![image-20211011111044996](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211011111044996.png)

### 1、生成AST

官方文档：

![image-20211011145437872](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211011145437872.png)

进入相应的目录下，可以看到toyc-ch1这个脚本文件

![image-20211011145653696](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211011145653696.png)

运行脚本文件，生成ast.toy的抽象语法树

![image-20211011145315156](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211011145315156.png)

### 2、生成基本的MLlR表达式

#### Dialects的理解：

使用其他编译器从AST lowering 到LLVM IR 跨度过大。Dialects 是**对应IR的抽象**，不同Dialect之间的转换跨度比较小（逐级向下抽象），从群雄割据到一统江湖？

![image-20211012205738852](../../../../AppData/Roaming/Typora/typora-user-images/image-20211012205738852.png)

可以使用两种方法定义：C++直接定义 、通过tablegen定义

- dialect的C++定义（Ch2/mlir/**Dialect.cpp**）：

  ![image-20211012094441021](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211012094441021.png)

- dialect的tablegen定义（在项目的头文件Ch2/include/**ops.td**中）

  ![image-20211012095235353](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211012095235353.png)

​		总结：(https://zhuanlan.zhihu.com/p/102212806) dialect是对某一类IR或者某一部分内容的抽象，比如llvm dialect就是对llvm ir的一种抽象，vector dialect就是对向量部分的一种抽象，使用mlir的各种基础设施就可以对dialect进行定义，也可以进行dialect之间的转换或者lowering。

#### Operations的理解：

​		在MLIR中，Operations是**抽象和计算的核心单元**，与LLVM的指令在许多方面具有相似性。

<img src="C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211012164151809.png" alt="image-20211012164151809" style="zoom: 67%;" />

​		在MLIR中有两个类来完成Operation的数据结构： `Operation`和 `Op`，`Operation`定义在`mlir/include/mlir/IR/Operatoin.h` 中， `Op`的定义在 `mlir/include/mlir/IR/OpBase.td`中

eg：Toy中transpose函数的MLIR operations：

```
%t_tensor = "toy.transpose"(%tensor) {inplace = true} : (tensor<2x3xf64>) -> tensor<3x2xf64> 
loc("example/file/path":12:1)
```

定义Dialect和Operations：在 `/llvm-project/mlir/examples/toy/` 目录下的C++项目中定义

<img src="C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211012153432679.png" alt="image-20211012153432679" style="zoom:67%;" />

#### 生成：

<img src="C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211012170050498.png" alt="image-20211012170050498"  />



### 3、MLIR表达式变型

**目的：**

MLIR是一个看重代码优化的编译框架，因此在我们生成了MLIR表达式之后，就要进行进一步的处理，将MLIR表达式变型，从而实现优化的目的。

**两个关键步骤：**

表达式的匹配和重写。

**两种方法：**

1. 直接用C++编写表达式的匹配和重写函数。	
2. 使用Declarative Rewrite Rules（DRR）规则来定义重写规则，并使用ODS框架来自动生成代码。

#### 1 使用C++

举例说明

```toy
//Toy语言代码，这是一条冗余的代码，转置X两次，相当于没转置。
def transpose_transpose(x) {
  return transpose(transpose(x));
}
```

未优化时的MLIR表达式：

```
func @transpose_transpose(%arg0: tensor<*xf64>) -> tensor<*xf64> {
  %0 = toy.transpose(%arg0 : tensor<*xf64>) to tensor<*xf64>
  %1 = toy.transpose(%0 : tensor<*xf64>) to tensor<*xf64>
  toy.return %1 : tensor<*xf64>
}
```

在ToyCombine.cpp文件中定义rewriter

```c++
/// Fold transpose(transpose(x)) -> x
struct SimplifyRedundantTranspose : public mlir::OpRewritePattern<TransposeOp> {
  /// We register this pattern to match every toy.transpose in the IR.
  /// The "benefit" is used by the framework to order the patterns and process
  /// them in order of profitability.
  SimplifyRedundantTranspose(mlir::MLIRContext *context)
      : OpRewritePattern<TransposeOp>(context, /*benefit=*/1) {}

  /// This method is attempting to match a pattern and rewrite it. The rewriter
  /// argument is the orchestrator of the sequence of rewrites. It is expected
  /// to interact with it to perform any changes to the IR from here.
  mlir::LogicalResult
  matchAndRewrite(TransposeOp op,
                  mlir::PatternRewriter &rewriter) const override {
    // Look through the input of the current transpose.
    mlir::Value transposeInput = op.getOperand();
    TransposeOp transposeInputOp = transposeInput.getDefiningOp<TransposeOp>();

    // Input defined by another transpose? If not, no match.
    if (!transposeInputOp)
      return failure();

    // Otherwise, we have a redundant transpose. Use the rewriter.
    rewriter.replaceOp(op, {transposeInputOp.getOperand()});
    return success();
  }
};
```

在主文件toyc.cpp中增加优化管道。

```
mlir::PassManager pm(module.getContext());
  pm.addNestedPass<mlir::FuncOp>(mlir::createCanonicalizerPass());
```

##### 生成：

执行脚本 `toyc-ch3 test/Examples/Toy/Ch3/transpose_transpose.toy -emit=mlir -opt`

![image-20211012144725228](C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211012144725228.png)

发现问题：仍有一条 tensor<*xf64> -> tensor< *xf64> 没被消除，这是因为MLIR考虑到operations可能有副作用。我们可以添加新的trait `NoSideEffect` 到`TransposeOp`来解决这个问题。

```
def TransposeOp : Toy_Op<"transpose", [NoSideEffect]> {...}
```

#### 2 使用DRR规则

DRR：Declarative, rule-based pattern-match and rewrite 

第二种方式是使用基于规则的方式来自动生成表达式匹配和重写函数，代码生成的部分还是使用ODS框架进行实现

The automatically generated C++ code corresponding to each of the DRR patterns can be found under `path/to/BUILD/tools/mlir/examples/toy/Ch3/ToyCombine.inc`.

举例：

Toy源代码：

```
def main() {
  var a<2,1> = [1, 2];
  var b<2,1> = a;	//reshape是冗余代码
  var c<2,1> = b;	//reshape是冗余代码
  print(c);
}
```

##### 生成MLIR并且优化过后的代码：

<img src="C:\Users\yyyyyyyyyyyy\AppData\Roaming\Typora\typora-user-images\image-20211012165158268.png" alt="image-20211012165158268" style="zoom: 67%;" />

### 4、使用接口，完成泛化的表达式变型

#### **# 参考链接：**

https://zhuanlan.zhihu.com/p/361976985

https://zhuanlan.zhihu.com/p/106472878

#### # 动机：

尽管不同方言可能代表着不同的抽象，有一系列转换和分析是我们需要重复使用的。如果我们为每一个dialect都执行这些转换，会导致很多冗余。

**## 生成代码的准备：推断Tensor的shape**：

采用下面的例子（**同时也用于5、6章节**）来进行具体介绍:

```
//toy语言源文件
def multiply_transpose(a, b) {
  return transpose(a) * transpose(b);
}

def main() {
  var a<2, 3> = [[1, 2, 3], [4, 5, 6]];
  var b<2, 3> = [1, 2, 3, 4, 5, 6];
  var c = multiply_transpose(a, b);
  var d = multiply_transpose(b, a);
  print(d);
}
```

对于这样的例子，当我们不进行任何优化生成MLIR表达式的时候，除了在实例化tensor的时候，其他时候并不能知道tensor的shape信息。如下图所示，使用的是泛化的tensor类型。

![preview](https://pic2.zhimg.com/v2-f9d045c41cec63750ed9842de701f37d_r.jpg)

推断每一个tensor的shape信息：在第三章的优化之前增加**两个pass**（分别运用了Dialect接口和Operation接口），首先进行**内联**，然后**推断运算数据的shape**，最后再消除冗余进行规范化。

![img](https://pic4.zhimg.com/80/v2-647e52ba8ed0db96055464013d3a2713_720w.jpg)

#### # inlining pass：

使用內联pass，可以用定义的函数体替代函数的调用。对于`multiply_transpose` 这种函数，函数的调用、返回有关的准备和收尾工作的代码往往比函数体本身的代码要大得多。因此，对于这类简单的、使用频繁的小函数，将之声明为内联函数可提高运行效率。

MLIR提供了一个dialect可以立刻使用的内联传递（inlining pass），Toy dialect只需要提供一个接口来执行这个内联传递。



##### ## 内联传递的具体实现

以模块的方式介绍

<img src="https://pic4.zhimg.com/v2-882e96132eed84a8cab08dec7a66d53b_r.jpg" alt="preview" style="zoom:67%;" />

1. **Dialect模块（Dialect.cpp）**中的操作，两部分：定义内联操作的约束（变形规则）、添加上述定义的变形规则

   - **实现内联pass的表达式变形规则**。使用**Dialect接口**`DialectInlinerInterface`。有两个重载函数`isLegalToInline`，分别是两个钩子；第一个钩子：检查给定的可调用操作内联到给定调用中是否合法，检查能不能内联；第二个钩子：检查给定的操作是否合法地内联到给定的区域。

     ```cpp
     /// This class defines the interface for handling inlining with Toy operations.
     /// We simplify inherit from the base interface class and override
     /// the necessary methods.
     struct ToyInlinerInterface : public DialectInlinerInterface {
       using DialectInlinerInterface::DialectInlinerInterface;
     
       /// This hook checks to see if the given callable operation is legal to inline
       /// into the given call. For Toy this hook can simply return true, as the Toy
       /// Call operation is always inlinable.
       bool isLegalToInline(Operation *call, Operation *callable,
                            bool wouldBeCloned) const final {
         return true;
       }
     
       /// This hook checks to see if the given operation is legal to inline into the
       /// given region. For Toy this hook can simply return true, as all Toy
       /// operations are inlinable.
       bool isLegalToInline(Operation *, Region *, bool,
                            BlockAndValueMapping &) const final {
         return true;
       }
     
       /// This hook is called when a terminator operation has been inlined. The only
       /// terminator that we have in the Toy dialect is the return
       /// operation(toy.return). We handle the return by replacing the values
       /// previously returned by the call operation with the operands of the
       /// return.
       void handleTerminator(Operation *op,
                             ArrayRef<Value> valuesToRepl) const final {
         // Only "toy.return" needs to be handled here.
         auto returnOp = cast<ReturnOp>(op);
     
         // Replace the values directly with the return operands.
         assert(returnOp.getNumOperands() == valuesToRepl.size());
         for (const auto &it : llvm::enumerate(returnOp.getOperands()))
           valuesToRepl[it.index()].replaceAllUsesWith(it.value());
       }
     };
     ```

   - 在 Toy Dialect 注册界面中添加上述定义的变形规则，下图中为 登记 ToyInlinerInterface

     ```cpp
     /// Dialect creation, the instance will be owned by the context. This is the
     /// point of registration of custom types and operations for the dialect.
     ToyDialect::ToyDialect(mlir::MLIRContext *ctx)
         : mlir::Dialect(getDialectNamespace(), ctx, TypeID::get<ToyDialect>()) {
       addOperations<
     #define GET_OP_LIST
     #include "toy/Ops.cpp.inc"
           >();
       addInterfaces<ToyInlinerInterface>();
     }
     ```

2. **Operatin模块 （Ops.td）**

   - 确定在哪里内联，即要知道在main函数中调用`multiply_transpose`的位置。使用**Operation接口**`CallOpInterface`,对函数调用进行标记。

   - 添加 **cast operation**，将变量确定的类型转化为泛化的类型。动机：将变量确定的类型转化为泛化的类型。

      ```
      def CastOp : Toy_Op<"cast", [
          DeclareOpInterfaceMethods<CastOpInterface>,
          NoSideEffect,
          SameOperandsAndResultShape]
        > {
        ...
      }
      ```

3. **PassManager模块 （toyc.cpp）**

   - 将内联传递（inliner pass）添加到优化管道（optimization pipeline）中

##### **## 结果：**

实现**内联操作但未完成Shape推断**的MLIR表达式：

![img](https://pic1.zhimg.com/80/v2-2864a3af0c5a861f9a7056bd850fa90c_720w.jpg)

#### # Shape推断Pass

上一步通过castOp已经将内联中确定类型的tensor转变成了泛化类型的tensor。

##### **## 现在的目标：**

根据那些确定类型tensor，将那些泛化的tensor都转变为确定类型的tensor。

##### **## 实现思路：**

这里我们将要用ODS框架来生成自定义的**Operation接口**（作用在特定的Operation上），这个接口就是用来推测tensor的shape类型。整个Shape推断也将会编写为一个pass作用在MLIR表达式上。

##### **==## 具体步骤==：**

![img](https://pic1.zhimg.com/80/v2-0b2a2c6b25d40dcda1485bfc5acb2dd0_720w.jpg)

1. 使用**ODS（Operation定义规范）框架**的规则编写Shape推断的接口模块。文件：llvm-project/mlir/examples/toy/Ch4/include/toy/**ShapeInferenceInterface.td**

   ```
   def ShapeInferenceOpInterface : OpInterface<"ShapeInference"> {
     let description = [{
   		Interface to access a registered method to infer the return types for an
   		operation that can be used during type inference.
   	}];
   
   	let methods = [
   		InterfaceMethod<"Infer and set the output shape for the current operation.",
   										"void", "inferShapes">
   	];
   }
   ```

2. 将特征添加到需要的**Operation定义**中.

   动机：指定哪些Operation将会使用Shape推断接口。

   文件：**Ops.td**

   ```
   def MulOp : Toy_Op<"mul",
       [..., DeclareOpInterfaceMethods<ShapeInferenceOpInterface>]> {
     ...
   }
   ```

3. **定义对应操作的形状判断函数**。为各个使用Shape推断接口的Operation，实现接口中的`inferShapes`函数。文件：**Dialect.cpp**

   如`MulOp`运算符，将其结果形状推断为输入的形状。

   ```cpp
   /// Infer the output shape of the MulOp, this is required by the shape inference
   /// interface.
   void MulOp::inferShapes() { getResult().setType(getOperand(0).getType()); }
   ```

   **上述代码简述**：`getResult().setType()`获取结果并设置结果的数据类型，将其设置为：第一个操作数`getOperand(0)`（即第一个输入参数）的数据类型。注意，此处的`MulOp`对应的操作是对应元素相乘，所以输入两个参数的数据类型必是同型的，取第一个即可。

4. ==定义==一个Shape推断接口的类，提供Shape推断算法。 文件：ShapeInferencePass.cpp

   ![img](https://pic1.zhimg.com/80/v2-d7c171eb4eec42d97be6e00f887150f4_720w.jpg)

5. ==**经过前四步的准备**==，现在可以创建`ShapeInferencePass`，并在PassManger中添加。

   

##### ## 最终结果：

![img](https://pic3.zhimg.com/80/v2-7e7a80c83f4d3b0ba04705a21b3516ea_720w.jpg)

### 5、MLIR表达式优化--部分Lowering

部分lowering指的是从一个高级别的Dialect到一个低级别的Dialect的过程中，**可以只Lowering其中的一部分Operation，剩下的Operation只需要进行升级**，与其他dialect共存。

在第四章的例子中，最后得到如下所示的MLIR表达式

![preview](https://pic2.zhimg.com/v2-6ef861ad1f2176052bf096664dd53b95_r.jpg)

使用MLIR提供的仿射变换Dialecr：`Affine`，这个Dialect适合处理计算集中型的Operation，但是不支持Toy中的`print`，因此我们需要对`PrintOp`进行升级（在Ops.td文件中）。使得`print`Operation可以接受`F64MemRef`类型的参数，也就与`Affine Dialect`可以共存了。

<img src="https://pic2.zhimg.com/80/v2-96c0657c0a1f00b80ceb69d84e52c989_720w.jpg" alt="img" style="zoom:80%;" />

#### # ==ToyToAffine模块==

在ToyToAffine模块中，识别出原有Toy Operation，使用新的Affine Operation进行替换，除此之外，我们需要在编写Pass时添加新的Dialect，禁用原来的Dialect，这样一来就完成了Lowering操作。

#### # 具体实现

##### **第一步，定义转换目标（Conversion Target）**

**目标**：为了实现进一步转换。

**实现：**将 Toy Dialect 中计算密集操作转换为 Affine、MemRef 和 Standard Dialects 的操作组合。

**文件：** LowerToAffineLoops.cpp

```cpp
void ToyToAffineLoweringPass::runOnFunction() {
  // The first thing to define is the conversion target. This will define the
  // final target for this lowering.
  mlir::ConversionTarget target(getContext());

  // We define the specific operations, or dialects, that are legal targets for
  // this lowering. In our case, we are lowering to a combination of the
  // `Affine`, `MemRef` and `Standard` dialects.
  target.addLegalDialect<mlir::AffineDialect, mlir::memref::MemRefDialect,
                         mlir::StandardOpsDialect>();

  // We also define the Toy dialect as Illegal so that the conversion will fail
  // if any of these operations are *not* converted. Given that we actually want
  // a partial lowering, we explicitly mark the Toy operations that don't want
  // to lower, `toy.print`, as *legal*.
  target.addIllegalDialect<ToyDialect>();
  target.addLegalOp<PrintOp>();
  ...
}
```

##### 第二步，明确转换模式（Conversion Patterns）

使用一种新型的转换框架: ConversionPattern，与此前介绍的RewritePatterns不同点在于它接收一个额外的操作数（operands）参数。用来对新的类型的值进行操作时，匹配旧类型。

文件：LowerToAffineLoops.cpp

##### 第三步，在下降过程的模式列表中添加上述定义的转换模式

文件：LowerToAffineLoops.cpp

##### 第四步，明确对于中间表示的部分下降

```cpp
void ToyToAffineLoweringPass::runOnFunction() {
  ...
  // With the target and rewrite patterns defined, we can now attempt the
  // conversion. The conversion will signal failure if any of our *illegal*
  // operations were not converted successfully.
  auto function = getFunction();
  if (mlir::failed(mlir::applyPartialConversion(function, target, patterns)))
    signalPassFailure();
}
```

##### 第五步，**对于`toy.print`操作的定义更新**。

**动机：**在Lowering的过程中，会将数据的`TensorType`类型转换为`MemRefType`类型。但是对于`toy.print`操作，在这一部分中不想进行优化，所以需要对该Operation的定义进行更新。

```text
def PrintOp : Toy_Op<"print"> {
  ...
  // The print operation takes an input tensor to print.
  // We also allow a F64MemRef to enable interop during partial lowering.
  let arguments = (ins AnyTypeOf<[F64Tensor, F64MemRef]>:$input);
  ...
}
```

##### 第六步，**将上述定义好的内容添加到管线（pipeline）中**。

文件 toyc.cpp

```cpp
if (isLoweringToAffine) {
	mlir::OpPassManager &optPM = pm.nestmlir::FuncOp();

	// Partially lower the toy dialect with a few cleanups afterwards.
	optPM.addPass(mlir::toy::createLowerToAffinePass());
	optPM.addPass(mlir::createCanonicalizerPass());
	optPM.addPass(mlir::createCSEPass());
	...
}
```

#### # 执行命令

```
$ cd llvm-project/build/bin
$ ./toyc-ch5 ../../mlir/test/Examples/Toy/Ch5/affine-lowering.mlir -emit=mlir-affine
```

##### 结果：

```text
module  {
  func @main() {
    %cst = constant 1.000000e+00 : f64
    %cst_0 = constant 2.000000e+00 : f64
    %cst_1 = constant 3.000000e+00 : f64
    %cst_2 = constant 4.000000e+00 : f64
    %cst_3 = constant 5.000000e+00 : f64
    %cst_4 = constant 6.000000e+00 : f64
		
    %0 = alloc() : memref<3x2xf64>
    %1 = alloc() : memref<3x2xf64>
    %2 = alloc() : memref<2x3xf64>

    affine.store %cst, %2[0, 0] : memref<2x3xf64>
    affine.store %cst_0, %2[0, 1] : memref<2x3xf64>
    affine.store %cst_1, %2[0, 2] : memref<2x3xf64>
    affine.store %cst_2, %2[1, 0] : memref<2x3xf64>
    affine.store %cst_3, %2[1, 1] : memref<2x3xf64>
    affine.store %cst_4, %2[1, 2] : memref<2x3xf64>
    affine.for %arg0 = 0 to 3 {
      affine.for %arg1 = 0 to 2 {
        %3 = affine.load %2[%arg1, %arg0] : memref<2x3xf64>
	affine.store %3, %1[%arg0, %arg1] : memref<3x2xf64>
      }
    }

    affine.for %arg0 = 0 to 3 {
      affine.for %arg1 = 0 to 2 {
	%3 = affine.load %1[%arg0, %arg1] : memref<3x2xf64>
	%4 = affine.load %1[%arg0, %arg1] : memref<3x2xf64>
	%5 = mulf %3, %4 : f64
	affine.store %5, %0[%arg0, %arg1] : memref<3x2xf64>
	}
    }

    toy.print %0 : memref<3x2xf64>
    dealloc %2 : memref<2x3xf64>
    dealloc %1 : memref<3x2xf64>
    dealloc %0 : memref<3x2xf64>
    return
  }
}
```

### 6、Lowering to LLVM IR

https://zhuanlan.zhihu.com/p/108386819

上一章最后实现 Toy Dialect的**部分Operation** Lowering到 Affine Dialect和Standard Dialect，而PrintOp还是沿用Toy Dialect，输出的MLIR表达式是多种Dialect混合的形式。

**本篇目标：**将混合的MLIR表达式完全Lowering到LLVM IR Dialect ，然后生成LLVM IR （JIT）

**实现：**在PassManager模块中再添加一个**Pass**实现LLVM IR Dialect的MLIR表达式

**具体步骤：**和之前的定义Pass的过程一样，要确定Lowering target，变换操作类型，定义匹配重写的Pattern，最后执行Lowering输出MLIR表达式。

1. **确定lowering target**（文件:llvm-project/mlir/examples/toy/Ch6/mlir/LowerToLLVM.cpp）

   此处的Lowering target 就是唯一的 LLVMDialect

   ```
   ConversionTarget target(getContext());
   target.addLegalDialect<LLVM::LLVMDialect>();
   target.addLegalOp<ModuleOp, ModuleTerminatorOp>();
   ```

2. **变换操作类型**

   ```
   LLVMTypeConverter typeConverter(&getContext());
   ```

3. **定义Lowering Pattern**（==文件==）

   识别和重写当前MLIR表达式，最终生成LLVM IR Dialect的MLIR表达式。在例子中就是要为Affine Dialect和Standard Dialect，以及遗留的`Toy.PrintOp`定义匹配重写规则。其中对于Affine Dialect和Standard Dialect都有实现好的Pattern，直接可以使用。此处只需要对`Toy.PrintOp`实现Pattern即可。
   
4. **进行完全的Lowering**

   输入之前定义好的 `module`，`target`，`pattern`和`typeConverter`，调用`applyFullConversion`

   ```
   auto module = getModule();
   if (failed(applyFullConversion(module, target, patterns, &typeConverter)))
     signalPassFailure();
   ```

5. **输出LLVM IR Dialect的MLIR表达式**

   1-4步完成Pass定义。登记上述Pass，在PassManager模块中添加后，使用下面的命令输出MLIR表达式

   ```
   $ cd llvm-project/build/bin
   $ ./toyc-ch6 ../../mlir/test/Examples/Toy/Ch6/codegen.toy -emit=mlir-llvm -opt
   ```

6. **从LLVM IR Dialect的MLIR表达式生成LLVM IR表达式，或使用JIT编译引擎来执行代码**

![img](https://pic4.zhimg.com/80/v2-10ecedee5be4c6a6507616913858983b_720w.jpg)

```
$ cd llvm-project/build/bin
$ ./toyc-ch6 ../../mlir/test/Examples/Toy/Ch6/codegen.toy -emit=llvm -opt
```

附加：想要打印出最终结果，要定义MLIR的执行引擎`mlir::ExecutionEngine`，然后告诉引擎希望执行的函数，例子中是main函数：

```
auto invocationResult = engine->invoke("main");
```

使用如下命令获得最终计算结果：

```
$ cd llvm-project/build/bin
$ ./toyc-ch6 ../../mlir/test/Examples/Toy/Ch6/codegen.toy -emit=jit -opt
1.000000 16.000000 
4.000000 25.000000 
9.000000 36.000000
```

### 7、添加自定义的数据类型，以结构体为例

结构体的使用：

<img src="https://pic4.zhimg.com/80/v2-19af7c589dbe80a9b3f62fb00de9419f_720w.jpg" alt="img" style="zoom: 50%;" />

步骤：1 定义Type类 2 语法分析&打印 3 StructuralType上的操作

#### 1 定义Type类

1 在`ToyType`命名空间中，在`Types`枚举类型中添加`Struct`枚举常量。

2 构造Type实际上就是构造一个**存储实例**，因此我们构造新的数据类型时，需要基于存储类 `TypeStorage`，构造一个**存储类的衍生类**（此处为StructTypeStorage）。

3 提供一个给其他模块（如MLIRGen模块）调用的类`StructType`

<img src="https://pic1.zhimg.com/80/v2-30d82deb4c01a70ce83a9eaf55902268_720w.jpg" alt="img" style="zoom:67%;" />

#### 2 语法分析&打印

**动机：**步骤1完成后，我们可以在进行MLIR表达式的生成和变型的时候调用`StructType`，但是不能分析和输出`.mlir`文件。

**目标：**

`struct`的产生式如下所示：

```
struct-type ::= `struct` `<` type (`,` type)* `>`
```

我们要做的是可以对这样的语法结构进行语法分析，同时也可以输出这样的语法结构。

**实现：**重载`parseType`和 `printType` 这两个函数来实现在`.mlir`文件中针对`struct`的语法分析和将`struct`语法结构打印到`.mlir`文件中。

<img src="https://pic3.zhimg.com/80/v2-12aa80c2071ad84cb2857365b9c02d8e_720w.jpg" alt="img" style="zoom: 80%;" />

#### 3 StructType上的操作

**已完成内容：**

定义`struct`数据类型，并且可以对它进行分析和打印

**当前目标：**

让Operation可以使用`struct`数据类型

**实现：**

**3.1** 在Operation模块（Ops.td）中添加`Toy_StructType`, 然后将其添加到`Toy_Type`中，这使得各个Operation可以使用`struct`数据类型，和`F64Tensor`同理

```
def Toy_StructType : Type<CPred<"$_self.isa<StructType>()">, "Toy struct type">;

def Toy_Type : AnyTypeOf<[F64Tensor, Toy_StructType]>;
```

**3.2** 更新一些Operation的定义，使得Operation正确使用`struct`数据类型

以`ReturnOp`为例，在参数中接收`Toy_Type`, 使`ReturnOp`可以返回`Toy_Type`中的任意类型（包括`struct`）

```
def ReturnOp : Toy_Op<"return", [Terminator, HasParent<"FuncOp">]> {
  ...
  let arguments = (ins Variadic<Toy_Type>:$input);
  ...
}
```

**3.3** 针对性地定义处理`struct`的Operation。添加两个Operation：`StructConstantOp` 用来处理结构体的常数数值，`StructAccessOp` 用来访问结构体中的成员。添加完这两个Operation后，我们就可以打印出MLIR表达式:

```
module {
  func @multiply_transpose(%arg0: !toy.struct<tensor<*xf64>, tensor<*xf64>>) -> tensor<*xf64> attributes {sym_visibility = "private"} {
    %0 = "toy.struct_access"(%arg0) {index = 0 : i64} : (!toy.struct<tensor<*xf64>, tensor<*xf64>>) -> tensor<*xf64>
    %1 = "toy.transpose"(%0) : (tensor<*xf64>) -> tensor<*xf64>
    %2 = "toy.struct_access"(%arg0) {index = 1 : i64} : (!toy.struct<tensor<*xf64>, tensor<*xf64>>) -> tensor<*xf64>
    %3 = "toy.transpose"(%2) : (tensor<*xf64>) -> tensor<*xf64>
    %4 = "toy.mul"(%1, %3) : (tensor<*xf64>, tensor<*xf64>) -> tensor<*xf64>
    "toy.return"(%4) : (tensor<*xf64>) -> ()
  }
  func @main() {
    %0 = "toy.struct_constant"() {value = [dense<[[1.000000e+00, 2.000000e+00, 3.000000e+00], [4.000000e+00, 5.000000e+00, 6.000000e+00]]> : tensor<2x3xf64>, dense<[[1.000000e+00, 2.000000e+00, 3.000000e+00], [4.000000e+00, 5.000000e+00, 6.000000e+00]]> : tensor<2x3xf64>]} : () -> !toy.struct<tensor<*xf64>, tensor<*xf64>>
    %1 = "toy.generic_call"(%0) {callee = @multiply_transpose} : (!toy.struct<tensor<*xf64>, tensor<*xf64>>) -> tensor<*xf64>
    "toy.print"(%1) : (tensor<*xf64>) -> ()
    "toy.return"() : () -> ()
  }
}
```

**3.4** 在`ToyCombine.cpp`中对上述两个Operation进行优化。再配合之前的一系列MLIR表达式变型和优化的方法（==为什么有两个优化，流程是否如下： 优化新的两个Operation->MLIR表达式变型->优化==），即可生成最终的MLIR表达式：

```
module {
  func @main() {
    %0 = "toy.constant"() {value = dense<[[1.000000e+00, 2.000000e+00, 3.000000e+00], [4.000000e+00, 5.000000e+00, 6.000000e+00]]> : tensor<2x3xf64>} : () -> tensor<2x3xf64>
    %1 = "toy.transpose"(%0) : (tensor<2x3xf64>) -> tensor<3x2xf64>
    %2 = "toy.mul"(%1, %1) : (tensor<3x2xf64>, tensor<3x2xf64>) -> tensor<3x2xf64>
    "toy.print"(%2) : (tensor<3x2xf64>) -> ()
    "toy.return"() : () -> ()
  }
}
```

![img](https://pic3.zhimg.com/80/v2-a805bb52b9010ad5fea9c273e5b49db2_720w.jpg)

