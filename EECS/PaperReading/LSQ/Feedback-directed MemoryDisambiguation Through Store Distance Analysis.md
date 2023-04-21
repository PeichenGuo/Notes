---
Author: "Changpeng Fang"
Year: 2006
Journel/Conference: "ICS"
Summary: "用软件统计store distance(一个load到其同地址store之间的store个数)，辅以微架构实现，预测load-store依赖"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
用软件统计store distance(一个load到其同地址store之间的store个数)，辅以微架构实现，预测load-store依赖

### Motivation
[[Memory Dependence Prediction using Store Sets]]实现非常复杂，需要更简单的预测方式
在SPEC2000中82%的load都只有一个store distance，因此可以用这个方式来简单预测。
这样的预测可以大幅度减少load replay


### Solution
store distance指的是一个load到其同地址store之间的store个数，如图：
![[Pasted image 20230421123711.png]]
用软件统计store distance。统计一个load到其store的store distance，如果这个distance超过一个阈值（speculative distance），则认为其不具有预测性（太长了，比如都超过ROB Size了，确实不太有预测性）
一个load的distance如果有95%都是一个值，则称这个distance是dominant distance，这个distance可以用于预测。
这个统计其实开销不是很大

微架构上实现一个store table。简单来说是一个fifo，里面存着store的id（本文实现用的是ROB ID)。在其上维护一个cur指针指向最新的store。当一个load进来的时候，可以用cur - store distance来找对应的store。
![[Pasted image 20230421124117.png]]


### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
