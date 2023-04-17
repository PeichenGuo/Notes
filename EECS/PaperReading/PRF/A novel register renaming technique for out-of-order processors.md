---
Author: "Hamid Tabani"
Year: 2018
Journel/Conference: "HPCA"
Summary: "很多指令只有一个consumer，这种情况下consumer和preducer可以共用一个preg"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
大概50%的SPECint和30%的SPECfp指令只有一个consumer，这种情况下consumer和preducer可以共用一个preg。可以提供6%的speedup，同性能下减少10.5%功耗。

### Motivation
insight1：
大概50%的SPECint和30%的SPECfp指令只有一个consumer
如何in-flight地判断是不是只有一个consumer？
1. 如果第一个consumer redefine了这个preg，就能说明这条指令不仅是第一个consumer，还是最后一个consumer，从而是唯一一个
2. 预测

情况一其实不算少见：
![[Pasted image 20230415140900.png]]
可以看到一个consumer的指令大多数都原地redefine了

![[Pasted image 20230415140956.png]]
可以再利用的中有很大一部分可以连续再利用

![[Pasted image 20230415141022.png]]
综合起来有为数不少的指令可以reusepreg

### Solution
值得一提的是，preg reuse的话，会导致wakeup alias现象，因为多个架构寄存器映射到同一个preg上了。
本文用了多余的2bit代表是第几次重用，wakeup的时候是2bit+pid来wakeup。
这个bit是可以扩展的，但2bit比较广泛，tradeoff结果
==没看具体实现==

### Evaluation
![[Pasted image 20230415141316.png]]
![[Pasted image 20230415141321.png]]
表现不错

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
