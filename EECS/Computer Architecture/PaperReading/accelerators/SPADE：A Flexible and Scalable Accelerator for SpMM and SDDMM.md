---
Author: "[[Josep Torrellas]]"
Year: 2023
Journel/Conference: ISCA
Summary: BlaBlaBla
Rate: 3
Question: None
Eureka: None
---
### Authors
[[Charith Mendis]]
[[Josep Torrellas]]

### Abstract


### Motivation
现有的SpMM(稀疏矩阵乘稠密矩阵)和SDDMM(采样稠密矩阵乘稠密矩阵)的accelerators都有以下两个问题：
1. moving data overhead 
2. marginal flexibility



### Solution
针对两个问题：
1. eliminate any data move between CPU and accelerator
2. tile-based ISA获得flexibility

#### 紧密结合
SPADE和core紧密结合，共享L2cache和L2 tlb。还有一个bypass buffer 用来可选择性地直连memory
![[Pasted image 20231017140250.png]]
程序有两个mode，cpu mode和spade mode。前者只有cpu在跑，后者只有spade在跑。程序想要跑spade的时候，pause cpu，选择性wb cache blocks，通知cpe，然后切换模式。

#### Tiled ISA 
==没看，卡不太懂==

#### architecture
![[Pasted image 20231017144057.png]]

### Evaluation


### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
