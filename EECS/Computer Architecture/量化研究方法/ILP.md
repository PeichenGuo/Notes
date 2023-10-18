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

==跳过tomasulo算法==

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