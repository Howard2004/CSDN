> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/lgyLGY35/article/details/113930618)

[Verilog](https://so.csdn.net/so/search?q=Verilog&spm=1001.2101.3001.7020) 语法
=============================================================================

### 目录

*   [Verilog 语法](#Verilog_1)
*   *   [一、Verilog 基础知识](#Verilog_3)
    *   *   [逻辑值](#_4)
        *   [数字进制](#_7)
        *   [标识符](#_13)
        *   [数据类型](#_18)
        *   *   [寄存器类型](#_20)
            *   [线网类型](#_32)
            *   [参数类型](#_40)
        *   [运算符](#_51)
    *   [二、Verilog 程序框架](#Verilog_62)
    *   *   [Verilog 注释](#Verilog_63)
        *   [Verilog 关键字](#Verilog_65)
        *   [Verilog 程序框架](#Verilog_85)
        *   *   [模块调用、例化——模块化设计](#_120)
    *   [三、Verilog 高级知识点](#Verilog_162)
    *   *   [结构语句](#_163)
        *   *   [initial 与 always](#initialalways_164)
        *   [赋值语句](#_203)
        *   *   [阻塞赋值（Blocking）](#Blocking_205)
            *   [非阻塞赋值（Non-Blocking）](#NonBlocking_210)
            *   *   [总结](#_218)
                *   [assign 和 always 区别](#assign__always__225)
        *   [条件语句](#_237)
        *   *   [if_else 语句：](#if_else_238)
            *   [case 语句：](#case_243)
            *   [latch 锁存器](#latch_268)
    *   [四、Verilog 状态机 FSM](#VerilogFSM_272)
    *   *   [状态机 FSM 概念](#FSM_273)
        *   [状态机模型](#_276)
        *   *   [Mealy 状态机（输出 = 输入 + 之前状态）](#Mealy_277)
            *   [Moore 状态机（输出 = 之前状态）](#Moore__282)
        *   [状态机设计](#_286)
        *   *   [1. 状态空间定义](#1_292)
            *   [2. 状态跳转（时序逻辑）](#2_317)
            *   [3. 下一个状态的判断（组合逻辑）](#3_327)
            *   [4. 各个状态下的动作（组合逻辑）](#4_345)
            *   [总结](#_361)

一、Verilog 基础知识
--------------

### 逻辑值

0、1、X（未知）、Z（高阻态）

### 数字进制

二进制：4’b0101 表示 4 位二进制数字 0101；  
十进制：4’d2 表示 4 位十进制数字 2（二进制 0010）；  
十六进制：4’ha 表示 4 位十六进制数字 a（二进制 1010）  
当代码中没有指定数字的位宽与进制时，默认为 32 位的十进制，比如 100，实际上表示的值为 32’d100。

### 标识符

用于定义模块名，端口名，信号名，由字母、数字、$ 和_(下划线) 组成，第一个字符必须是字母或者下划线，区分大小写。  
**不建议大小写混合使用，普通内部信号建议全部小写，参数定义建议大写。**  
采用一些前缀或后缀，比如：时钟采用 clk 前缀：clk_50m，clk_cpu；低电平采用_n 缀：enable_n；

### 数据类型

寄存器类型、线网类型和参数类型。

#### 寄存器类型

是一个数据存储单元，常用 reg 类型，默认初始值为 X（未知）。

```
//reg define
reg [31:0] delay_cut;  //32位寄存器
reg        key_reg;    //默认位宽为 1

```

只能在 always 语句和 initial 语句中被赋值。（不允许像 C 语言一样，在定义同时赋值）  
如果该过程语句描述的是时序逻辑，即 always 语句带有时钟信号，则该寄存器变量对应为寄存器；  
如果该过程语句描述的是组合逻辑，即 always 语句不带有时钟信号，则该寄存器变量对应为硬件连线。

#### 线网类型

表示结构化实体（e.g. 门）的物理连线。不能存储值，它的值由驱动它的元件决定。驱动元件有连续赋值、门、assign 等。默认是 Z 高阻态。 有 tri 和 wire 型。

```
//wire define
wire data_en;       //数据使能信号 
wire [7:0] data ;   //数据

```

#### 参数类型

参数其实就是一个常量，用 parameter 定义（类似 C 语言中的 define）我们可以一次定义多个参数，参数与参数之间需要用逗号隔开。参数的定义是局部的，只在当前模块中有效。

```
//parameter define
parameter DATA_WIDTH = 8; //数据位宽为8位
//可以[DATA_WIDTH : 0]只修改参数值来修改位宽

```

参数型数据用于定义[状态机](https://so.csdn.net/so/search?q=%E7%8A%B6%E6%80%81%E6%9C%BA&spm=1001.2101.3001.7020)的状态、数据位宽和延迟大小等，在模块调用时可以通过参数传递来改变调用模块中已定义的参数。

### [运算符](https://so.csdn.net/so/search?q=%E8%BF%90%E7%AE%97%E7%AC%A6&spm=1001.2101.3001.7020)

**算术运算符：** Verilog 实现乘除比较浪费组合逻辑资源，尤其是除法。所以 2 的指数次幂的乘除习惯用移位，非 2 的指数次幂的乘除调用现成的 IP（ISE 提供）  
**关系运算符**  
**逻辑运算符：** ！、&& 、 ||  
**条件操作符：** a ? b : c  
**位运算符：** ~（取反）、&、 |、^（异或）  
**移位运算符：** << a << b 将 a 左移 b 位  
左移时位宽增加，右移时位宽不变。  
4‘b1001 <<2 = 6’b100100  
**拼接运算符：** {a,b} 将 a 和 b 拼接起来作为一个新信号。

二、Verilog 程序框架
--------------

### Verilog 注释

// 与 /* */

### Verilog 关键字

<table><thead><tr><th>关键字</th><th>含义</th></tr></thead><tbody><tr><td>module</td><td>模块开始定义</td></tr><tr><td>input</td><td>输入端口定义</td></tr><tr><td>output</td><td>输出端口定义</td></tr><tr><td>inout</td><td>双向端口定义</td></tr><tr><td>parameter</td><td>信号的参数定义</td></tr><tr><td>wire</td><td>wire 信号定义</td></tr><tr><td>reg</td><td>reg 信号定义</td></tr><tr><td>always</td><td>产生 reg 信号语句的关键字</td></tr><tr><td>assign</td><td>产生 wire 信号语句的关键字</td></tr><tr><td>begin</td><td>语句的起始标志</td></tr><tr><td>end</td><td>语句的结束标志</td></tr><tr><td>posedge/negedge</td><td>时序电路的标志</td></tr><tr><td>case</td><td>Case 语句起始标记</td></tr><tr><td>default</td><td>Case 语句的默认分支标志</td></tr><tr><td>endcase</td><td>Case 语句结束标记</td></tr><tr><td>endmodule</td><td>模块结束定义</td></tr></tbody></table>

### Verilog 程序框架

基本设计单元是模块（Block），类比 C 语言中的函数。  
模块由两部分组成，一部分描述接口，一部分描述逻辑功能，  
每个 Veirlog 程序包括端口定义、IO 说明、内部信号说明、功能定义。下面以程序示例讲解知识点。

```
//     模块名(端口定义);
module block(a,b,c,d);
	//IO说明
	input a,b;
	output c,d; 	
	/************************************************
	1.未说明类型就是wire类型，reg类型定义为：
		output reg [3:0] led //在位宽之前加reg
	2.端口定义和IO说明可以放在一起,例如
		module led( 
			input sys_clk , //系统时钟
		 	input sys_rst_n, //系统复位，低电平有效
		 	output reg [3:0] led //4 位 LED 灯  
		 	); 	
 	************************************************/
	
	//内部信号说明就是只在此模块内部使用的一些信号，不是输入输出
	
	/************************************************
	功能定义：3种方法：
	assign描述组合逻辑
	always描述组合/时序逻辑
	例化示例元件 例如 and #2 u1(q,a,b)   
	3种逻辑功能是并行的
	************************************************/
	//assign 给wire类型赋值
	assign c = a|b;
	assign d = a&b;
endmodule

```

#### 模块调用、例化——模块化设计

Verilog 语法中的模块例化。[FPGA](https://so.csdn.net/so/search?q=FPGA&spm=1001.2101.3001.7020) 逻辑设计中通常是一个大的模块中包含了一个或多个功能子模块，Verilog 通过模块调用或模块实例化来实现这些子模块与高层模块的连接。【把系统划分为几个模块，每个模块对应一个 module，**一个 module 一个 Verilog 程序文件**。顶层模块只做例化（调用其它模块），不做逻辑。】

首先将被调用模块例化，例化模块名加 u_

```
//顶层模块
module seg_led_static_top ( 
	input sys_clk , // 系统时钟
 	 input sys_rst_n, // 系统复位信号（低有效）
 	 output [5:0] seg_sel , // 数码管位选
 	 output [7:0] seg_led // 数码管段选  
 	);
 	 
 	 //parameter define
 	 parameter TIME_SHOW = 25'd25000_000; // 数码管变化的时间间隔 0.5s
 	
 	 //wire define
 	 wire flag; // 数码管变化的通知信号


//每隔 0.5s 产生一个时钟周期的脉冲信号
time_count #(
	.MAX_NUM (TIME_SHOW) //参数TIME_SHOW控制数码管每隔多长时间改变显示的数值，底层代码给参数MAX_NUM了值，此处也是一种参数赋值方式，就是把TIME_SHOW的值给MAX_NUM
	 ) u_time_count(  //例化模块名加_u
	.clk (sys_clk ),   //由模块代码看以看到clk是模块的输入
 	.rst_n (sys_rst_n),//所以这就是把上层模块的信号输入到底层模块，这里（）里可以是wire或者reg，而下面的output的连接必须是wire型的
	 
 	.flag (flag ) //而底层模块的输出信号是连接到了上层模块中定义的其他变量 
 	//信号传递的时候信号的位宽必须一致
 	);

//属于底层文件
//计时模块代码
 module time_count( 
 	input clk , // 时钟信号
 	input rst_n , // 复位信号
	output reg flag // 一个时钟周期的脉冲信号
);

parameter MAX_NUM = 25000_000; // 计数器最大计数值
reg [24:0] cnt; // 时钟分频计数器

```

三、Verilog 高级知识点
---------------

### 结构语句

#### initial 与 always

**initial：**  
只执行一次，立即执行，用于 tb 文件编写，产生激励信号或对 reg 变量赋初值。

```
initial begin
	sys_clk          <= 1b'0;
	touch_key        <= 1b'0;
	#10 touch_key    <= 1b'1;
end

```

**always：**  
一直执行，但需要结合一定的时间控制才有作用  
always 的时间控制可以是电平触发也可是边沿触发。可以是单个信号也可以是多个信号，多个信号用 or 连接

```
/**************边沿触发(时序逻辑行为)**************/
//用于产生 0.5 秒使能信号的计数器
 //@()里的多个事件名或信号名组成的列表叫做敏感列表，只有满足敏感列表才能执行后面的语句
always @(posedge sys_clk or negedge sys_rst_n) begin
 if (sys_rst_n == 1'b0) 
 	counter <= 1'b0;
 else if (counter_en)
 	counter <= 1'b0;
 else
 	counter <= counter + 1'b1;
end

/**************电平触发(组合逻辑行为)**************/
always @(a or b or c or d or e) begin
	out1 = a ? (b + c) : (d + e);
end
//如果输入变量很多，敏感列表就很长且易错
always @( * ) begin  //表示后面语句块中所有输入变量都是敏感的
	out1 = a ? (b + c) : (d + e);
end

```

### 赋值语句

分为阻塞赋值和非阻塞赋值；RHS（Right hand side）是赋值符号右侧信号， LHS 是左侧

#### 阻塞赋值（Blocking）

b = a;  
计算 RHS 的值并更新 LHS。

在一个 always 块中，后面的赋值语句是在前一句赋值语句结束后才开始赋值的。即 always 块内的语句是一种顺序关系。

#### 非阻塞赋值（Non-Blocking）

b <= a;  
（1）赋值开始的时候，计算 RHS；  
（2）赋值结束的时候，更新 LHS。  
在计算非阻塞赋值的 RHS 以及 LHS 期间，允许其它的非阻塞赋值语句同时计算 RHS 和更新 LHS。  
非阻塞赋值只能用于对寄存器类型的变量进行赋值，因此只能用在 initial 和 always 块等过程块中。  
![](https://img-blog.csdnimg.cn/20210222152132111.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xneUxHWTM1,size_16,color_FFFFFF,t_70)  
需要注意的是赋值语句是一直在 always 中执行，所以随着 clk 的上升沿一直来，bc 最后也都变成 a =0;

##### 总结

**1. 描述组合逻辑电路**，用阻塞赋值，比如 assign 赋值语句和不带时钟的 always 赋值语句，这种电路结构只与输入电平的变化有关系  
**2. 描述时序逻辑电路**，用非阻塞赋值，综合成时序逻辑的电路结构，比如 带时钟的 always 语句；这种电路结构往往与触发沿有关系，只有在触发沿时才可能发生赋值的变化  
注意 ：

*   同一个 always 中不要既用阻塞又用非阻塞
*   不允许在多个 always 中对同一个变量赋值（因为并行执行）

##### assign 和 always 区别

assign 不能带时钟。 always 可以带也可以不带。在 always 不带时钟时，逻辑功能和 assign 完全一致，都只产生组合逻辑。比较简单的组合逻辑推荐使用 assign 语句，比较复杂的组合逻辑推荐使用 always 语句。

```
 assign counter_en = (counter == (COUNT_MAX - 1'b1)) ? 1'b1 : 1'b0; 
 always @(*) begin
 		case (led_ctrl_cnt) 
			 2'd0 : led = 4'b0001;51 2'd1 : led = 4'b0010;
			 2'd2 : led = 4'b0100;
			 2'd3 : led = 4'b1000;54 default : led = 4'b0000;	
		endcase
end

```

### 条件语句

#### if_else 语句：

1. 必须在过程块（有 initial 和 always 语句引导的）中使用  
2. 表达式值为 X,Z 时按假处理  
3.if 之后如果多句，要用 begin end  
4. 不允许 if 嵌套

#### case 语句：

与 C 中的 case 不同，没有 switch 关键字。

```
	case (curr_st)
		 4'h0: next_st = S1;
	 	 4'h1: next_st = S2;
	 	 4'h2: next_st = S3;
	 	 4'h3: next_st = S4;
	 	 default: next_st = S0;
	 endcase

```

1. 所有表达式的位宽必须相等，不能用’bx 代替 n’bx，  
2.casez：比较时不考虑 z  
casex：比较时不考虑 z 和 x

```
reg [7:0] sel; //1100_0011
casez(sel)   //这种就不用考虑下面表达式中的高阻值
	8'b1100_zzzz:语句1；
	8'b1100_xxzz:语句2；//xx与00不相等，所以执行语句1
endcase

casex(sel)此时两个表达式相当于相同了，error！


```

#### latch [锁存器](https://so.csdn.net/so/search?q=%E9%94%81%E5%AD%98%E5%99%A8&spm=1001.2101.3001.7020)

锁存器是电平触发的存储器，是组合逻辑产生的，寄存器是边沿触发的存储器，在时序电路中使用，由时钟触发产生的。  
**if 缺少 else 分支，case 缺少 default** 分支会导致代码在综合过程中出现了 latch。latch 只在组合逻辑电路中产生，也就是只有不带时钟的 always 语句中 if 或者 case 语句不完整才会产生 latch，带时钟的语句 if 或者 case 语句不完整描述不会产生 latch。

四、Verilog 状态机 FSM
-----------------

### 状态机 FSM 概念

我们一直在强调硬件描述语言是并行执行的，所以如果按顺序流程来完成一个操作的时候，就需要很多的 if_else，降低了代码的可读性，也让代码编写难度增加（与单片机编写程序的区分）。所以就需要引入状态机，将一项功能的完成分解为若干步，每一步对应于二进制的一个状态，通过预先设计的顺序在各状态之间进行转换，状态转换的过程就是实现逻辑功能的过程，就像数电里学过的画状态转换图 / 表。  
状态机，（Finite State Machine，FSM 有限状态机）一种在有限个状态之间按一定规律转换的时序电路，是组合逻辑和时序逻辑的组合。

### 状态机模型

#### Mealy 状态机（输出 = 输入 + 之前状态）

![](https://img-blog.csdnimg.cn/20210222160127177.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xneUxHWTM1,size_16,color_FFFFFF,t_70)

*   F 是当前状态和输入信号的函数，状态是否改变、如何改变，取决于组合逻辑 F 的输出；
*   状态寄存器，其由一组触发器组成，用来记忆状态机当前所处的状态，状态的改变只发生在时钟的跳边沿；
*   状态机的输出是由输出组合逻辑 G 提供的，G 也是当前状态和输入信号的函数。

#### Moore 状态机（输出 = 之前状态）

![](https://img-blog.csdnimg.cn/20210222160431461.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xneUxHWTM1,size_16,color_FFFFFF,t_70)  
Moore 型不是没有输入，而是输出与输入无关。

### 状态机设计

**四段论：**

*   状态空间定义；
*   状态跳转；
*   下个状态判断；
*   各个状态下的动作；

#### 1. 状态空间定义

```
/************************方法1：************************/
//state space
parameter S0 = 2'b00;
parameter S1 = 2'b01;
parameter S2 = 2'b10;
parameter S3 = 2'b11;
// internal varialbe
reg [1:0] current_state;
reg [1:0] next_state;

/************************方法2：推荐************************/
//独热码：每个状态只有一个寄存器置位，译码逻辑简单
//state space
parameter S0 = 2'b1000;
parameter S1 = 2'b0100;
parameter S2 = 2'b0010;
parameter S3 = 2'b0001;
// internal varialbe
reg [3:0] current_state;
reg [3:0] next_state;


```

#### 2. 状态跳转（时序逻辑）

就是上面框图中的状态寄存器

```
always @(posedge clk or negedge rst_n) begin
	if(!rst_n)
		current_state <= S0;
	else
		current_state <= S1;

```

#### 3. 下一个状态的判断（组合逻辑）

就是上面框图中的产生下一状态的组合逻辑 F

```
always @(current_state  or input signals) begin
	case(current_state )  //强烈建议使用case 而不用if_else
		S0: begin
			if(a)
				next_state = S1;
			else 
				next_state = S0;
		S1:.......;
		S2:.......;
		S3:.......;
		default:.......;
	endcase
end

```

#### 4. 各个状态下的动作（组合逻辑）

就是上面框图中的产生输出的组合逻辑 G

```
//action 
wire qook;
assign qook = (current_state == S1) ? 1'b1 : 1'b0;

//下面是用always写上面的语句
always @(current_state ) begin
	if(current_state == S1)
		qook = 1;
	else 
		qook = 0;
end

```

#### 总结

这里我们实际用了一个三段式状态机。基本格式是：  
第一个 always 语句实现同步状态跳转；  
第二个 always 语句采用组合逻辑判断状态转移条件；  
第三个 always 语句描述状态输出 (可以用组合电路输出，也可以时序电路输出)。

*   可以在组合逻辑之后再加一级寄存器实现时序逻辑输出  
    ![](https://img-blog.csdnimg.cn/20210222213819300.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xneUxHWTM1,size_16,color_FFFFFF,t_70)
    
    1. 滤除组合逻辑输出的尖峰脉冲  
    2. 进行时序计算和约束  
    3. 对于总线形式的输出信号来说，容易使总线数据对齐，减小总线数据间的偏移，减小接收数据采样出错的频率。比如 8 位位宽信号，如果用组合逻辑，可能导致并行的 8 个数据从输入端到达输出端的时间不一致。