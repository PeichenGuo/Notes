---
Author: "Adrian Cristal"
Year: 2004
Journel/Conference: "HPCA"
Summary: "通过checkpoint的方式实现乱序commit和把长时间内应该不ready的指令放到buffer中的办法，来提升ILP"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
通过checkpoint的方式乱序commit和把长时间内应该不ready的指令放到buffer中的办法，来提升性能。相比rob和iq大小大了一个数量级的传统处理器，只有10%的性能减少；相比现在的处理器有200%的提升。

### Contribution
1. 基于checkpoint的ooo commit方式
2. 把长时间不ready的东西放在buffer中来提升ILP。类似[[Long Term Parking (LTP) --- Criticality-aware Resource Allocation in OOO Processors]]。但LTP是以此来提升MLP，我不知道为什么这篇认为这种parking可以提升ILP。

### Motivation
rob和iq这种东西需要变大才能获得更好的性能，但是这并不scaling，需要其他办法、
![[Pasted image 20230417112931.png]]
issue queue里有太多不ready的指令堵着

### Solution
##### OOO Commit
不再用ROB，如果在checkpoint之间出现exception或者misprediction，回退到checkpoint再重新执行。
给每个preg添加了future free bit。如果这个bit是1，那么在释放checkpoint的时候释放这个preg。

checkpoint只需要记录prf的valid bit和future free list，这两个list就可以记录所有的寄存器状态，因为preg不会在checkpoint release之前reallocate，也就不会丢失数据。

store指令会在checkpointrelease时把这个checkpoint和下个checkpoint之间的store发出去。

在下面三种情况下打checkpoint：
1. 64条指令后的第一个branch。misprediction
2. 每64条store。store因为会等checkpoint release才送出去，因此很占据lsq，因此每64条store就要打个checkpoint来确保store按时发出去
3. 每512条指令

##### Slow Lane Instruction Queuing (SLIQ)
类似[[Long Term Parking (LTP) --- Criticality-aware Resource Allocation in OOO Processors]]的UIT
本文判断critical的方法和LTP很不一样。
![[Pasted image 20230417113114.png]]
有个pseudo ROB，图中深色那个，它是一个fifo，在iq中滑动，当出队的指令是一个critical dependent指令时，就把这个指令放到SLIQ中；当critical load执行之后，再把这些指令放回iq中。

判断critical的方式是，有一个32bit的寄存器，每个bit代表一个架构寄存器。每个critical load（L2 miss load）都会把对应位置1。每当一个指令出队的时候，都和这个寄存器比一下，如果其rs位是1，说明这个出队指令是一个critical dependent指令，把它放到sliq中，并且把它的rd置1。
这种做法实际上是把critical判断放在了decod之外。


### Evaluation
![[Pasted image 20230417113559.png]]
上面的线是ROBsize4096，这不太可能；下面是rob size128，这比较小。OOO checkpoint entry都是8。COoO 后面的数字代表的是LSIQ entry num。
![[Pasted image 20230417113815.png]]
当lsiq向iq re-insert的周期改变时，ipc变化没有很大。
==剩下的evaluation没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
