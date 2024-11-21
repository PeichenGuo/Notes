---
Author: "Srikanth T. Srinivasan"
Year: 2004
Journel/Conference: "SIGARCH"
Summary: "通过把cache miss这种long lantency的指令片段slice整体拿走，"
Rate: 4
Question: "None"
Eureka: "None"
---
### Abstract
把那些long dependency指令和其前面的指令称之为slice，类似[[Long Term Parking (LTP) --- Criticality-aware Resource Allocation in OOO Processors]]的critical指令片段。用一个SPU slice processing unit保存这些slice。
当slice被发现（load miss）之后，会把slice放到spu中。
本文实现CFP后可以有效缩减L2D的大小且不会影响过多性能，从而减少die area

### Contribution
1. non blocking prf。
2. 一种decouple prf/schedule和instruction window的办法
3. CFP可以减少cache面积，可以接受更长的memory lantency

### Motivation
long lantency的load会block住很多资源。

### Solution
CPF会提前释放slice的两种寄存器：
1. complete source reg：已经执行完的rs，且已经被slice读完了
2. dependent destination reg：slice的rd。
还会像[[Checkpoint Processing and Recovery---Towards Scalable Large Instruction Window Processors]]里一样把没有consumer且被rename的reg提前回收。
![[Pasted image 20230416195731.png]]
在有long lantency的情况下，slice会被丢到fifo SDB(slice data buffer)中；当long lantency load结束后，front end暂停，sdb会把slice放回issue queue里。此时会在后端进行rename，此时rename会是一个preg-preg的rename。
为了保持dependency，不会rename slice的rd。
### Evaluation
![[Pasted image 20230416201302.png]]
在spec int和spec server上爆锤cpr
![[Pasted image 20230416201341.png]]
在prf大小和schedule window大小情况相同时，吊打cpr。

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works
这篇的baseline
[[Checkpoint Processing and Recovery---Towards Scalable Large Instruction Window Processors]]
#### Similar Works
