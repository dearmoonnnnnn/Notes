# 零、概念和问题

## 概念

##### 1、FPGA

`Field-Programmable Gate Array`,现场可编程门阵列。是一种可以被用户在购买后可以重新编程的集成电路。

##### 2、EP4CE6

EP4CE6是来自Altera的Cyclone IV系列FPGA芯片的一个型号。

##### 3、verilog

是一种硬件描述语言(Hardware Description Language,HDL)，用于描述、设计和验证数字电路和系统。它与VHDL一起是现代FPGA和ASIC设计的两种最常用的HDL。

它不仅可以描述电路的行为，还可以描述电路的结构和时许。

verilog的一些关键点和用途：

1. **模块化结构**：Verilog代码通常被组织成模块(modules),每个模块都可以表示一个电路部件或一个更复杂的系统。
2. **描述层次**：Verilog可以从三个不同层次描述硬件，包括门级(gate-level)、数据流级(dataflow-level)和行为级(behavioral-level)
3. 。。。

使用的EDA工具：Quartus开发软件、ModelSim仿真软件、Vivado绘图软件等

##### 4、边沿触发

信号边沿指信号从低电平到高电平的上升沿或从高电平到低电平的下降沿。

## 问题

##### 1、

# 一、开发环境

**软件介绍**

- Quartus II  的主要功能和用途
  1. **设计输入**：允许用户使用硬件描述语言(如Verilog)输入他们的设计。
  2. **综合**：Quartus将用户的HDL代码总和为一个可以在FPGA上实现的网表。
  3. **布局与布线**：一旦设计被总和，Quartus会执行布局与布线，确定逻辑元素在FPGA的实际位置以及他们之间的互连。
  4. **时序分析**：使用内置的TimeQuest

**硬件介绍**

使用的实验仪为 `SUN ESX86COP`，实验仪分为**微机原理**和**组成原理**两部分。

实验仪中的`FPGA`模块

**型号：EP4CE6**

- 2M FLASH和1M 的SRAM

- 512字节EEPROM
- 看门狗
- JTAG、AS下载口
- 1个复位按键
- 1个重配置按键
- 4个独立按键
- 4个发光二极管
- 50MHz振荡器
- 配置芯片：EPCS4

# 二、Verilog HDL

由c语言发展而来，但存在一些区别

- C语言顺序执行，描述软件，生成的代码是对硬件的操作和数据的搬移

- Verilog并行执行，描述硬件，会生成对应的硬件电路

## 2.1 基础语法

### A 逻辑值

0：逻辑低电平，条件为假

1：逻辑高电平，条件为真

z：高阻态，无驱动

x：未知逻辑电平

### B 关键字

```verilog
module example			   //module表示模块开始， example 表示模块名，主模块要和.v文件名称相同
(
    input wire  sys_clk,   //输入信号
    input wire  sys_rst_n, //输入信号
    inout wire  sda      , //输入输出信号
  	
    output wire po_flag    //输出信号
);
endmodule   

//需要一些变量和参数对输入信号进行处理，从而得到输出信号
//变量
wire  [0:0] flag;			//线网形变量，可以看成直接连接，在实际的物理电路中，表示为连线
reg   [7:0] cnt ;			//寄存器型变量，在实际的物理电路中，映射为真实寄存器

//参数
parameter CNT_MAX = 100;	//可以在顶层文件，通过实例化对功能模块中的参数进行修改
localparam CNT_MAX = 100;	//只能在模块内部使用，不能实例化

//常量
//格式：[换算为二进制后位宽的总长度]['][数值进制符号][与数值进制符号对应的数值]
//eg：8'd171 :位宽8bit，十进制的171      h：16进制; o:8进制; b: 2进制
//直接写参数，例如100，表示位宽为32bit的十进制数100
```

**具体示例**

```verilog
//子模块 slect = 0, out = A; select = 1, out = B
module mux2(A, B, select, out);			// 关键字module: 模块开始
 input A,B, select;
 output out;
 
 reg out; //在 always 块中输出的变量，即使是组合逻辑，也要定义为'reg'变量
 
 always@(A or B or select)
 	begin
 		if (!select)
			out = A;
 		else
 			out = B;
	end
endmodule								// 关键字endmodule: 模块结束	
//主模块：与、或、非、与非、或非、异或、同或运算
module ComLogic(A, B, 			// 主模块名称要与.v文件名称相同
select,
andLogic, orLogic,notLogic, nandLogic, norLogic, xnorLogic, xorLogic,
muxout);
 input A,B;
 output andLogic, orLogic,notLogic, nandLogic, norLogic, xnorLogic, xorLogic; 
input select;
 output muxout;
 assign andLogic = A & B; //and (andLogic, A, B);
 assign orLogic = A | B; //or (orLogic, A, B);
 assign notLogic = ~A; //not (notLogic, A);
 assign nandLogic = ~(A & B); //nand (nandLogic, A, B);
 assign norLogic = ~(A | B); //nor (norLogic, A, B);
 assign xnorLogic = A ^ B; //xnor (xnorLogic, A, B);
 assign xorLogic = A ^~ B; //xor (xorLogic, A, B);
 mux2 mux2(.A(A), .B(B), .select(select), .out(muxout)); //模块例化
endmodule
```

