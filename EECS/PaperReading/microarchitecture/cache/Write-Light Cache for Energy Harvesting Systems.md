---
Author: Jongouk Choi，Changhee Jung
Year: 2023
Journel/Conference: RIOSJournel
Summary: 通过动态调整cacheline dirty写回阈值来平衡容灾性和性能
Rate: 3
Question: None
Eureka: None
---
## Authors

## Abstract

## Background
解决的主要是供电不稳定的低功耗情况下如何用更节能的方式来防止crash，也就是及时把数据存到non violated memory NVM里
## Motivation


## Solution
有一个dirty queue来存放所有dirty的cacheline，dirtyline不直接写回memory而是先放入dirty queue，条件满足时再写回memory然后把原先dirtyline置clean。
dirty queue有两个threshold，第一个是maxline，queue的最大容量，超过这个line直接stall住store；另一个是waterline，超过这个值后开始往memory写cacheline。这两个值的gap就是ILP
maxline如果大小和cache size一样，就是一个write back cache；maxline如果大小是0，那就是一个write through cache。
![[Pasted image 20240723124338.png]]
最重要的设计如上，可以根据电量(供电电压)来灵活调整maxline和waterline来在performance和energy effciency上保持平衡
## Evaluation


## Unsolved Question


## Related Works
### Later Works

### Previous Works

### Similar Works
