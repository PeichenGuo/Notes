---
Author: Onur Mutlu
Year: 2022
Journel/Conference: Mutlu说了算
Summary: PIM综述
Rate: 3
Question: None
Eureka: None
---
# Tags
# Abstract

# Background
## Introduction
因为data movement耗时耗力，现代的系统是compute centric的，这不一定对，可以是data centric的。
因此有了PIM和PNM——在data存储的地方进行运算，减少data 移动

PIM这个领域已经被提出很长时间了。但由于三个原因没有大规模使用：
- 在DRAM中集成比较困难
- memory scaling过去没有那么大问题
- data movement不是bottleneck

本文介绍两种：
- 修改存储单元结构，用尽可能小的修改来高效执行运算，称之为process using memory PUM
- 用memory controller来运算，称之为process near memory PNM


# Motivation
## Main Memory 现状
有四个问题：
1. 容量 延迟 带宽无法一起提升
2. DRAM更小的node会带来更低的可靠性
3. Rowhammer对于DRAM来说有较大的影响
4. DRAM闲时耗电导致过大能耗
因此需要intelligent memory controller
==这部分说的很细，但没有看，有空要看==

## 两个新技术
3D stack memory
NVM


# Solution
## PUM和MNM
![[Pasted image 20241121150610.png]]
## PUM
可以充分利用DRAM内部带宽
### RowClone
有两种bandwidth intensive memory operation：
- bulk data copy
- bulk data init
Google发现其服务器有5%的时间在单纯的做memset和memcopy
RowClone原理非常简单： 把row a copy到一个buffer里，然后再导通row b把buffer内容填入row b。
开销很低，收益很大，在11x的性能提升，74x的能耗提升，直接少了一个数量级
### Ambit
主要针对：bulk bitwise operations。这个操作在db中比较常见（bit table），除此之外就是比较特定的领域了
Accelerator-in-Memory for bulk Bitwise operations AMBit
==具体原理没看==

### SIMDRAM
简而言之是一个开发PUM的框架，可以把逻辑生成node

### Gather-scatter DRAM
解决stride存取问题。
把cacheline的内容映射到不同的chip上，确保不同stride下可以并行访问。
然后再取的时候根据stride的pattern来把分散的数据直接取成一个cacheline
![[Pasted image 20241121160246.png]]

## PNM
很多都是用3d封装的方式贴在mem上
PNM更多在于针对一个application，进行大规模数据的预处理处理。
### Tesseract：图片处理
这个是做graph processing的

==剩下没看==

## 如何支持PIM
==没看==

# Evaluation


# Unsolved Question


# Related Works
## Later Works

## Previous Works

## Similar Works
