---
Author: "Mikko H. Lipasti, John Paul Shen"
Year: 1996
Journel/Conference: "ASPLOS"
Summary: "BlaBlaBla"
Rate: 4
Question: "None"
Eureka: "None"
---
### Abstract
本文探索了value locality，在时间和空间局部性后的第三个面向（当时情况下），并做load value prediction。本文基于对历史load过的值，对load value进行预测，并发现这样的预测是较为准确的。

### Motivation
经验上来看，value绝对有locality，因为常用的软件往往是genral by design。也就是说，程序不仅要handle normal case，也会handle各种corner case；还会给未来拓展留出可能。这就造成了以下的一些特征：
- data redundancy：比如稀疏矩阵、充满空白字符的text等
- error checking：会大量重读之前的内容
- program constant
- computed branch：branch指令有些需要基地址，基地址常常一样
- virtual function called：函数指针
- glue code：比如两段代码间经常跑同一段代码做context switch
- addressability：数据指针，比如数组起始地址
- call-subgraph identities：producer和consumer在互相调用时会经常call同样的函数
- memory alias resolution：没懂
- register spill code：compiler没reg后会把数据写到mem来空出reg，一些不常用的const会反复load store。

![[Pasted image 20230422172952.png]]
一个load和前面1个（浅色）或前面6个（深色）中的同地址load，load上来的值一样，称其为有locality。
可以看出50%的load和紧接着的上一个同地址load，load上来的值一样；80%的load获得的值，和前6个同地址load中某一个load获得的值一样。这样的局部性可谓很好。
![[Pasted image 20230422173318.png]]
这张图可以看出address load的值的locality显著更好，int也显著好于float。

### Solution
##### LVP(Load Value Prediction Unit)
其中有三个组件

LVPT(Load  Value Prediction Table)
存放load之前load过的值，是一个load pc - history value 直接映射的table。

LCT(Load Classification Table)
本文把load分成三种：
1. unpredictable：不能被预测的
2. predict：可以被预测的
3. constant：大多数情况可以被预测的，也就是大多数情况load的数值不变的
LCT包含了一个n-bit situration。2-bit是 unpredictable-unpredictable-predict-constant，1bit的事,unpredictable-constant。

CVU(Constant Verification Unit)
LVP对unpredictable不进行预测；对predict进行预测，并进行正常的访存，在访存内容回复后对比其正确性以更改LCTentry

本文对constant的处理很有趣。constant的值和valid bit回存在CVU中，如果该load是个constant load，CVU会直接进行数据查询并返回，不进行正常的访存操作。
每当一个store进行时，会看这个store是否更改的事CVU内的值，如果是，则把对应entry disable掉。
当一个constant load在cvu中查到一个diabled entry时，会把自己的状态降级为predict，并正常进行访存。
这样一来节省了L1D的bandwidth
![[Pasted image 20230422180821.png]]
本文实验中集中LVP配置，其中LVPT的history entry代表存多少load的值。==我不知道为啥LCT的entry数为啥和LVPT的不一样==
![[Pasted image 20230422180918.png]]
上图是LCT对load分类的正确率，可以看出正确率其实挺高的，但相比bpu还是有点小。
![[Pasted image 20230422181019.png]]
上图是CVU中constant load数量的占比，可以看到数量其实不少，这些占比可以看做是L1D带宽的减少，因为这些值都不进行访存了。
![[Pasted image 20230422181119.png]]
上图是三个模块在流水线里的位置。
==详细实现没看，mispredict代价和恢复没看==



### Evaluation
==没看懂==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
