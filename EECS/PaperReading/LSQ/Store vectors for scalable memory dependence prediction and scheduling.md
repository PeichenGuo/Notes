---
Author: "Samantika Subramaniam"
Year: 2006
Journel/Conference: "HPCA"
Summary: "BlaBlaBla"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
之前的prediction technique大多依赖于CAM，过于复杂。
因此本文使用了dependency vector这种方式来减少复杂度从而增加scaling ability。
本文相比blind algorithm有8.4%的提升（理想提升8.5%），比store set([[Memory Dependence Prediction using Store Sets]])更好而且实现更简单。

### Motivation
之前的prediction technique大多依赖于CAM，比如[[Memory Dependence Prediction using Store Sets]]，过于复杂，不scaling。

本文使用的方式是[[Select-free instruction scheduling logic]]，通过这个方式不适用CAM。

### Solution
相比store set存pc的方式，本文存一个相对位置
![[Pasted image 20230421152802.png]]
图中（a)存的是pc，而本文使用的方法是(b)，存的是前多少个store有dependency。

有一个SVT存每个load对应的相对dependency的vectore（store vector），然后有一个LSM代表LSQ中每个load对应的stq种的依赖关系。
![[Pasted image 20230421152706.png]]
如上图。当一个load来的时候，先在SVT中找气对应的vector。
由于这个vector是相对的位置，因此需要根绝stq shift一下。
而且STQ中所有被resolved的store要clear掉，所以需要和stq的rdy bit 做一下and ==这段没懂，为啥要和不ready的做and==
最后填到LSM中

只有当LSM行bit全是0时，这个load才准备好发射。

开始的时候SVT全是0，也就是所有load进行blind/naive speculation，如果出现violation，则把对应的SVec相对位置置1。
### Evaluation
同开销下性能比store set好。
![[Pasted image 20230421153724.png]]

==具体没看==

##### 为什么store vector更好
![[Pasted image 20230421163206.png]]
store set回让store串行
而store vec中store都对load没有knowledge，不构成串行延误，因此比store set好了点。
### Unsolved Question


### Related Works
#### Later Works

#### Previous Works
[[Memory Dependence Prediction using Store Sets]]
[[Select-free instruction scheduling logic]]
[[A High-Speed Dynamic Instruction Scheduling Schemefor Superscalar Processors]]
#### Similar Works
