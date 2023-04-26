---
Author: "Rami Sheikh"
Year: 2017
Journel/Conference: "MICRO"
Summary: "用预测load的地址来代替预测load的值，从而减少load-store conflict。"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
load的value prediction面对两个问题：
1. store会改变memory，让load predictor记住的值变stale
2. value mispredict会引发costly的pipeline flush
因此本文提出Decoupled Load Value Prediction (DLVP)。DLVP通过将value prediction替换为load address prediction来减轻stale。他通过一些投机取巧的方式通过address从dcache里获得对应的值，从而进行value prediction
性能提升约4.8%


### Motivation


### Solution
![[Pasted image 20230426154825.png]]
##### PAP path-based address prediction
![[Pasted image 20230426154905.png]]
用FGA（fetch group address）索引PAP，然后对比tag（pc的一部分）来确定是否hit。hit后查看confidence（是一个FPC，见[[Probabilistic Counter Updates for Predictor Hysteresis and Stratification]]），如果过了一个阈值，则做出prediction；否则不做prediction
值得一提的是，本文对于一个FG只做两个预测，因为98%的FG只有2个或以下的load；剩下的2%的FG只做前两个load的prediction。
replacement只在新entry allocate时，对应的confidence是0的情况下做。

##### Predicted Values Table (PVT).
为了避免增加preg的写口，再加上本文发现preg中只有很少一部分是通过本文机制预测执行的，因此本文添加了一个PVT存放预测的值。
PVT存一个preg的tag，和对应的值。
当PVT满的时候，不进行预测。
rename map table(RMT)有一个bit代表是否是predict的值，如果是1，则去PVT找值；否则去PRF找值

##### Load-Store Conflict Detector (LSCD)
DLVP解决了2/3的load-store conflict，但in-flight store没法解决。因此设计了Load-Store Conflict Detector (LSCD)。
LSCD中存储地址预测成功但值错了的load的pc，下一次这个load来后不进行预测。

### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works
[[Practical data value speculation for future high-end processors]]
[[Correlated Load-Address Predictors]]
[[Probabilistic Counter Updates for Predictor Hysteresis and Stratification]]
#### Similar Works