##### 赋值方式

阻塞赋值 `=`

```verilog
a = 1;
b = 2;
c = 3;
begin
    a = b;
    c = a;
end
// 顺序执行
// 赋值结果 a = 2,b = 2,c = 2
```

非阻塞赋值`<=`

```verilog
a = 1;
b = 2;
c = 3;
begin
    a <= b;
    c <= a;
end
// 并行执行
// 赋值结果 a = 2,b = 2,c = 1
```

##### always

用来描述硬件行为。它提供了一种模拟硬件在某些条件下的响应的方法。

`alway`块在某些事件发生时被出发，并执行其中的语句。

1. **触发事件**：`always`块可以通过敏感列表来定义它的触发事件(如信号的边沿变化或电平变化)

   - **边沿触发**：用`posedge`和`negedge`关键字描述

     ```verilog
     always @(posedge clk)
     ```

   - **电平触发**：直接列出信号名

     ```verilog
     always @(a or b)
     ```

2. **描述行为**：`always`块的内容可以用来描述组合逻辑或时序逻辑

   - **组合逻辑**：使用`always @(*)`或列出所有相关的信号。在这种情况下，块中的逻辑会在任何输入变化时立即响应

     ```verilog
     always @(*) begin
         y = a & b;
     end
     ```

   - **时序逻辑**：通常与边沿触发一起使用，例如描述触发器(flip-flop)或寄存器

     ```verilog
     always @(poesdge clk) begin
         if (reset)
             q <= 0;
         else 
             q <= d;
     end
     ```

##### assign

用于创建组合逻辑电路，将一个信号的值分配给另一个信号。

`assign`语句通常在模块级或数据流级描述中使用，用于连接各种信号，执行逻辑运算，从而生成一种组合逻辑的输出。

eg：

1. **信号连接**：`assign`可以用来将一个信号与另一个信号相连接。这通常将一个信号的值赋给另一个信号，从而实现信号的传递。

   ```
   assign out_signal = in_signal;
   ```

2. **逻辑运算**：可以执行逻辑运算，例如与、或、非等操作，以生成输出信号。

   ```
   assign out_signal = in_signal1 & in_signal2;
   ```

3. **多路选择**：可以用于实现多路选择器，根据某些条件选择不同的输入信号

   ```
   assign out_signal = (select_signal) ? in_signal1 : in_signal2;
   ```

4. **位运算**：可以执行位级运算，例如位与、位或、位异或等

5. **组合逻辑电路**：可以用来描述任意复杂的组合逻辑电路，包括各种逻辑门的组合，从而生成输出信号。

   ```
   assign out_signal1 = (in_signal1 & in_signal2) | (~in_signal3);
   ```

`assign`语句用于描述组合逻辑，它不会引入时序原件(如触发器)，因此输出信号会立即相应输入信号的变化。

# 三、实验

设计流程

<img src="https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20231031144423908.png" alt="image-20231031144423908" style="zoom:67%;" />

## **实验一**

编写一个2选1的多路选择器模块

编写程序，实现几个基本的门（与、或、非、与非、或非、异或、同或），并示例应用多路选择器。

**代码**：

```verilog
//子模块 slect = 0, out = A; select = 1, out = B
module mux2(A, B, select, out);
 input A,B, select;
 output out;
 
 reg out; //在 always 块中输出的变量，即使是组合逻辑，也要定义为'reg'变量
 
 always@(A or B or select)
 begin
 if (!select)
out = A;
 else
 out = B;
end
endmodule
//主模块：与、或、非、与非、或非、异或、同或运算
module ComLogic(A, B, 
select,
andLogic, orLogic,notLogic, nandLogic, norLogic, xnorLogic, xorLogic,
muxout);
 input A,B;
 output andLogic, orLogic,notLogic, nandLogic, norLogic, xnorLogic, xorLogic; 
input select;
 output muxout;
 assign andLogic = A & B; //and (andLogic, A, B);
 assign orLogic = A | B; //or (orLogic, A, B);
 assign notLogic = ~A; //not (notLogic, A);
 assign nandLogic = ~(A & B); //nand (nandLogic, A, B);
 assign norLogic = ~(A | B); //nor (norLogic, A, B);
 assign xnorLogic = A ^ B; //xnor (xnorLogic, A, B);
 assign xorLogic = A ^~ B; //xor (xorLogic, A, B);
 mux2 mux2(.A(A), .B(B), .select(select), .out(muxout)); //模块例化
endmodule
```

