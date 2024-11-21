---
Author: Miquel Moret ́
Year: 2023
Journel/Conference: ISCA
Summary: 用predictor猜测AMO指令near和far
Rate: 4
Question: None
Eureka: None
---
### Abstract


### Motivation
AMO操作有near和far：
near把东西fetch到本地cache来做exclusive操作
far把东西只加载directory、LLC等地方做了。比如AMBA5 CHI

远近开销是有差异的。如下图，随着核数上升，near和atomicload-far的throughput会逐渐下降；与之相反，atomicstore-far
![[Pasted image 20231013151549.png]]

本文研究什么时候适合far，什么时候适合near。然后使用一个predictor来选择合适的策略


![[Pasted image 20231013153540.png]]
这个是CHI的near和far操作。RN0是req发起者，RN1拥有数据，HN是负责仲裁的。

![[Pasted image 20231013153837.png]]
上图展示了什么情况下near好，什么时候far好。
如同情况（a)时，如果两个thread交替使用，near会有ping-pang效益，其data会不断地在两个thread间交换。near不如far
如情况b时，如果两个thread交换频率低，此时显然near好于far
### Solution
![[Pasted image 20231013154718.png]]
现存的两种static policy。
Unique Near的意思是，如果cache block没处于U态，就避免进入U态，使用Far。如果cache block已经在U态了，可能没法far，就使用near。

三种提出的：


两种predictor
#### Metric-Based DynAMO
记录两个数：near的访问次数和directory invalide这个cache block的次数
这个比例越大，说明near的次数越多，访问的更密集。

#### Reused-Pattern
用一个reuse bit和一个reuse counter来算
每次fetch到near后，reuse bit清空
当这个被再次访问的时候，bit被设置

当下一次evict或者invalidation来的时候，如果bit被set，counter++；否则--

### Evaluation

有预测后确实好了

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
