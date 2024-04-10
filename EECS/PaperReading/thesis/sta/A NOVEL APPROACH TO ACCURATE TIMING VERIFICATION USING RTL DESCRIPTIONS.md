---
Author: Kaushik Roy, Jacob Abraham
Year: 1989
Journel/Conference: 26th ACM/IEEE, Design Automation Conference
Summary: BlaBlaBla
Rate: 3
Question: None
Eureka: None
---
## Authors

## Abstract

## Background

## Motivation


## Solution
### 最小覆盖指令集
def: 有个column vector，$Z_{i}(j)$的意思是第j个硬件模块$w_j$会不会被指令$z_i$激活
同理有row vector $W_j(i)$

然后Z或者$W^t$可以组成matrix M
def $M_{i,j} = 1$, if $w_j$ can be covered by $z_i$

找到一个能覆盖所有硬件w的指令z的集合L是np完全问题，启发式算法如下：
![[Pasted image 20240410143333.png]]
1找特殊的，只有特定指令z可以点亮的器件w
2、3 去重复的.  dominants的意思应该是指令j是指令i的子集，i可以完全覆盖j
4找最大交集
## Evaluation


## Unsolved Question


## Related Works
### Later Works

### Previous Works
[[A Path Selection Algorithm for Timing Analysis]]

### Similar Works
