---
Author: "José-Lorenzo Cruz"
Year: 2000
Journel/Conference: "ISCA"
Summary: "提出了一种异构多bank架构，重点介绍多层prf"
Rate: 2
Question: "None"
Eureka: "None"
---
### Abstract
In particular we focus on amulti-level organization of the register file, which provides low latency and simple bypass logic. We propose several caching policies and prefetching strategies and demonstrate the potential of this multiple-banked organization.

it increases performance by 87% and 92% when the register file access time is factored in.

### Motivation
 amulti-stage register file has severe implications for processor performance (e.g. higher branch misprediction penalty) and complexity (more levels of bypass logic).
### Solution
重要insight：很多reg都被浪费了，如果这些浪费被利用，那么很少数量的reg就可以维持高性能。浪费的原因如下：
1. 早allocate
2. 提早ready，用到它数据的intr还没进iq
3. 晚release

本文采用了异构的bank，但主要还是在探讨multi level。异构与否并不重要。
重要insight：
1. 大多数寄存器只被读一次
2. 有很大一部分数据来自于bypass而非prf

两种cache policy:
non-bypass.L2存bypass的数据，L1存非bypass的。因为大部分数据只被读一次，bypass被读的数据不是很likely被再次读到，放到L2。
on-demand: 只cache那些还没有issue但ready的指令的rs。

两种fetch policy：什么情况下l2会被fetch到l1
on-demand：当指令所有rs ready时，把在l2的rs fetch到l1
prefetch-first-pair：当某指令issue的时候，把rd做rs时的另一个rs做prefetch。
![[Pasted image 20230414130459.png]]


### Evaluation
![[Pasted image 20230414130522.png]]
non-bypass好于on-demand，但prefetch只在几种情况下更优。
![[Pasted image 20230414130725.png]]
性能还是显著弱于1-cycle（10%-2%）
![[Pasted image 20230414130808.png]]
比传统2-cycle，full bypass要弱，弱8% 2%。但好处是bypass只有一层，组合逻辑少很多![[Pasted image 20230414130929.png]]
可以看出来同面积下性能好于2-cycle full bypass
![[Pasted image 20230414131011.png]]
这个把access time考虑了进来，看到这个做法确实好于1-cycle和2-cycle 1-bypass。但实在好的略少。孬。