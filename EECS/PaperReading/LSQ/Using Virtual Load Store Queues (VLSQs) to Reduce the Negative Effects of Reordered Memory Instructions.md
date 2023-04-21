---
Author: "Aamer Jaleel"
Year: 2005
Journel/Conference: "HPCA"
Summary: "在LSQ上加窗，从而减少乱序发射的ls数，从而嫌少trap和l1d miss"
Rate: 4
Question: "None"
Eureka: "None"
---
### Abstract
为了解决ooo窗口变大，ls乱序发射变多，从而带来的性能损失。因此通用LSQ上的virtual load store queue来减少select logic的issue宽度，从而减少ls乱序发射。

### Motivation
在issue window越来越大，MLP越来越多的情况下，unorder的load store也会发射的更多，这回带来两个后果：
1. replay trap增多
2. cache miss增多。这是因为unorder的load store会破坏程序locality

![[Pasted image 20230420134543.png]]
这个带来的性能影响还挺大。
如上图，可以看出更大的ROB对于handling trap的时间和L1D miss的概率都是巨大的。

值得注意的是，后者带来的影响或许没有那么大，因为L2D miss概率影响应该不大，只有L1 miss的话其开销是较为有限的，大概10个cycle。

### Solution
异常简单，就是在LSQ上加两个指针。virtual head pointer指向最老的未发射的ls指令，virtual tail pointer指向vlsq末尾。两个指针的差就是窗口大小。只有在窗口内的才能发射，窗口外的不能发射。

好处有两个：
1. 减少reordering
2. 减少预测执行的memory指令

坏处有一个：ILP减少。毕竟并行发射的窗口都减小了。
### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
