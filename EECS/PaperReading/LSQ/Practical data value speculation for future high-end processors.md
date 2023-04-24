---
Author: "Arthur Perais, Andre Seznec"
Year: 2014
Journel/Conference: "HPCA"
Summary: "利用上了branch history来做prdiction"
Rate: 1
Question: "None"
Eureka: "None"
---
### Abstract


### Motivation


### Solution
![[Pasted image 20230424165417.png]]
这篇灵感来自于[[A case for (partially) TAgged GEometric history length branch prediction]]

这个predictor就是把branch过去不同长度的历史map到不同的table上
每个table都存了tag（指令pc的一段）和value。
做预测的时候选择尽可能长的history的结果来做预测。
### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works
[[Load Value Prediction via Path-based Address Prediction --- Avoiding Mispredictions due to Conflicting Stores]]
#### Previous Works
[[Value locality and load value prediction]]
[[The Predictability of Data Values]]
[[Highly accurate data value prediction using hybrid predictors]]
#### Similar Works
[[A case for (partially) TAgged GEometric history length branch prediction]]