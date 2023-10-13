---
Author: "George Z."
Year: 1998
Journel/Conference: "SIGARCH"
Summary: "通过store set(每个load的所有历史dependency)来预测load-store依赖"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
本文使用store set来进行load store依赖的预测。store set简单来说就是每个load存一下拥有过的dependence，然后每次load执行的时候查一下store set来预测是否有依赖。

### Motivation
![[Pasted image 20230421132237.png]]
这张图很有意思。
Naive speculation指的是ooo执行然后解决trap。这回导致较多的violation然后带来penalty。但其实还好。
不做specutlation就是所有load等store执行结束后再执行，可以看出有巨多false dependency，这会造成很大的性能减弱。

### Solution
##### store set
两个assertion：
1. 历史store记录可以用来预测
2. 1ld-多st或者多ld-1st的预测是有价值的

每当一个load 出现store violation时，把那个store的pc加到这个load的store set里。每当和load执行的时候，查一遍store set里的pc是不是fetch了但没issue。

1个load可能对应多个store，处理这个情况很重要，==但我没看==，因为我觉得很obvious

![[Pasted image 20230421140415.png]]
实现上有一个SSIT和一个LFST。SSID存一个store set的id，LFST存一个inum，指向一个最近的store。
当一个load fetch的时候，用index索引获得ssid；如果valid，用ssid访问LFST，得到一个inum，这个inum的store就是它的dependency。
当一个store fetch的时候，用index索引获得ssid；如果valid，用ssid访问LFST，得到一个inum，这个inum的store就是它的dependency，并且这个store会把自己的inum更新到LFST里。

### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works
[[Memory Dependence Prediction using Store Sets]]
#### Similar Works
