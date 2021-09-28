---
title: 【期末复习】Verilog期末复习
date: '2019/12/29 13:33:21'
categories: 期末复习
tags: 期末复习
abbrlink: 42790
---

大三上学期期末从各路收集来的以及根据老师上课所将的内容写的 Verilog 复习总结。

<!--more-->

# 术语解释

- EDA: Electronic Design Automation 电子设计自动化
- HDL: Hardware Description Language 硬件描述语言
- ASIC: Application Specific Integrated Circuit 专用集成电路
- SoC: System on Chip 片上系统
- PLD: Programmable Logic Device 可编程逻辑器件
- CPLD: Complex Programmable Logic Device 复杂可编程逻辑器件
- FPGA: Field Programmable Gate Array 现场可编辑门阵列
- LUT: Look Up Table 查找表
- IP核: Intelligent Property Core 知识产权核


## IP核

- 分为：
  - 软 IP：用 Verilog/VHDL 等硬件描述语言描述的功能块，但并不设计用什么具体电路实现这些功能
  - 固 IP：完成了综合的模块，以网表文件的形式提交客户使用
  - 硬 IP：提供设计的最终阶段产品：掩模

# 什么是硬件描述语言？HDL 的功能和优点？
- 用形式化的方法描述硬件电路的语言称为硬件描述语言
- 功能
    * 描述电路的连接、功能、时序
    * 在不同抽象级上描述电路
    * 表达具有并行性
- 优点
    * 设计在高层次进行，与实物无关
    * 设计开发更加容易
    * 自动将高级描述映射到具体工艺实现
    * 缩小开发周期
    * 具体实现时才做出某些决定

# CPLD 与 FPGA 的区别

- 从**可编程逻辑结构**上
    * CPLD 基于乘积项的可编程逻辑机构
    * FPGA 基于查找表的可编程逻辑结构
- 从**编程方式**上
    * CPLD 叫做复杂可编程逻辑器件。特点是，编程数据在掉电时不丢失
    * FPGA 叫做现场可编程门阵列。特点是，数据在掉电后就回丢失，需要专门的数据芯片来存储其编程数据



# 自底向上与自顶向下的设计方法

- 自底向上的设计方法，是以固定功能元件为基础，基于电路板的设计方法。手工设计一般先按电子系统的具体功能要求进行功能划分，然后对每个模块画出真值表，用卡诺图进行手工逻辑简化，写出布尔表达式，画出相应的逻辑线路图，再根据此选择元器件，设计电路板，最后进行实测与调试

- 自顶向下的分析算法通过在最左推导中描述出各个步骤来分析记号串输入。将大型的数字电路设计分割成大小不一的小模块来实现特定的功能，最后通过由顶层模块调用子模块来实现整体功能，这就是Top-Down的设计思想。

# 什么是综合，与编译器有什么区别？

- 综合：将用硬件语言描述的设计转化为电路。电路中没有时间概念，是同步进行的
- 编译：将用硬件语言描述的设计转化为 CPU 可执行的代码。可得到机器码，并按时间排序
- 区别：编译得到机器码，机器码是 CPU 所执行的每一个基础动作，将这些动作在时间序列上排列起来就是程序；综合就是把设计变成了硬件电路，在时间序列上没有先后顺序

# Verilog HDL 的模块组成

- module / endmodule 引导的电路模块描述
- input / output 引导的队模块的外部端口的语句
- reg 等关键词定义的数据类型
- always@ 等关键字引导的对模块逻辑功能描述的语句

# Verilog 文字规则

- 0：二进制数 0、低电平、逻辑 0、事件为假的判断结果
- 1：二进制数 1、高电平、逻辑 1、事件为真的判断结果
- z 或 Z：高阻态或高阻值。可用 "?"代替
- x 或 X：不确定，或位置的逻辑状态

x 值和 z 值都是不分大小写

# 数据类型

数据类型是 Verilog 用来表示数字电路硬件中的物理连接、数据存储对象和传输单元等

- 线网类型（net 型）

  - 最常用的 net 型：wire

- 寄存器类型（register 型）

  - 格式：

    ```verilog
    reg 变量名1, 变量名2, ...;
    reg [msb:lsb] 变量名1, 变量名2, ...;
    ```

  - reg 型变量必须放在过程语句中， 如 initial，always 等

