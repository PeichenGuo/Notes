---
Book: A Primer On Memory Consistency and Cache Coherence
Chapter: 4
Summary: "-"
---
SPARC引入，x86使用，RISCV支持TSO扩展
### Motivation
store buffer： store commit的时候进sb，写进下层cache后退出sb
sb可以遮盖寻求下层cache的coherence state的延迟
TSO的关键在于利用了这个**FIFO Store Buffer**

### Basic of TSO
==TSO中load可以bypass store==
![[Pasted image 20230713131839.png]]
图中这种执行方式在tso中是正确的
为了保证L1可以加载到正确的值，程序员需要手动**Fence**来确保store和load的顺序。图中需要再S1和L1、S2和L2之间放fence，确保L1和L2不会超过S1和S2
#### formalization
三个主要change：
1. 允许后面的loadbypass前面的store
2. load的值是最新的store
3. 添加FENCE来提供回到sc的方式
![[Pasted image 20230713132347.png]]
![[Pasted image 20230713132434.png]]
![[Pasted image 20230713132448.png]]
### implementation
![[Pasted image 20230713132821.png]]
##### 简单的实现
1. load和store按程序顺序从core发射给cache（不考虑ooo）
2. store进入store buffer。当buffer满的时候stall core
3. 每次发给cache的要不是load，要不是store
注意：**stb对coherence是不可见的**
##### Atomic RMW (Read-Modify-Write)
注意：rmw中read可以bypass前面的store，但其紧随其后的atomic store不能bypass。因此**TSO RMW整体不能bypass store**
