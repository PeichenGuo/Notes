---
Author: "Sanyam Mehta"
Year: 2023
Journel/Conference: "HPCA"
Summary: "通过在loop中找preg redefination，在redefination的时候就释放preg。通过payload ram来存放exec返回的值。"
Rate: 2
Question: "None"
Eureka: "None"
---
### Abstract
insight：在loop中大部分逻辑寄存器都在这个循环或者下个循环里被redefination了
根据这个insight，在redefination发生的时候就可以release preg
只有在周期之间仍被使用的指令才需要被保留

prf被缩小了50%且性能有了5%的提升

### Motivation
prf需要缩小
![[Pasted image 20230415112710.png]]
如图是一个preg的生命周期。

insight1：
40%的时间在define-finishex
60%的时间在finishex-commit

insight2：
在现有的架构中，preg需要再finishEX后再deallocation，因为pid要用来wakeup

### Solution
insight1：添加了一个WID来记录不同的loop，从而可以判断不同周期间的redefination
insight2：添加了一个payload Ram来存exec后返回的值，并改用robid来做wakeup
![[Pasted image 20230415123645.png]]
比cpr[[Checkpoint Processing and Recovery---Towards Scalable Large Instruction Window Processors]]好很多
![[Pasted image 20230415123707.png]]


### Evaluation
![[Pasted image 20230415123825.png]]
在preg数都比较多的时候，cpr和srr差不多，cpr可能性能更少
但在preg只有之前的50%时，srr还能保持较好的性能
![[Pasted image 20230415124152.png]]
上面图的归一化版本

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works
在finishEx阶段deallocate
[[Checkpoint Processing and Recovery---Towards Scalable Large Instruction Window Processors]]
在finishEx阶段allocate
[[Virtual-Physical Registers]]
CPR也可以提前释放，但W1和W2是一样的，只会在同一个窗内找依赖。
[[Checkpoint Processing and Recovery---Towards Scalable Large Instruction Window Processors]]
[[Reducing the Complexity of the Register File in Dynamic Superscalar Processor]]和CPR类似，会把提前释放的放在l2prf里。
直接跳过那些会bypass且只有一个cosumer的preg
[[Spartan --- Speculative avoidance of register allocations to transient values for performance and energy efficiency,]]
互补性工作
[[A novel register renaming technique for out-of-order processors]]
#### Similar Works