# parameter

- parameter：用于定义延时和变量的宽度
```verilog
parameter param1 = const_expr1,
		 ...
```

# 过程赋值 always 语句的特点

- 无限循环语句
- 具有顺序和并行双重性
- 本身是并行语句
- 只允许描述对应单一时钟的同步时序逻辑
- 不完整的条件语句可能引入时序电路

# 运算
## 条件运算符
```
条件表达式？表达式1：表达式2
（注意：问号后面为两个分支，前面为一是选择哪个分支）
```

## 相等运算符
- 相等运算 ==
- 逻辑不等 !=
- 非全等  !==
- 全等运算 === (必须考虑 x 和 z 两种状态)

## 规约运算符
- & 规约与
- ~& 规约与非
- | 规约或
- ~| 规约或非
- ^ 规约异或
- ~^ 规约异或非
```verilog
a = 1001, b = 1011;
a & b = 1001（位运算）
&a = 0 （规约运算：结果只有一位，要么 0 要么 1）
```

## 位宽、进制、数值
```verilog
4'b1010
----------
4：位宽
'b：二进制
1010：数值
```
> 没有加位宽和进制时默认为十进制

# 赋值语句

## 阻塞型赋值

- 格式：目标变量名 = 驱动表达式
- "阻塞"指在当前的赋值完成前阻塞或停止其他语句的赋值行为
- 阻塞型赋值一旦执行，目标变量被立即更新，不存在延时
- 阻塞赋值可以使用在 assign 语句中，也可以使用在过程语句中
- 在同一过程中，允许对同一目标变量多次阻塞赋值中

## 非阻塞赋值

- 格式：目标变量名 <= 驱动表达式
- "非阻塞"：存在一个特殊的延时操作，且在赋值过程中不影响其他同类语句的赋值操作
- 非阻塞赋值在执行时，目标变量并没有马上进行更新，而是等待语句块结束时统一进行赋值
- 只能用在过程语句中

# 优化设计

## 资源优化

- 资源共享
- 逻辑优化
- 串行化

## 速度优化

- 流水线设计
- 寄存器配平
- 关键路径法
- 乒乓操作法
- 加法树法

# 状态机

## 常用编码方式
- 顺序编码
- 一位热编码

## 一般形式

- 有限状态机（FSM），是表示有限个状态以及在这些状态之间的转移的动作等行为的模型
- 只要涉及触发器的电路，无论电路大小都能归结为状态机。如二分频电路、4位二进制计数器等

## 一般结构

- 从信号输出方式上分为
  - Mealy 型
    - 异步输出，输出的是当前状态和所有输入信号的函数，它的输出是在输入变化后立即发生的，不依赖时钟同步
    - 下一状态和输出都取决于当前状态和当前输入
  - Moore 型
    - 同步输出，输出的仅为当前状态的函数，在输入发生变化时必须等待时钟的到来，比 Mealy机要多等待一个时钟周期
    - 下一状态取决于当前状态和当前输入，输出取决于当前状态
- 状态机结构包含
  - 说明部分
  - 主控时序过程
  - 主控组合过程
  - 辅助过程

## 状态机设计的一般步骤

- 抽象逻辑，得出状态转换图
- 状态简化
- 状态分配
- Verilog HDL 来描述

# 按键消抖（去毛刺）

## 什么是按键抖动？如何进行按键消抖？

> 由于按键的机械触电的弹性作用，按键在按下和释放的瞬间产生接触不稳定（弹性抖动），造成电压信号的抖动现象，称为按键抖动。

按键消抖的方法：
- 延时方法去毛刺
- 逻辑方法去毛刺
- 定时方法去毛刺
    * 每隔一段时间扫描一次键盘，如果连续两次或三次所获得的按键状态相同，就认为按键保持稳定了


# 程序题

- 多路选择器：组合逻辑电路；if-else；case，条件运算符
```verilog
// 4 选 1 多路选择器
module mux41a(a, b, c, d, s1, s0, y);
    input a, b, c, d, s1, s0;
    output y;  // 输出信号
    reg[1:0] SEL; // 模块内部的暂存变量 SEL
    reg y;
    
    always @ (a, b, c, d, SEL)
    begin
    SEL = {s1, s0}; // s1,s0 并位为 2 元素矢量变量 SEL
    if(SEL == 0)
        y = a;
    else if(SEL == 1)
        y = b;
    else if(SEL == 2)
        y = c;
    else
        y = d;
    end
endmodule
```

