---
Book: A Primer On Memory Consistency and Cache Coherence
Chapter: 5
Summary: "-"
---
在多核中，只有部分操作是会引起歧义的，比如对同一个地址的操作。
上锁可以解决这样的歧义。
换个角度，锁之间的load和store是不会引起歧义的。
因此这些load和store就具有重新排序的潜力.
针对reordering可以做出下面的优化：
1. non-fifo的合并stb。其实是store可以bypass store
2. speculation支持更加简单。
3. 可以更好地和coherence进行耦合，从而提升效率

### An example of relaxed memory consistency
![[Pasted image 20230714122611.png]]
简而言之，其设计是：
1. 后面的load可以bypass前面的store
2. store和store，load和load，load后的store，还有RMW，如果不是同一个地址，可以随意排序。如果是同一地址，则需要保序。
3. 任何指令都需要和fence保序
### Sequential Consistency for Data-Race-Free (SC for DRF)
==用fence包裹syn操作==
![[Pasted image 20230821145111.png]]
### Some relaxed model concepts
#### Release Consistrncy
Fence 全包裹是浪费的：==引入aq rl==
aquire只需要后续的fence，rl只需要前向的fence
因此RC只要求如下三种顺序：
- aq-> ls
- ls -> rl
- aq和rl之间的顺序
#### Causality and Write Atomicity
Causality：因果性
![[Pasted image 20230821150510.png]]
图中C2在看到data1写成new之后 才会把data2写成new。c3在看到data2写成new之后，才会把把r3写成data1。如果此时data1是new，即”==我看到然后再告诉你，你也能看到==“，符合causality；否则打破causality

Write Atomicity：
一个核的写，被其他核同时看到。在某一时间点前，任何一个其他核读到的数据都是旧数据；在某一时间点后，任何一个其他核读到的数据都是新数据。不存在某个时间点，有些核读到新数据；有些核读到旧数据
write atomicity的一个condition是IRIW(Independent Read Independent Write)，如下图
![[Pasted image 20230821151845.png]]
C3看到了data1被写了，C4看到了data2被写了。
此时如果C3看到data2没被写，C4看到data1没被写，就违反了atomicity。因为对于C3而言，s2后于s1；对于c4而言，s1后于s2。如果存在atomicity，s1和s2的顺序应该全局一样。

write atomicity => causality
causality =/> write atomicity 

### RISC-V WEAK MEMORY ORDER (RVWMO)