---
Author: H.C. Yen
Year: 1988
Journel/Conference: 25th ACM/IEEE, Design Automation Conference
Summary: BlaBlaBla
Rate: 3
Question: None
Eureka: None
---
## Authors

## Abstract

## Background
在这篇之前主要有四种做法：
1. plain depth-first search
2. depth-first with pruning
3. heirarchical depth-first without pruning
4. breadth-first with pruning


## Motivation


## Solution
提出一个input dag，其中边是delay，还有一个threshold T ，超过t的路径才会被枚举

第一步按宽搜标深度，source是0，往后递增。确保任何一条边都是i到i+k，k>0
这样做的好处是，对于任何一个vertex vl,i的最大delay$max\_delay(v_{l,i}) = max\{v_{l+1, j} + E_{v_{l,i},v_{l+1, j}}\}$
然后可以从后往前算

## Evaluation


## Unsolved Question


## Related Works
### Later Works

### Previous Works

### Similar Works
