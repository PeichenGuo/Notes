---
Author: "Andreas Moshovos"
Year: 1997
Journel/Conference: "ISCA"
Summary: "BlaBlaBla"
Rate: 4
Question: "None"
Eureka: "None"
---
### Abstract
本文提供了两种technique：
1. 预测可能的misprediciton
2. 提供避免mis-prediction的synchronization

### Motivation
随着window变大，blind prediction（load完全ooo执行）的miss可能性变高了，因此需要data  speculation了

经验主义的insight：造成data misprediction的load-store比较少且大部分都有较好的temporal locality。因此可以用合理的开销存储历史信息，并以此做出合理预测

### Solution
##### 思路
思路非常简单，就是存一个store-load pair。store执行的时候把pair的bit set了，load执行的时候看一眼该bit是否set，只有在set的情况下才能发。如图：
![[Pasted image 20230422153321.png]]

拿什么做pair的索引是tricky的。如果只用pc的话，store-load的依赖可能是动态的，每个loop都不太一样，pc-pc可能不好使。因此本文使用了tag来做pair的index。本文用的tag是dependence distance和instance num做tag，其中dependence distance是instance num的差，如下图d：![[Pasted image 20230422153706.png]]
MDPT (memory dependence prediction table)

MDST(memopry dependence synchronization table)

==具体实现没看，有很详细的实现==

### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
