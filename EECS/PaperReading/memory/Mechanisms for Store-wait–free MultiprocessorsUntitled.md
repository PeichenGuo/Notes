---
Author: "[[Andreas Moshovos]]"
Year: 2007
Journel/Conference: ISCA
Summary: 提出了scalable的store buffer，和checkpointed的粗粒度retire group
Rate: 3
Question: None
Eureka: None
---
### Authors
[[Thomas F. Wenisch]] 
[[Anastasia Ailamaki]]
[[Babak Falsafi]]  
[[Andreas Moshovos]]
### Abstract


### Motivation
为了解决store buffer不scalable的问题（associative search不scalable），提出了Scalable Store Buffer (SSB)。采用fifo，避免search

为了解决ordering带来的stall，采用了atomic sequence ordering ASO。维护粗粒度的atomic顺序，发生错误的时候rollback，类似[[Checkpoint Processing and Recovery---Towards Scalable Large Instruction Window Processors]]，[[Increasing processor performance through early register release]]或者[[Cherry---Checkpointed Early Resource Recycling in Out-of-order Microprocessors]]。

#### store stall
两种可能: 
capacity stalls：容量满了
ordering stalls：有前面有没有retire的load、fence、RMW等指令

### Solution
#### SSB
用L1来做forwarding，SSB只是一个store fifo。

#### ASO
把access分成atomic组，保证组间顺序，组内顺序放松。
ASO分为三个阶段，如下图：
![[Pasted image 20231015131435.png]]
1. accumulate。每个组都有checkpoint，组内随意retire。
2. await permission。当组满了之后，开新的组，旧组进入await permission态等待write permission
3. commit。 所有的write permission到达后，进入commit态。当组内所有write都globally visible后，这个组可以retire。

==实现没看==
### Evaluation


### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
