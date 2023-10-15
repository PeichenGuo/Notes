---
Author: "[[Kanad Ghose]]"
Year: 2004
Journel/Conference: ICCD
Summary: 提前做deallocation，但使用了一种checkpoint prf，其每个bitcell都做了备份
Rate: 2
Question: None
Eureka: None
---
### Authors
O. Ergin, D. Balkan, D. Ponomarev, and [[Kanad Ghose]]
### Abstract
一个寄存器从finish exec后到retire之间其实有很长的delay，可以提前释放。本文提供了两种提前释放的方法。
主要是提出了CPR（checkpoint physical register）这种东西，每个bitcell都有一个shadow cell做备份。
SPEC2000上可以提升23.3%

### Motivation
wb-deallocate 之间有36cycle，alloc-wb之间却只有12cycle
![[Pasted image 20230416144839.png]]
也提出了short-live的概念。当一个指令commit的时候，其rd logic reg已经被重命名了
和[[Spartan --- Speculative avoidance of register allocations to transient values for performance and energy efficiency,]]类似，不再多讲
![[Pasted image 20230416144825.png]]

### Solution
##### CRF
设计了一个CRF，checkpoint register file，其bitcell长这样：
![[Pasted image 20230416145353.png]]
当checkpoint signal rise的时候，bit cell的数据会存到shadow cell里，recover signal rise的时候，bit cell数据会被shadow cell写回。

bitcell面积大了26.5%，综合来看总体面积大了20%。
而且没加sram的port，时序上不会有太大改变。

##### 两种方法
第一种是：
1. short lived
2. 所有consumer都开始执行了。

第二种：
short live是redefination 指令commit，第二种方法是redefination指令进入issue queue就行。
这种方法比较适合小prf

==具体实现没看==
### Evaluation
方法1和2结合在一起，对于40+40的寄存器能带来25%的提升。但问题是40+40本身就小，一般prf都比这个大。
![[Pasted image 20230416150453.png]]

和[[Cherry---Checkpointed Early Resource Recycling in Out-of-order Microprocessors]]是有意义的，因为cherry也是checkpoint prf。
![[Pasted image 20230416150659.png]]
可以看出来比cherry的提升还是挺明显的。
~~但这个图画的好烂~~
### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
