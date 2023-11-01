---
Book: CAQA
Chapter: 4
Summary: BlaBla
---
# Introduction
SIMD: single instruction multiple data
SIMD理论上笔MIMD在能耗上更好，因为一条指令fetch可以对应多个data fetch。
# Vector Architecture
![[Pasted image 20231026145104.png]]
scalar register 可以用作vector load/store的地址，也可以是fpfu(float point function unit)的输入

RV64V将data size和不同的vector reg联系在一起

## _Predicate Registers: Handling IF Statements in Vector Loops_
```cpp
for (i = 0; i < 64; i ++)
	if(x[i] != 0) // 
		x[i] = x[i] - y[i]
```
因为有if，无法像之前那样处理vector，因为并行的时候不知道该不该操作
使用vector-mask control， 有一个reg mask对应每一个element，表示是否应该操作
在vector engine中由engine操作，在GPU中由硬件操作
下面的vpne是在设置register

_vsetdcfg 2*FP64 # Enable 2 64b FP vector regs_
_vsetpcfgi 1 # Enable 1 predicate register_
_vld v0,x5 # Load vector X into v0_
_vld v1,x6 # Load vector Y into v1_
_fmv.d.x f0,x0 # Put (FP) zero into f0_
_vpne p0,v0,f0 # Set p0(i) to 1 if v0(i)!=f0_
_vsub v0,v0,v1 # Subtract under vector mask_
_vst v0,x5 # Store the result in X_
_vdisable # Disable vector registers_
_vpdisable # Disable predicate registers_

## _Memory Banks: Supplying Bandwidth for Vector Load/Store Units_
大部分vector processor有多个memory bank，原因有三：
1. 一周期多ld st，并且memory的时钟慢于主频，需要多个memory bank来增加带宽
2. 大部分vector processor支持非连续ld st
3. 支持多核共享同一个memory system

## _Stride: Handling Multidimensional Arrays in Vector Architectures_
对于不相邻的数据，vector processor需要一种更高效的fetch方式：stride
stride reg可以记录不相邻数据间的步长，然后fetch到vec reg里，假装是相邻的数据。

## _Gather-Scatter: Handling Sparse Matrices in Vector Architectures_
sparse matrix通常是用压缩存储方式，涉及到一个二级存储：
```cpp
for (i = 0; i < n; i=i+1)
	A[K[i]] = A[K[i]] + C[M[i]];
```
使用一个index vector，其元素是一个偏移值，该偏移值+基址就是稀疏矩阵的元素地址 
RV64V的支持如下，用vldi/vldx和vsti/vstx支持。
```asm
vsetdcfg 4*FP64 # 4 64b FP vector registers
vld v0, x7 # Load K[]
vldx v1, x5, v0) # Load A[K[]]
vld v2, x28 # Load M[]
vldi v3, x6, v2) # Load C[M[]]
vadd v1, v1, v3 # Add them
vstx v1, x5, v0) # Store A[K[]]
vdisable # Disable vector registers
```
这个gather和scatter的操作非常耗时，因为每一次访问都是独立的，甚至之间会有冲突。但后面会提到一个memory结构来解决这个问题。

#  _SIMD Instruction Set Extensions for Multimedia_
视频和音频的数据一般很有规律，因此多媒体SMID相比vector processor会有三个缺陷：
1. no vector length reg
2. no stride or gather/scatter
3. no mask reg
![[Pasted image 20231031140452.png]]

## _The Roofline Visual Performance Model_
将floating-point performance,memory performance, and arithmetic intensity结合在一起
arithmetic intensity指的是浮点操作和memory access的比例。比如稀疏矩阵是O(1)，即每次取到的都有用；dense matrix是O(n)，即每n次凑够一个浮点运算
![[Pasted image 20231031140941.png]]

# _Graphics Processing Units_
## _NVIDIA GPU Computational Structures_
![[Pasted image 20231101121842.png]]
![[Pasted image 20231101133931.png]]
一个SIMD processor。一个gpu由多个processor组成
![[Pasted image 20231101134003.png]]
## _NVIDA GPU Instruction Set Architecture_
形如：
opcode.type d, a, b, c;
其中d是dest reg，abc是三个src reg。type是数据类型：
![[Pasted image 20231101134430.png]]
![[Pasted image 20231101134448.png]]
一个实例代码：
![[Pasted image 20231101134555.png]]

## _Conditional Branching in GPUs_
GPU会用hardware来处理if.
主要的硬件有三个：
1. internal mask
2. branch synchronization stack
3. instruction markers
当某些lane在if上branch了，有些没有时，会把该指令push到stack里。此时称为diverge。当lane运行到同样的line时，叫converge，会pop这个stack。
internal mask会控制lane的开关
![[Pasted image 20231101145710.png]]
上面是一段gpu汇编程序。
setp设置了prediction reg p1。根据p1，一些会走then的代码，一些会走else的代码。

## _NVIDIA GPU Memory Structures_
![[Pasted image 20231101150121.png]]
host写gpu memory，但不写local和private。
相比使用巨大的cache，gpu用小cache和多线程来遮掩dram延时。

## GPU vs vector processor
![[Pasted image 20231101160920.png]]

# _Detecting and Enhancing Loop-Level Parallelism_
重点在检测loop-carried dependece！ 指iteration之间的data dependency
compiler做这个更方便
loop-carried dependecy经常以recurrence（重现）的方式出现，即某个变量的值由上次循环的值决定。
## _Finding Dependences_