- 计数器：时序逻辑电路；复位、使能、进（借）位
```verilog
// 万能计数器   verilog 中没有 { }, 用 begin/end 代替
module cntN(clk, en, rstn, cnt, co);
    input clk, rstn, en;
    output[M-1:0] cnt; // 2^M >= N, M 取最小值
    output co;
    reg[M-1:0] cnt;
    reg co;
    
    always @ (posedge clk or negedge rstn) // 低电平有效的异步复位
    {
        if(!rstn)
        {
            // 复位
            cnt <= 0;
            co <= 0;
        }
        else
        {
            if(en)
            {
                if(cnt == N-1)
                {
                    // 进位
                    cnt <= 0;
                    co <= 0;
                }
                else
                {
                    // 计数
                    cnt <= cnt + 1;
                    co <= 0;
                }
            }
        }
    }
endmodule
```

```verilog
module cnt8(clk, en, rstn, cnt, co);
	input clk, en, rstn;
	output[2:0] cnt;
	output co;
	reg[2:0] cnt;
	reg co;
	
	always @ (posedge clk or negedge rstn)
	begin
		if(!rstn)
		begin
			cnt <= 0;
			co <= 0;
		end
		else
		begin
			if(en)
			begin
				if(cnt == 7)
				begin
					cnt <= 0;
					co <= 0;
				end
				else
				begin
					cnt <= cnt + 1;
					co <=0;
				end
			end
		end
	end
endmodule
```

- 编码器、译码器：组合逻辑电路，和实际结合
```verilog
// 3-8 译码器  题目会给真值表
module decoder38(din, dout);
    input[2:0] din;
    output[7:0] dout;
    reg[7:0] dout;
    
    always @ (*)
    begin
        case(din)
            3'b000:dout = 8'01111111;
            3'b001:dout = 8'10111111;
            3'b010:dout = 8'11011111;
            3'b011:dout = 8'11101111;
            3'b100:dout = 8'11110111;
            3'b101:dout = 8'11111011;
            3'b110:dout = 8'11111101;
            3'b111:dout = 8'11111110;
            default:dout = 8'b11111111;
        endcase
    end
endmodule
```

```verilog
// 8-3 编码器  具体数值见真值表
module coder83(din, dout);
    input[7:0] din;
    output[7:0] dout;
    reg[2:0] dout;
    
    always @ (din)
    case(din)  // x 表示真值表中的数值
        8'bxxxxxxxx: dout<=3'b000;
        8'bxxxxxxxx: dout<=3'b001;
        8'bxxxxxxxx: dout<=3'b010;
        8'bxxxxxxxx: dout<=3'b011;
        8'bxxxxxxxx: dout<=3'b100;
        8'bxxxxxxxx: dout<=3'b101;
        8'bxxxxxxxx: dout<=3'b110;
        8'bxxxxxxxx: dout<=3'b111;
        default: dout<=3'b000;
    endcase
endmodule
```

- 移位寄存器：普通移位、循环移位；并串转换
```verilog
// 用移位寄存器设计串并转换电路
module SeriToPara(din, dout, clk, rstn);
    input clk, din, rstn;
    output[7:0] dout;
    reg[7:0] dout;
    
    always @ (posedge clk or negedge rstn)
    begin
        if(!rstn)
            dout <= 0;
        else
            dout <= {dout[6:0], din};
    end
endmodule
```

- 结构建模：利用库元件、子模块调用方式编写程序

![](https://gitee.com/weizujie/Img/raw/master/img/利用库元件、子模块调用方式编写程序.png)
```verilog
// 利用库元件、子模块调用方式编写程序
module f_adder(ain, bin, cin, cout, sum);
  input ain, bin, cin;
  output cout, sum;
  wire d, e, f;
  h_adder u1(.A(ain), .B(bin), .so(e), .co(d));
  h_adder u2(.A(e), .B(cin), .so(sum), .co(f));
  or u3(cout, d, f);
endmodule
```


- 低电平有效复位
```verilog
always @ (posedge clk or negedge rstn)
    if(!rstn)
        ...
```