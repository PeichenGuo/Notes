---
Book: CAQA
Chapter: 3
Summary: BlaBla
---
# 概念
## 基本概念
**基本块**：内部没有出入口的程序块
15%-25%的指令是if，因此基本块大不了，因此基本块内的ILP提升不大，因此我们要将ILP做到块间并行

**循环级并行**：尝试在循环迭代间并行
比如循环展开这种
```cpp
for (int i = 0; i < 1000; i ++)
	x[i] += y[i]; // 这个是完全可以并行执行的
```
本章主要讲循环级并行

与之相对的是数据级并行[[DLP]]


# ILP的基本编译器技术
## 基于流水线调度和循环展开

## 分支预测
### 2-bit
用其他branch的结果做预测的叫Correlating Predictor
可以看出4086entry就接近理想性能，1024entry也不会车差很多
![[Pasted image 20231017154203.png]]

### Tournament predictor
多个predictor竞争
![[Pasted image 20231017154420.png]]
可以看出tournament相比另外两个有进步，但没有明显进步。
![[Pasted image 20231017154611.png]]
### Tagged Hybrid Predictor
![[Pasted image 20231018173550.png]]
pc的一段(tag)+history的一段(h)的hash做index。
结果是有matching tag中有最长history的，图中就是最右边的predictor优先。
性能显著好于gshare

### i7 branch predictor
旧架构（Nehalem microarchitecture) 2级，每级3个做tournament：
1. 2-bit
2. gshare
3. loop exit predictor。猜loop exit branch的循环次数的。

新架构(skylake) tagged hybrid

# Dynamic Scheduling: Overcoming Data Hazards With Dynamic Scheduling

![[Pasted image 20240328173716.png]]
 这个CDB比较重要，是一个后传的data path，开销可能很大

# Hardware-Based Speculation
==跳过，讲预测执行的==

# Multi-Issue Processors
![[Pasted image 20231018181544.png]]

# 动态schedule多发射预测执行
![[Pasted image 20231018185248.png]]

# _Advanced Techniques for Instruction Delivery and Speculation_

## 提高fetch宽度
### Branch Target Buffer BTB
存branch pc -> target pc的表
一种优化是存instr而非pc。这样连fetch都不用。a53用了这种技巧

### 特殊的branch predictors
#### Return Stack
在SPEC95中。15%branch有procedure return。
但处理这种return，分支预测器不好使，最强情况下也有60%。有一种stack可以处理这种情况，直接cache大部分的return address。（jalr和jal会记下一个return addr，把这个存住）
![[Pasted image 20231018191901.png]]
随着return stack增加，在4之后，预测正确率就达到了90%以上。显著强于普通预测器的60%
#### Loop Predictor
Loop退出很难被预测，因为有大量执行训练了loop。因此有专门的loop predictor。

### _Speculation: Implementation Issues and Extensions_
#### _Register Renaming Versus Reorder Buffers_
==没看==

#### _The Challenge of More Issues per Clock_
issue逻辑很复杂，一个cycle放不下。所以issue宽度变化比较小。

#### _How Much to Speculate_
预测执行的好处之一是：==可以提前让堵住pipeline的events暴露==
一些处理器只会预测执行low-cost exceptional events，比如L1C miss；开销更大的events会等其进入非预测执行的状态下。

#### _Speculating Through Multiple Branches_
可能一个fetch unit里有多个branch，预测多个branch有好处的情况有三种：
1. branch很多的情景
2. branch cluster
3. long delay function units

#### _Speculation and the Challenge of Energy Efficiency_
![[Pasted image 20231019163553.png]]
在int程序中很大一部分指令是预测错误执行的，在flt程序中有非常小一部分是预测错误执行的。
因此可以看到，在int为主的程序中，使用预测执行是肯定造成更大能耗的（执行更多的不该执行的代码），因此对于低功耗cpu来看，应该慎重考虑是不是要用预测执行。

#### _Address Aliasing Prediction_
猜load、store地址之间有没有一样。

# _Multithreading_
三种粒度：
1. fine-grained。每个cycle都切换线程。好处是可以遮掩long stall，坏处是对于execution会延长。
2. coarsed-grained。只在long stall的时候切换线程。缺点是没办法避免small stalls
3. simultaneous multithreading (SMT) 最常见。通过renaming和调度的方式来执行独立现成的多条指令

![[Pasted image 20231020143315.png]]
SMT总体上而言，对性能有较大提升，平均下来对能耗也有一定降低

# 实例
## _The ARM Cortex-A53_
![[Pasted image 20231020143720.png]]
AGU是adress generate unit，要么pc++，要么从四个predictor来：
1. 1-entry的branch target cache存branch后的两条指令.no-delay
2. 3072-entry的hybrid-predictor。优先级低于branch target cache。2-cycle delay
3. 256-entry inderect branch predictor。3-cycle delay
4. 8-entry return stack. 3-cycle delay

Branch decisions are made in ALU pipe 0, resulting in a branch misprediction. 



_penalty of 8 cycles_


