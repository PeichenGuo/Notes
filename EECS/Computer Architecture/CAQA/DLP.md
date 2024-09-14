---
Book: BlaBla
Chapter: 4
Summary: BlaBla
---
# Introduction
SIMD: single instruction multiple data
SIMD理论上比MIMD在能耗上更好，因为一条指令fetch可以对应多个data fetch。
# Vector Architecture
## RVV
用的是动态寄存器类型设置，可以动态配置寄存器种类和长度
RVV下经典的DAXPY问题的汇编代码如下：
![[Pasted image 20240914120935.png]]
相比之下 RV64V比RV64G少了很多次循环
另一个重要区别是流水线互锁概率。在RVG里每个周期指令都会停顿一次，比如fadd等fmul，fst等fadd；但RVV中每个向量指令只需要等一次。换句话说，RVG是每个向量元素都需要停一次，而RVV是每个向量指令停一次。在这个例子里，RVG的停顿次数比RVV要高32倍
这种元素传递被称为chaining，链接。
## Execution Time
主要分为三个部分：
- 操作数长度
- 和其他操作之间的结构冒险
- 和其他操作之间的数据冒险
（其实所有操作的执行时间都可以分为这三个部分）

所有vector machine都有多条流水线称为lane

convey是一组内部不包含结构冒险的指令 ，即可以一起执行。convey的多少可以体现代码的性能。
RAW的操作序列应该在一个convey里，这样可以做chaining操作，在不同的function unit之间可以做forwarding。最近的实现都是flexible chaining，有依赖的指令间可以任意链接。
比如下图的代码可以分为三个convoy
![[Pasted image 20240914123148.png]]
![[Pasted image 20240914123153.png]]

chime是执行一个convey需要的时间。（chime可以理解为一种时间单位）

chime模型忽略了start up开销。

## Multiple Lanes
RVV重要假设：一个向量寄存器的第N个元素只能和另一个向量寄存器的第N个元素进行运算。极大简化运算，使lanes实现成为可能。一个4lanes设计可以让32周期的chime变为8周期。
![[Pasted image 20240527170936.png]]
## Vector-Length Register（vl reg）：处理长度不等于max vector length的循环
添加一个vl寄存器来控制运算长度。
但有时候长度是编译时未知的，比如如下代码
![[Pasted image 20240914124551.png]]
n有可能大于mvl，因此用到了strip mining。strip mining是生成两个循环，一个处理mvl整数倍数的迭代，一个处理剩下的迭代。上面的代码可以翻译成下面的代码：
![[Pasted image 20240914124617.png]]


## Predicate register：处理if
## _Predicate Registers: Handling IF Statements in Vector Loops_
```cpp
for (i = 0; i < 64; i ++)
	if(x[i] != 0) // 
		x[i] = x[i] - y[i]
```
因为有if，无法像之前那样处理vector，因为并行的时候不知道该不该操作
使用vector-mask control， 有一个reg mask对应每一个element，表示是否应该操作.
上面的代码可以翻译为：
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
上面的vpne是在设置register，下面的三行直到vpdisable都是在为谓词寄存器的控制下进行的


GPU和Vector一个重要区别就在这里，vector将为此寄存器作为体系结构的一部分，通过compiler显示地操作掩码寄存器；而在GPU中由硬件操作，软件无法看到
## _Memory Banks: Supplying Bandwidth for Vector Load/Store Units_
大部分vector processor有多个memory bank，原因有三：
1. 一周期多ld st，并且memory的时钟慢于主频，需要多个memory bank来增加带宽
2. 大部分vector processor支持非连续ld st
3. 支持多核共享同一个memory system
下面的例子可以看出memory bank的需求到底有多高
![[Pasted image 20240914131359.png]]

## _Stride: Handling Multidimensional Arrays in Vector Architectures_
对于不相邻的数据，vector processor需要一种更高效的fetch方式：stride
stride reg可以记录不相邻数据间的步长，然后fetch到vec reg里，假装是相邻的数据。

