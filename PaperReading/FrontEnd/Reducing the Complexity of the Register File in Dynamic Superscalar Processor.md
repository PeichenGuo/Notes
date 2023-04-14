---
Author: "Rajeev Balasubramonian， David H. Albonesi"
Year: 2001
Journel/Conference: "MICRO-34"
Summary: "搞了个2-level和banked prf"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
In this paper, we propose a register file organization that reduces register file size and port requirements for a given amount of ILI? We use a two-level register file organization to reduce register file size requirements, and a banked organization to reduce port requirements

### Motivation
单层prf时序太差，要想办法优化时序

### Solution
两个办法：
- 用更好的allocation policy来减少required register
- 减少port complexity

把寄存器分为了两种：有active consumer的，等precise condition的。
当所有consumer被用完后，从L1挪到L2

还把prf分成多个bank，每个bank1r1w。

rename阶段只分配给L1。
L1记录一个Usage Table。里面记着consumer个数，一个Overwrite bit记录这个寄存器是不是最新的映射（比如st进来后会重新映射到一个新的prf），一个bit记录result写没写完，两个放sequence number的记录离这个寄存器：一个是写这个架构寄存器的指令后的第一条branch，另一个是再次写这个架构寄存器指令前的第一条branch。

当L1的个数低于某个值的时候，就会把cosumer个数为0且被覆盖的寄存器写到L2.

这里L1才存数据，L2是为了让L1的内容可以尽快retire设置的备份，用来做recovery的。

Copy List用来recovery。记L1的preg id，和上面两个sequence number。这两个sequence number可以记录寄存器的存活时间。只有在某个物理寄存器被分配，到其被覆盖这段时间内的branch发生misprediction时，被copied的指令才需要recovery。注意，只有被覆盖后overwrite bit才会置1.

banked
一个关键insight：不是所有的源寄存器都是从寄存器堆里读，很多都是从bypass读
文章说平均一条指令有0.63个从prf读，0.73个从bypass读。这是cisc注意。一个8r8w的prf降到4r4w只减了2%的性能。

### Evaluation

![[Pasted image 20230413235502.png]]
注意，这里L1+L2=160。
L1=60时表现最好
![[Pasted image 20230413235557.png]]
4 bank 1r1w有点拉，但差的不是很多
![[Pasted image 20230413235631.png]]
8 bank好很多，甚至和16r4w类似了。
![[Pasted image 20230413235709.png]]
在两个方法都用后，甚至可以有更好的IPS。