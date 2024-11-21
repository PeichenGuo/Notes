---
Author: "[[Mathias Payer]]"
Year: 2023
Journel/Conference: ISCA
Summary: 应该使用软硬结合的方式处理imprecise store exception，ie，接受硬件的imprecise store exception
Rate: 3
Question: None
Eureka: None
---
### Abstract


### Motivation
因为store可能会被滞后执行（比如store buffer），store产生的exception也会imprecise
但是，显然不能不用store buffer。

本文认为有两种方式来解决，一是speculate，二是软件解决

Post-retirement speculation + SC 可以达到差不多的性能，比如
### Solution


### Evaluation


### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