引入stride会导致memory bank conflict变多，对memory bank的要求也很高。需要满足如下的条件，则会发生存储体冲突：
![[Pasted image 20240914131650.png]]
具体可见下面的例子。当步幅是bank数的倍数时，每次访问都会冲突
![[Pasted image 20240914131829.png]]


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
vldx v1, x5, v0) # Load A[K[]]集中
vld v2, x28 # Load M[]
vldi v3, x6, v2) # Load C[M[]] 集中
vadd v1, v1, v3 # Add them
vstx v1, x5, v0) # Store A[K[]] 分散
vdisable # Disable vector registers
```
其中vldx和vstx是集中-分散操作。其中元素位置都是通过offset+V\[i\]的方式实现的
![[Pasted image 20240914132227.png]]
这个gather和scatter的操作非常耗时，因为每一次访问都是独立的，甚至之间会有冲突。但后面会提到一个memory结构来解决这个问题。

## 向量体系结构变成
向量体系结构的优势是，编译器可以告诉程序员某段代码是否会向量化，还可以给出一些提示。
目前程序能否向量化的主要因素是其本身结构

#  _SIMD Instruction Set Extensions for Multimedia_
视频和音频的数据一般很有规律，因此多媒体SMID相比vector processor会有三个缺陷：
1. no vector length reg
2. no stride or gather/scatter
3. no mask reg
![[Pasted image 20231031140452.png]]

## _The Roofline Visual Performance Model_
将floating-point performance,memory performance, and arithmetic intensity结合在一起
arithmetic intensity指的是浮点操作和memory access的比例，相当于每次memory access会有多少次运算。比如稀疏矩阵是O(1)，即每次取到的都有用；dense matrix是O(n)，即每1次凑够n个浮点运算
![[Pasted image 20231031140941.png]]
![[Pasted image 20240914132631.png]]
# _Graphics Processing Units_
## GPU编程
CUDA是为了让程序员克服易购计算和多重并行而开发的编程框架
NVIDIA的并行形式的同一主题是CUDA线程，CUDA线程会被聚合然后分块，成为线程块；执行线程块的硬件叫做多线程SIMD处理器。
在CUDA下，DAXPY编程如下：
![[Pasted image 20240914133356.png]]
每个向量元素是一个线程，256个线程是一个线程块。
GPU会根据自己的线程块id x 线程块线程数量 再加上线程id，来计算出此时实际的元素索引（线程索引)，如果i在范围内，则进行计算。
可以看到 cuda可以让程序员绕过编译器和os在代码中显示地并行。
## _NVIDIA GPU Computational Structures_
![[Pasted image 20240914133919.png]]
![[Pasted image 20240914133928.png]]

![[Pasted image 20231101121842.png]]
注意memory那里的术语有较大差距。
![[Pasted image 20231101134003.png]]
对于这个8192个元素的向量乘法的代码块，被称为一个grid。
它分为了多个thread block。由于每个thread block最多512个元素，因此分为了16个thread block。thread block是被调度到一个SIMD processor的单元。
每个thread block中有多个SIMD thread(Wrap)。只有同一个thread block中的simd thread可以通过local memory做communication

有一个thread block scheduler分配thread block给simd处理器。
下面是SIMD处理器的简化框架。它运行的是被分配的一个thread block
![[Pasted image 20231101133931.png]]
一个SIMD processor。一个gpu由多个processor组成
gpu有两级调度器，第一级将thread block调度给simd processor，后一级调度simd thread (warp)。
一个SIMD thread中包含多个SIMD instruction。图中thread宽度是32，也就是32条指令，被映射到16个物理lane，因此一个thread需要2个chime运行。
warp内部会有一个scoreboard来跟踪多条simd thread，由于不同thread之间是并行的，哪个ready就可以issue哪个。
gpu会有巨大的sram，图中例子每个lane就有1024个32位reg。
在创建SIMD thread的时候就会分配一组物理寄存器，在SIMD thread exit时释放。
## _NVIDA GPU Instruction Set Architecture_
PTX指令集描述的是对单个cuda thread的操作，隐藏了很多具体的硬件指令。一个PTX指令可以扩展到多个机器指令。
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

一般来说if then else的所有指令都会被执行，
## _NVIDIA GPU Memory Structures_
![[Pasted image 20231101150121.png]]
host写gpu memory，但不写local和private。
相比使用巨大的cache，gpu用小cache和多线程来遮掩dram延时。用streaming替代package来遮掩dram delay。

## GPU vs vector processor
1. GPU有更多lane
2. Vec显式单位步幅载入和存储。
主要区别是gpu是多线程的，vector machine不是
![[Pasted image 20231101160920.png]]

# _Detecting and Enhancing Loop-Level Parallelism_
重点在检测loop-carried dependece！ 指iteration之间的data dependency
compiler做这个更方便
loop-carried dependecy经常以recurrence（重现）的方式出现，即某个变量的值由上次循环的值决定。
## _Finding Dependences_
编译器会认为所有的index索引都是affine(仿射)的：a\*i+b
因此确认dependency变成确认两个affine会不会在一个loop bound之间有一样的值

# _Cross-Cutting Issues_
## _Energy and DLP: Slow and Wide Versus Fast and Narrow_
gpu的频率相比core会低一点，以降低功耗。
## _Banked Memory and Graphics Memory_
封装（HBM）对gpu很重要

# _Fallacies and Pitfalls_
