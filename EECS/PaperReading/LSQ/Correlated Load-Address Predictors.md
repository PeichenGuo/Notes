---
Author: "Michael Bekerman"
Year: 1999
Journel/Conference: "SIGARCH"
Summary: "提出了context-based load address predition和若干小手段"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
提出了一些新的预测手段：
1. Context-based prediction
2. 利用global correlation做prediction算法
3. confidence enhancement
4. 防止错误load或过长load chain污染预测池
67%预测正确，1%预测错误

### Motivation
两种可以利用的load behavior
1. Recursive Data Structures (RDS)，类似链表这种。
2. Loads operating on pointers passed via registers or on any type of values passed through the stack may reflect a correlated behavior. 比如传参
这两种load的地址比较固定而且好预测

### Solution
![[Pasted image 20230427152709.png]]
load buffer存储过去的static load，然后存储其历史访问的地址。
Link Table存储一个history对应的link，也就是地址。

history需要多深？本文RDS2个history深，但对于更深的pointer不好使。

index的方式：shift left m bit 然后xor

##### global corelation
本文LB和LT存的是base address + offset，这样的好处是：
1. 减少LT中的link数，因为base addr更少
2. misprediction 更少。
3. 增加prediction rate。
坏处是：
1. 需要记录更多的history，因为同一个RDS的pointer用同一个entry了
2. access time上升，因为ALU被加入进来
3. 更多alias

##### enhance confidence
三种方法，前两种是per-load，后一种是per-link：
1. 饱和计数器
2. Control-Flow Indications.每次预测失败的时候，把GHR(global history register， bpu用的那个）的末n位放在LB entry中，下次再hit的时候对比GHR和LT中记录的失败trace，如果相同就不再做预测
3. LT Tag。加长history长度，多余的部分作为tag放在LT entry中，index的时候同时对比tag

##### reducing pollution
添加了pollution-free bit。每次访问会update pf bit，只有当pf bit满了之后才会替换LT entry。相当于只有短时间内连续的load访问才会替换LT entry，从而避免污染。

### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
