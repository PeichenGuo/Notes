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
