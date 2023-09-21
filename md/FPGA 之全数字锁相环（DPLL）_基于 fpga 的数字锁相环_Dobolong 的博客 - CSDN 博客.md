> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/DBLLLLLLLL/article/details/84395583?app_version=6.1.6&code=app_1562916241&csdn_share_tail=%7B%22type%22%3A%22blog%22%2C%22rType%22%3A%22article%22%2C%22rId%22%3A%2284395583%22%2C%22source%22%3A%22qq_39636216%22%7D&uLinkId=usr1mkqgl919blen&utm_source=app)

#### 文章目录

*   *   [1. 数字锁相环的基本原理和组成](#1__1)
    *   *   [1.1 锁相环（PLL）的简介](#11_PLL_2)
        *   [1.2 锁相环（PLL）的原理和组成](#12_PLL_16)
        *   [1.2 数字锁相环（DPLL）的原理和组成](#12_DPLL_26)
    *   [2. 数字鉴相器 (DPD)](#2_DPD_34)
    *   [3. 数字振荡器 (DCO)](#3_DCO_254)
    *   [4. 数字缓冲器 (DB)](#4_DB_319)
    *   [5. 数字锁相环 (DPLL) 的实现](#5_DPLL_422)
    *   [6. 仿真测试和结果](#6__479)

### 1. 数字[锁相环](https://so.csdn.net/so/search?q=%E9%94%81%E7%9B%B8%E7%8E%AF&spm=1001.2101.3001.7020)的基本原理和组成

#### 1.1 锁相环（PLL）的简介

锁相环最开始并不是用在数字电路中，而是模拟短路中。对于模拟电路中的锁相环，大家可以参考这篇文章稍微看一下，数字和模拟只是实现的方法不同，但是原理、步骤还是共同的。关于模拟 PLL。可以参考一片文章：  
http://www.sohu.com/a/190569594_464086  
首先在学习数字锁相环之前，我们应该先了解什么是锁相环？锁相环的作用是什么？什么是锁相环，我在网上找到这么一段话：  
锁相环（PLL），顾名思义，就是为了锁定频率的。它能使受控振荡器的频率和相位均与输入参考信号保持同步。 它是一个以相位误差为控制对象的反馈控制系统，是将参考信号与受控振荡器输出信号之间的相位进行比较，产生相位误差电压来调整受控振荡器输出信号的相位，从而使受控振荡器输出频率与参考信号频率相一致。在两者频率相同而相位并不完全相同的情况下，两个信号之间的相位差能稳定在一个很小的范围内。  
上面一段话可能不容易理解，简单来说，就是现在有两个不同来源的信号：一个信号是参考信号，这个信号一般是由芯片的晶振得到的信号，它具有信号的稳定性较好等优点，但是其频率是固定不变的。另一个信号是由芯片或者模块内部的压控振荡器得到的。这种由压控振荡器得到的信号可以是某范围内的任意频率的信号，但是这种信号的稳定型较差，容易受到外界干扰。  
那么在实际使用过程中，我们需要一种频率能够变化的，同时质量较好的信号；或者对于一块芯片，我们需要不同的模块的内部时钟（这种时钟可以是压控振荡器产生）都能参考一个总的时钟来进行同步，从而避免两个模块内部时钟的差异而产生的数据传输的漂移等问题。因此，如何将压控振荡器得到的信号能够具有晶振信号的信号质量呢？  
那就是通过 PLL 锁相环来实现。只要压控振荡器产生的时钟（下称输入信号）是参考信号的整数倍（或者整除倍），那么就能将输入信号先进行分频，后得到与参考信号频率相同的时钟，将分频后的信号和参考信号进行比较，从而使分频后的信号和参考信号保持相同的稳定的频率和相位。被分频后的信号稳定，也就是间接的表示输入信号的稳定。从而我们得到了一个频率在一定范围内可变的稳定的信号。  
有上述可以看出，锁相环具有以下功能：

1.  能够将一个信号和另一参考信号同步；
2.  当这个信号是输出信号分频后得到的信号，PLL 就能够得到参考信号的倍频信号（实际上倍频器很多都是利用了这个功能）；
3.  当输入信号频率可变、分频系数可变时，PLL 就能够得到在频率一定范围内稳定信号。  
    ![](https://img-blog.csdnimg.cn/20181123155048757.?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RCTExMTExMTEw=,size_16,color_FFFFFF,t_70)

#### 1.2 锁相环（PLL）的原理和组成

锁相环（PLL）的作用我们已经大概了解了，其最主要功能的实现，是在于如何将两个频率不同、相位差始终在变化的信号，变成两个相同频率、相同相位的信号。  
这里引入一个概念，首先我们都知道，对于三角函数，只有两个同频率的三角函数才能比较其相位差。但这里的相位差是指两个正弦函数的初始相位差。而实际上根据三角函数的欧拉定义的理解来看，我们可以把三角函数看做是在某个圆上逆时针运动的点到 x 轴的距离。那么频率就是点在圆上运动的角速度，频率越大，其运动的角速度越大。相位就是点在圆上的位置，而初始相位就是点在圆上开始运动时的位置。当两个点的运动角速度相同时，我们可以得到两个点的初始位置差，就是两个正弦函数频率相同时，得到初始相位差。这个差值在运动过程中一直是不变的。但是当两个点运动角速度不同时，我们去看它的初始位置差是没有意义的，因为两个点的位置差是一直在变的，而初始位置差只是一个开始的位置差，是个不变的量，所以说对于频率不同的三角函数，我们讨论起初始相位差是没有意义的。但是不代表不能比较某一时刻两个点的位置。也就是相位差，相位是存在的。  
关于这个问题，可以参考一篇文章：  
https://wenku.baidu.com/view/00b3230f102de2bd9705886c.html  
![](https://img-blog.csdnimg.cn/20181123172632289.gif)  
现在我们假设两个点在圆上赛跑，我们想让这两个点角速度相等。那么有一个办法就是以一个点为参考，参考点角速度不变，另一个点是速度可变点。每过一段时间，观察另一个点到参考点的位置，是在前，还是在后。如果在前，就让另一个点速度慢一点；如果在后，就让另一个点速度快一点。就这样不断调整另一个点的角速度，直到每次观察两个点都处于相同的位置。这样我们就可以认为这两个点达到了相同的速度。这种方法就是利用反馈调节来实现两个信号的同频同相。也就是锁相环（PLL）的实现原理。  
首先通过一个鉴相器来得到两个信号之间的相位差。并根据相位差输出电压信号。然后通过滤波器稳压后得到稳定的电压信号，该信号驱动压控振荡器得到新的频率的信号。当两个信号存在相位差时，电压信号就会改变，从而使受控信号不断变化。直到当两个信号没有相位差时，电压信号不再改变，从而使受控信号保持当前频率，这时，受控信号不再变化了，就叫做受控信号被锁定了。  
由上所述，一个锁相环由鉴相器、滤波器、振荡器三部分组成。外部输入是参考信号，内部输入和总的输出是受控信号。

#### 1.2 数字锁相环（DPLL）的原理和组成

在数字电路中，原来模拟信号正弦波、余弦波的频率和相位变成了 0 和 1 的脉冲信号，那么我们如何理解数字信号中的频率和相位呢？对于脉冲信号来说，我们可以把频率理解为在某固定时间内脉冲出现的个数，为了方便表示，我们把上升沿的出现视为脉冲的出现，把相邻两个脉冲出现的时间 t 求倒数，就得到了该信号在这个时刻处的信号频率。而对于相位，相位差就是指，存在两个脉冲信号，以一个脉冲信号为参考，在其出现脉冲后，到另一个信号出现脉冲之间的时间差就是相位差，当另一个信号脉冲晚于参考信号脉冲出现的时间，称之为另一个信号的相位滞后于参考信号。当另一个信号的脉冲出现在参考信号之前，称之为另一个信号的相位提前于参考信号。  
上述是一种较为简单的描述方式，适合初识脉冲信号的读者理解。而实际上，对于脉冲信号的频率、相位等问题，严格来说这样理解有一点点问题，但是对于我们来搭建数字锁相环 DPLL 来说足够了。其实这种三角函数和信号之间的转化，其根本的原理来源于傅里叶变换，从而我们对一个时间域上的信号（例如脉冲信号）可以进行频率域（其代表的三角函数的合成）上的分析。这部分的内容涉及到了信号分析等方面的内容，这里暂时不做深入，但是要想充分理解信号方面的知识，学好高数、信号分析等知识是必要的。  
我们知道了在数字电路中，脉冲信号也有了频率和相位的属性。那么我们的参考信号是以来时钟源的固定频率的信号，因为信号的质量比较好，所以该信号两个脉冲之间的时间差均是相同的，误差很小。我们在参考信号出现上升沿时，观察受控信号此时的状态。如果受控信号为高电平，我们就认为此时受控信号超前于参考信号；反之，如果受控信号是低电平，则认为此时的受控信号滞后于参考信号。当出现超前状态时，鉴相器会输出一个超前信号，超前信号会作用于振荡器，使得振荡器发出的受控信号频率降低。而滞后信号会使振荡器发出的受控信号频率升高，从而实现受控信号频率的反馈调节。  
如图所示，当参考信号出现上升沿时，受控信号为低电平，此时输出一个超前信号。（由于模块只在时钟为上升沿时触发，所以超前信号的触发延迟了半个时钟周期）  
![](https://img-blog.csdnimg.cn/20181123210908234.png)  
由此我们能够大概了解了数字锁相环中如何看待脉冲信号的频率和相位，如何处理得到相位差以及相位差如何在锁相环中起作用来实现信号频率的反馈控制。同模拟的锁相环（PLL）类似，数字锁相环（DPLL）也是由：数字鉴相器（Digital Phase Detector）、数字缓冲器（Digital Buffer）、数字振荡器（Digital Controlled Oscillator）三个模块构成，其外部输入为参考信号，内部输入和输出为受控信号。下面我们就来具体讨论如何在 [FPGA](https://so.csdn.net/so/search?q=FPGA&spm=1001.2101.3001.7020) 上用 verilog 实现各个模块。

### 2. 数字鉴相器 (DPD)

实现一个数字锁相环（DPLL），最重要的部分就是实现数字鉴相器（DPD）和数字振荡器（DF）。并且，这两个模块并不是独立存在的，而是说，数字振荡器的实现方式和数字振荡器的实现方式相互影响。所以只有两个模块共同设计，才能较好的实现一个数字锁相环的功能。  
首先我们来具体讨论一下一个数字鉴相器应该具有那些功能和特性：  
顾名思义，数字鉴相器就是能够鉴别两个数字信号相位的差别，并通过信号将这种差别表示出来。由上文我们已经知道了，对于两个矩形方波信号，其相位差可以看做是两个信号先后出现上升沿（或下降沿）之间的时间差。为了方便表示，假设以其中一个信号作为参考信号，另一个信号为受控信号，当参考信号出现上升沿（或下降沿）时，观察另一个信号是否已经出现了上升沿（或下降沿）。  
如果还未出现上升沿（或下降沿），则叫做 “受控信号滞后于参考信号”，或者简称 “滞后”；如果已经出现了上升沿（或下降沿），则叫做 “受控信号提前于参考信号”，或者简称 “提前”。  
而判断上升沿（或下降沿）是否已经出现，方法就是看当参考信号出现上升沿时，受控信号是 1 还是 0：当受控信号为 0，表示上升沿还没出现，所以是 “滞后”；当受控信号为 1，表示上升沿已经出现，所以是 “提前”。对于下降沿也是按照同样的方法考虑。  
![](https://img-blog.csdnimg.cn/20181125204625425.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RCTExMTExMTEw=,size_16,color_FFFFFF,t_70)  
目前为止，我们已经有两个输入，参考信号和受控信号；两个输出，滞后信号和提前信号。如何通过 verilog 实现上述的输入输出关系呢？  
这里笔者在阅读文献和思考后，想到了采用异或与门的方法来实现。  
首先我们先讲异或与门，通过上图的描述，我们可以很容易看出来：滞后信号是参考信号与受控信号先异或，异或的结果和受控信号相与得到；提前信号是参考信号与受控信号先异或，异或的结果和参考信号相与得到。再加上一个 RST 的复位信号，我们可以得到如下电路：  
![](https://img-blog.csdnimg.cn/20181125210412848.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RCTExMTExMTEw=,size_16,color_FFFFFF,t_70)  
用 verilog 实现：

```
wire controlled_signal;   //受控信号
wire reference_signal;    //参考信号
wire rst;                 //复位信号（反）
wire Q;                   //异或后的中间信号

assign Q =( controlled_signal ^ reference_signal ) && rst;
assign lead_signal = Q && controlled_signal;
assign lag_signal = Q && reference_signal;

```

这里我们已经把受控信号、参考信号、滞后信号、提前信号之间的逻辑电路搭建出来了。当知道了当受控信号相比于参考信号是滞后的时，相当于受控信号一个周期的时间比参考信号时间长，也就相当于受控信号的频率小于参考信号。当受控信号相比于参考信号是提前的时，相当于受控信号一个周期的时间比参考信号时间短，也就相当于受控信号的频率大于参考信号。  
即：  
滞后信号——> 受控信号的频率小——> 增加受控信号的频率  
提前信号——> 受控信号的频率大——> 减小受控信号的频率  
根据这个关系，来调节受控信号的频率，从而使受控信号的频率和参考信号最终相同。  
再考虑，如果按照上述方法调节，当受控信号和参考信号频率相差很大时，就会出现刚开始有一段时间，受控信号的频率是不断变化，不可预知的。这样的调节效果实时性并不好，需要时间来稳定。因此读者想到，如果能够在参考信号出现上升沿时，就让受控信号也出现上升沿，相当于两个人在赛跑时，当一个人从起点出发时，无论另一个人在哪，强制让另一个人也回到原点，两个人一起从原点出发。这样就能使受控信号和参考信号强制达到相同的频率，只是此时受控信号的占空比不是 50%。然后再根据滞后和提前信号，调节受控信号的占空比，从而最终达到 50% 的占空比。  
按照这种方法，鉴相器就需要一个信号输出来表示上升沿的出现。再考虑到 FPGA 中的总的时钟源，我们这里采用触发的方法来实现。同时将上述的异或与门加入到代码中。得到数字鉴相器的代码：

```
module DPD(
    input    controlled_signal,        //受控信号
	input    reference_signal,         //参考信号
	input    rst,                      //复位信号（反）
	input    clk,                      //时钟源
	
	output    lead_signal,             //提前信号，表示受控信号提前于参考信号出现边沿
	output    lag_signal,              //滞后信号，表示受控信号滞后于参考信号出现边沿
	output    bothEdge                //上升沿触发信号，表示参考信号出现上升沿	
    );
	
wire Q;                                //异或的中间变量
reg	 K;                                //上升沿触发信号的边沿信号

always @ (posedge clk or negedge rst)  
begin
	if(!rst)
		K <= 'd0;
	else 
		K <= reference_signal;
end	
assign	bothEdge = (reference_signal ^ K) && reference_signal;


assign Q =( controlled_signal ^ reference_signal ) && rst;
assign lead_signal = Q && reference_signal;
assign lag_signal = Q && controlled_signal;


endmodule

```

后起：  
上述方法在思考方式上大体没错，但是在实际运用过程中发现，可能存在着受控信号先出现上升沿，从而过早的出现了提前或者滞后信号，导致数字振荡器的计数器上限呈现一个周期变化的不可控的数值的情况。为了避免这种情况，需要仔细考虑参考信号和受控信号如何生成提前和滞后信号这个问题，而不是简单的用异或来实现。因此做了一个表格来描述这种关系。

![](https://img-blog.csdnimg.cn/20181127220632297.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RCTExMTExMTEw=,size_16,color_FFFFFF,t_70)

并根据这个表格，通过类似状态机的方法，来实现提前信号和滞后信号的输出。  
代码如下：

```
module DPD_2(
    input    controlled_signal,        //受控信号
	input    reference_signal,         //参考信号
	input    rst,                      //复位信号（反）
	input    clk,                      //时钟源
	
	output    lead_signal_wire,             //提前信号，表示受控信号提前于参考信号出现边沿
	output    lag_signal_wire,              //滞后信号，表示受控信号滞后于参考信号出现边沿
	output    bothEdge                //上升沿触发信号，表示参考信号出现上升沿	
    );
	
wire Q;                                //异或的中间变量
reg	 K;                                //上升沿触发信号的边沿信号
reg  controlled_signal_last;
reg   reference_signal_last;
reg  controlled_signal_now;
reg   reference_signal_now;
reg  lead_signal;
reg  lag_signal;

assign lead_signal_wire = lead_signal;
assign lag_signal_wire = lag_signal;

always @(posedge clk or negedge rst)  
begin
	if(!rst)
		K <= 'd0;
	else 
		K <= reference_signal;
end	
assign	bothEdge = (reference_signal ^ K) && reference_signal;

always @(posedge clk or negedge rst)
begin
    if(!rst)
	begin
	    controlled_signal_last <= 0;
		controlled_signal_now <= 0;
		reference_signal_last <= 0;
		reference_signal_now <= 0;
	end
	else
	begin
	    controlled_signal_last <= controlled_signal_now;
		reference_signal_last <= reference_signal_now;
		controlled_signal_now <= controlled_signal;
		reference_signal_now <= reference_signal;
	end
end


always @(*)	
begin
    case({reference_signal_last,controlled_signal_last,reference_signal_now,controlled_signal_now})  
        4'b0000: 
		begin
		    lead_signal=0;
			lag_signal=0;
		end
        4'b0001: 
		begin
		    lead_signal=0;
			lag_signal=0;	
		end	
        4'b0010: 
		begin
		    lead_signal=1;
			lag_signal=0;	
		end	
		4'b0011: 
		begin
		    lead_signal=0;
			lag_signal=0;	
		end	
		4'b0100: 
        begin
		    lead_signal=0;
			lag_signal=0;
		end    
		4'b0101: 
		begin
		    lead_signal=0;
			lag_signal=lag_signal;
		end    
		4'b0110: 
		begin
		    lead_signal=1;
			lag_signal=0;
		end    
		4'b0111: 
		begin
		    lead_signal=0;
			lag_signal=0;
		end    
		4'b1000: 
		begin
		    lead_signal=0;
			lag_signal=0;
		end    
		4'b1001:  
		begin
		    lead_signal=0;
			lag_signal=0;
		end    
		4'b1010: 
		begin
		    lead_signal=1;
			lag_signal=0;
		end    
		4'b1011: 
		begin
		    lead_signal=0;
			lag_signal=0;
		end    
		4'b1100: 
		begin
		    lead_signal=0;
			lag_signal=0;
		end    
		4'b1101: 
		begin
		    lead_signal=0;
			lag_signal=1;
		end    
		4'b1110:
		begin
		    lead_signal=1;
			lag_signal=0;
    	end	
		4'b1111: 
		begin
		    lead_signal=0;
			lag_signal=0;
		end    
		default:
		begin
		    lead_signal=0;
			lag_signal=0;
		end	
    endcase	
end	

endmodule

```

按照上述代码写出来的数字鉴相器，具有更好的性能。

### 3. 数字振荡器 (DCO)

现在我们已经构造出来了一个数字鉴相器，接下来我们将继续探讨如何实现一个数字振荡器（DCO）。  
在 FPGA 中，实现一个固定脉冲频率的信号，我们可以通过已知的时钟源，分频得到一定频率范围内的脉冲。具体实现方法就是通过计数器的方式，当出现时钟脉冲时，计数器 + 1，计数器上限就是分频系数，当计数器的数小于上限的 1/2 时，输出 1，当计数器的数大于上限的 1/2 时，输出 0，当计数器的数超过上限时，计数器归零。这样就能实现对时钟源的分频。  
根据上述方法，只要改变计数器的上下限，就能改变分频系数，从而改变输出信号的频率。再参考上文受控信号和滞后提前信号的关系，我们就能通过根据滞后提前信号，改变计数器上下限，来实现对受控信号频率的控制。当计数器上限增加时，分频系数增加，频率减小；当计数器上限减小时，分频系数减小，频率增加；因此有：  
滞后信号——> 受控信号的频率小——> 增加受控信号的频率——> 计数器上限减小  
提前信号——> 受控信号的频率大——> 减小受控信号的频率——> 计数器上限增加  
此外根据上述对上升沿触发同步的说法，当出现上升沿触发信号时，受控信号应强制产生上升沿，即受控信号强制从该脉冲周期的开始处开始，即计数器的数回到 0 从新开始计数。  
综上所述，再加上复位信号，一个数字振荡器的所有构成就有了，据此可以写出如下代码：

```
module 	DCO(
	input					clk,                    //时钟源
	input					rst,                    //复位信号
	
	input					carryPulse,             //借位信号，相当于计数器上限-1
	input					subtractionPulse,       //进位信号，相当于计数器上限+1
	input					bothEdge,               //上升沿触发信号
	
	output	reg			    controlled_signal       //受控信号
);

parameter	C = 100;                                //分频系数
reg	[9:0]		count;                              //计数器
reg [9:0]		count_cnt;                          //计数器上限

always @ (posedge clk or negedge rst)       //计数器上限
begin
	if(!rst)
		count_cnt <= C;
	else if(carryPulse)
		count_cnt <= count_cnt - 1'b1;
	else if(subtractionPulse)
		count_cnt <= count_cnt + 1'b1;
	else
		count_cnt <= count_cnt;
end

always @ (posedge clk or negedge rst)       //计数器计数
begin
	if(!rst)
		count <= 'd0;
	else if(count >= count_cnt)
		count <= 'd0;
	else if(bothEdge)
	    count <= 'd0;
	else 
		count <= count + 1'b1;
end

always @ (posedge clk or negedge rst)       //脉冲实现
begin
	if(!rst)
		controlled_signal <= 1'd1;	
	else if(count < (count_cnt >> 1))
		controlled_signal <= 1'b1;
	else
		controlled_signal <= 1'b0;
end
endmodule


```

到这里，一个数字锁相环（DPLL）其实就已经能够实现了，因为数字滤波器（DB）只是让受控信号的抗干扰能力更强，如图所示是仿真后的结果：  
![](https://img-blog.csdnimg.cn/20181125220610230.png)

### 4. 数字缓冲器 (DB)

下面再介绍一下数字缓冲器，来使受控信号的抗干扰能力更强。前面我们知道了，持续一个时钟周期的提前信号或者滞后信号能够使数字振荡器的计数器上限加一或者减一。当我的预设的数字振荡器的计数器上限与实际的参考信号的频率对应的计数器上限两个数值相差很大时，就有可能出现锁相环调节时间过长等现象。为了解决这种情况，如果能够让原来持续一个周期的提前信号或滞后信号成倍数的增加，变成持续 n 个周期的提前信号或者滞后信号，就能够使数字振荡器的计数器上限修改更快，从而更快的到达参考频率附近。但是相应的，受控信号的频率精度就会降低。也就是说，牺牲精度，追求速度。  
同时考虑另外一种情况，如果我对速度要求不高，但是对于精度要求较高，同时在信号传输过程中可能存在干扰，导致接收到的提前信号或滞后信号不是完全真实的信号，此时就可以通过一个累加器，只有接受到 n 个周期的提前信号，或者滞后信号，才对数字振荡器输出一个进位信号或者借位信号，此时数字振荡器的计数器上限才只加减 1，这样就能有效的提高精度，减少信号干扰带来的影响。但是这种做法牺牲了数字锁相换锁定的时间。  
综上所述，一个时钟周期的提前或滞后信号，对应 n 个时钟周期的借位或进位信号，是提高锁定速度，降低锁定精度。想法，n 个时钟周期的提前或滞后信号，对应一个时钟周期的借位或进位信号，是提高锁定精度，降低锁定速度。因此在实际运用中，应该按照自己的工程需要，合理选择比值。  
上述过程的实现方法，是通过一个计数器，当接收到一个提前或滞后信号时，计数器加 a，当输出一个进位或借位信号时，计数器减 b，调节 a 和 b 的比值，就能实现上述过程。  
具体的代码实现如下：

```
module DB_2(
    input    clk,    //时钟
	input    rst,    //复位
	input    s_in_1, //输入信号——1
	input    s_in_2, //输入信号——2
	
	output    s_out_1, //输出信号——1
	output    s_out_2  //输出信号——2
    );
	
parameter	C = 15'd1;    //输入+C
parameter	D = 15'd5;    //输出-D
parameter	up_net = 15'd1000;    //溢出上限

reg    s_out_1;
reg    s_out_2;
reg  [15:0]  count_1;
reg  [15:0]  count_2;
reg  [15:0]  count_net=up_net;   
 
always @(posedge clk or negedge rst)
begin
    if(!rst)
	begin
	    count_1 <= 0;
		s_out_1 <= 0;
	end
	else if((s_in_1 == 1) && (count_1 < D))
	begin
	    count_1 <= count_1 + C;
		s_out_1 <= 0;
		if(count_1 >= count_net)    count_1 <= count_net;
	end
	else if((s_in_1 == 0) && (count_1 >= D))
	begin
	    count_1 <= count_1 - D;
		s_out_1 <= 1;
		if(count_1[15] == 1)    count_1 <= 0;		
	end
	else if((s_in_1 == 1) && (count_1 >= D))
	begin
	    count_1 <= count_1 + C - D;
		s_out_1 <= 1;
		if(count_1 >= count_net)   count_1 <= count_net;
		if(count_1[15] == 1)    count_1 <= 0;		
	end
	else
	begin
	    count_1 <= count_1;
		s_out_1 <= 0;
	end
end

always @(posedge clk or negedge rst)
begin
    if(!rst)
	begin
	    count_2 <= 0;
		s_out_2 <= 0;
	end
	else if((s_in_2 == 1) && (count_2 < D))
	begin
	    count_2 <= count_2 + C;
		s_out_2 <= 0;
		if(count_2 >= count_net)    count_2 <= count_net;
	end
	else if((s_in_2 == 0) && (count_2 >= D))
	begin
	    count_2 <= count_2 - D;
		s_out_2 <= 1;
		if(count_2[15] == 1)    count_2 <= 0;		
	end
	else if((s_in_2 == 1) && (count_2 >= D))
	begin
	    count_2 <= count_2 + C - D;
		s_out_2 <= 1;
		if(count_2 >= count_net)   count_2 <= count_net;
		if(count_2[15] == 1)    count_2 <= 0;		
	end
	else
	begin
	    count_2 <= count_2;
		s_out_2 <= 0;
	end
end

endmodule

```

数字缓冲器的效果：

1.  分时效果  
    ![](https://img-blog.csdnimg.cn/20181127215730270.png)
    
2.  倍时效果  
    ![](https://img-blog.csdnimg.cn/20181127215854645.png)
    

### 5. 数字锁相环 (DPLL) 的实现

所有的子模块都已经实现了，剩下的数字锁相环的实现，就是根据实际的要求，将上述几个模块进行例化就行。例化的代码如下：

```
module DPLL(
    input    clk,
	input    rst,
	input    reference_signal,
	
	output  controlled_signal,
    output  [15:0]		count_cnt
    );
 
wire lead_signal;
wire lag_signal;	
wire bothEdge;
	
DPD uut1(
    .controlled_signal(controlled_signal),        //受控信号
	.reference_signal(reference_signal),         //参考信号
	.rst(rst),                      //复位信号（反）
	.clk(clk),                      //时钟源
	
	.lead_signal_wire(lead_signal),             //提前信号，表示受控信号提前于参考信号出现边沿
	.lag_signal_wire(lag_signal),              //滞后信号，表示受控信号滞后于参考信号出现边沿
	.bothEdge(bothEdge)                //上升沿触发信号，表示参考信号出现上升沿	
    );	

wire lead_signal_2;
wire lag_signal_2;	
	
DB uut2(
    .clk(clk),    //时钟
	.rst(rst),    //复位
	.s_in_1(lead_signal), //输入信号——1
	.s_in_2(lag_signal), //输入信号——2
	
	.s_out_1(lead_signal_2), //输出信号——1
	.s_out_2(lag_signal_2)  //输出信号——2
    );
	
DCO uut3(
	.clk(clk),                    //时钟源
	.rst(rst),                    //复位信号
	
	.carryPulse(lag_signal_2),             //借位信号，相当于计数器上限-1
	.subtractionPulse(lead_signal_2),       //进位信号，相当于计数器上限+1
	.bothEdge(bothEdge),               //上升沿触发信号
	
	.controlled_signal(controlled_signal),       //受控信号
	.count_cnt(count_cnt)                //计数器上限
);
	
	
endmodule

```

### 6. 仿真测试和结果

仿真代码如下：

```
module test_DPLL;

	// Inputs
	reg clk;
	reg rst;
	reg reference_signal;

	// Outputs
	wire controlled_signal;
	wire [15:0] count_cnt;

	// Instantiate the Unit Under Test (UUT)
	DPLL uut (
		.clk(clk), 
		.rst(rst), 
		.reference_signal(reference_signal), 
		.controlled_signal(controlled_signal), 
		.count_cnt(count_cnt)
	);
	
always #1 clk=~clk;
always #51000 reference_signal=~reference_signal;

initial begin
    clk=0;
	reference_signal=0;
	rst=0;
	
	
	#120
	rst=1;
	

end
      
endmodule

```

效果：

![](https://img-blog.csdnimg.cn/20181127223931382.png)

如上图所示，能够实现控制信号跟随参考信号。