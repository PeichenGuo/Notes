---
Author: "Rami Sheikh, Derek Hower"
Year: 2019
Journel/Conference: "HPCA"
Summary: "结合四种state-of-art的value predictor。没有创新点，但值得学习"
Rate: 4
Question: "None"
Eureka: "None"
---
### Abstract
四种value predictor：
1. LVP：Last Value Prediction。猜上一个
2. SAP: Stride Address Prediction。预测stride的地址，然后probe d$
3. CVP:Context Value Prediction。用branch history+load pc来预测value。见[[Practical data value speculation for future high-end processors]]
4. CAP：Context Address Prediction。用branch history+load pc来预测address。见[[Load Value Prediction via Path-based Address Prediction --- Avoiding Mispredictions due to Conflicting Stores]]



### Motivation
将load分为四种:
1. 和值高度关联。用LVP预测
2. 和地址高度关联。用SAP预测
3. 有关联但不多。用CVP和CAP预测

### Solution
##### composite predictor
![[Pasted image 20230427221706.png]]
66%以上的load被多个load预测
CAP和SAP预测了最多只能被一个predictor预测的值。

混合看上去效果不好，但实际上speedup有所提升
![[Pasted image 20230427222251.png]]

##### Accuracy Monitor
检测错误率，如果高了，就停止预测
1. M-AM：在一个epoch内，如果一个predictor预测错误率高过一个值，则把该predictor mute。
2. PC-AM：记录pc的预测错误率，如果低于某个值，则对该pc停止预测。
 ![[Pasted image 20230427223335.png]]
可以看出还是有显著提升的。

##### smart training
如果没做出预测，则对所有四个predictor进行训练；如果做出预测，则只对如下情况的predictor进行trainning：
1. 预测错误的
2. 预测正确且训练开销最低的
预测开销是：LVP > CVP > SAP > CAP
![[Pasted image 20230427223807.png]]
##### Fusion
把预测率低的资源让给率测量高的predictor
每几个epoch决定一个donner（提供预测高的predictor）然后把reciver的entry给donner。additional cache way的方式给他

### Evaluation

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works
[[Load Value Prediction via Path-based Address Prediction --- Avoiding Mispredictions due to Conflicting Stores]]
[[Exploring value prediction with the EVES predictor]]
[[Practical data value speculation for future high-end processors]]
#### Similar Works
