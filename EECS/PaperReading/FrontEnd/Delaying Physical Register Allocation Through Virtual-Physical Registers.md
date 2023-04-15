---
Author: "Teresa Monreal, Antonio González"
Year: 1999
Journel/Conference: "MICRO"
Summary: "提出了一种利用virtual physical register来在wb阶段做allocation的办法，用stealing的方式来解决死锁"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
[[Virtual-Physical Registers]]的续作。这个work用了stealing的方式解决deadlock，叫on-Demand with Stealing from Younger (DSY)。相当于老的instr进来后发现没preg了，从最新的获得了preg的指令那里哪一个过来，重新执行新指令。这样相比之前的做法，可以减少新指令的重新执行的概率。
### Motivation
同[[Virtual-Physical Registers]]
### Solution
##### on-Demand with Stealing from Younger (DSY)
如果执行结束的时候没有preg，看看有没有更年轻的指令有preg，有的话抢最年轻的preg，然后让他重新执行；否则自己重新执行。

作者还说，重新执行年轻指令不一定是坏事，因为：
1. 如果该指令是branch，相当于提前解决miss prediction问题
2. 如果该指令是ls，则可以做prefetch
他之所以敢这么说，是因为如果没做VPreg的话，传统方式都不会发出这个年轻指令，因为preg都用完了，会stall住等回收。

### Evaluation
![[Pasted image 20230414165413.png]]
![[Pasted image 20230414165459.png]]
vp-ori指的是之前的方式，也就是NRR的方式。可以看出还是有稳定进步的。

### Unsolved Question

### Related Works
#### Previous Works
[[Virtual-Physical Registers]]
#### Later Works
[[Dynamic Register Renaming Through Virtual-Physical Registers]]
