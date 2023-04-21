---
Author: "I. Park"
Year: 2003
Journel/Conference: "MICRO"
Summary: "用store-load pair predictor（预测需不需要search）和load buffer（存放oooload）的方式减少search bandwidth，哟个segment（切成段）的方式增加scaling ability"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
LSQ 不scaling，本文采用了两个办法来减少search bandwith：
1. store-load pair predictor 
2. load buffer
用了一个办法来增强scalling ability：segmentation

综合三个办法的1port lsq，相比2port的传统lsq，可以有average 6%(int) 和 15%(fp)的性能提升

### Motivation
两个问题：
1. search bandwidth 过大，开销大
2. cam结构不好scaling，不是线性的。

两个insight：
1. store-load的order violation可以预测且不太常见，而且大部分的store 和load都不访问同一个地址。根据这个insight提出了store-load pair prediction
2. 为了找到load-load order violation，新ld不需要search整个lsq，只需要搜ooo执行的那些load就行，而这些ld通常不多。根据这个insight提出了load buffer。


### Solution
##### 减少search bandwidth
三种search
![[Pasted image 20230420155816.png]]

##### 减少store queue search：store-load pair prediction
load只会在predictor认为需要search的时候才会去search store queue。这样大大减少了search 次数。
当mispredict的时候，因为load此时没法再search，改由store search load queue来做recovery。有两个case：
1. store先于load
2. store后于load
对于case2可以在store执行的时候搜索到，而对于case1则需要更复杂的方法。
每当store commit的是的时候，store会搜索load queue来寻找所有获得错误值的load。
==没理解这里怎么做recovery。因为没看实现。姑且记下有这么回事==

##### Load buffer：减少load queue search
把所有ooo发射的load放在一个buffer里，因为只有这些load会造成load load dependency。
![[Pasted image 20230420163851.png]]
==没看实现==

##### segment：scaling技巧
切成段
![[Pasted image 20230421102612.png]]
这样的问题是可能查dependency的时候时间不定，导致lantency不确定需要预测
因此本文放弃了预测segment#1外的load dependency

同时，这种segment方式可以让port更少的下有更多的搜索并行


### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works
[[Memory Dependence Prediction using Store Sets]]
#### Similar Works
