---
Author: "Simha Sethumadhavan"
Year: 2003
Journel/Conference: "MICRO"
Summary: "通过hashing的方式来减少search能耗"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
本文通过实现轻量级的hashing算法，称之为bloom filter。
本文提出了bloom filter的两种用法：
1. search filter：减少search num
2. state filter：
避免98%的associative search，大大减少功耗

### Motivation
HILP指的是high ilp，lilp指的是low ilp。
我们可以看到只有很少一部分的load store实际上在search过程中是有hit，其他都是无用的search。在LILP中，这个比例不到0.1；在HILP中这个比例不到0.3
![[Pasted image 20230428165615.png]]
这部分可以cut掉

### Solution
##### BFP：Bloom Filter Predictor
![[Pasted image 20230428170959.png]]
解决方案很简单，就是addr过一下hash，然后看一眼对应的hash bit。如果hash bit为1，则search；否则不search。为了解决collision，可以用下面的三种方法：
1. counter。每次hash到entry，counter++；entry deallocate时counter --
2. flush clear。只有在pipeline flush的时候清掉hash bit。
3. hybride solution。

hash function要有两个特点：时序好 且 collision概率低
==后面这里有讲更多的BFP的实现问题，没看==


##### Load State Filtering


### Evaluation


### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
