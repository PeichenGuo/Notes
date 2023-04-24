---
Author: "Kai Wang, Manoj Franklin"
Year: 1997 
Journel/Conference: "MICRO"
Summary: "提出了一种2-level predictor（4 history trace+pht），并提出了一个2-level+stride 的hybrid predictor"
Rate: 2
Question: "None"
Eureka: "None"
---
### Abstract
本文介绍了两种data value prediction方法：
1.  stride predictor
2. pattern based的2-level predictor
接着本文把这两种方式混合在一起做了一个hybrid predictor

### Motivation
pass

### Solution
##### stride-base value predictor
非常简单，就是一个算等差的predictor，不多讲了
![[Pasted image 20230424144828.png]]

##### 2-level value prediction
记录history trace的。
重要insight：很大一部分指令在较近的历史中只有四个或更少的history值。
![[Pasted image 20230424150222.png]]
VHT里只存四个值和pattern，用pattern索引PHT的值。

##### Hybrid predictor
![[Pasted image 20230424151546.png]]
VHT中加入了stride需要的东西和state状态。当pht中存的值超过一个阈值时，用2-level的prediction值；否则用stride值。这样节省了2-level的开销

### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
