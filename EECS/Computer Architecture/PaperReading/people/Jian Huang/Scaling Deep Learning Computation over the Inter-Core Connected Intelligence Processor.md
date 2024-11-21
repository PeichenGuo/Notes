---
Author: Yiqi Liu，Jian Huang
Year: 2023
Journel/Conference: RIOSJournel
Summary: 进行memory-communication tradeoff。一个核进行一个sub-task将结果传给下一个core。
Rate: 3
Question: None
Eureka: None
---
# Tags
# Abstract

# Background
现在ai的inter chip memory喜欢用VGM，virtual global memory
![[Pasted image 20240917204421.png]]
VGM有两个问题：
- inter-core communication效率低。有些核会被访问的多，有些核会被访问的少；一个tensor可能会被分散到很多core的mem，导致fetch一个tensor更复杂
- on-chip memory利用率低。core需要用到一个operator的时候，会从VGM fetch一个上来，这会导致duplicate。同时会减少on-chip memory。


# Motivation
见BG

# Solution
核心思想是：compute-shift
进行一个sub-task将结果传给下一个core。
好处有三个：
- weight tensor不会重复存储，每个核有一部分
- communication 被均匀分散在各个核
- 只需要和上下两个core沟通，避免大规模communication

需要一个trade-off：memory footprint和communication overhead。比如下图，a中的task可以有b c两种分法，b的话memory开销大（重复存储weight），c的话communication开销大（需要shift）
![[Pasted image 20240917205430.png]]

## rTensor
提出了rTensor。如图，rTensor可以切割成多个core，然后彼此见shift来用共享内存
![[Pasted image 20240917205615.png]]
下面这个图更好可以看到memory-communication的tradeoff。
![[Pasted image 20240917205919.png]]
# Evaluation


# Unsolved Question


# Related Works
## Later Works

## Previous Works

## Similar Works
