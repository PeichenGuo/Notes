---
Chapter: 7
---
# Routing  Algorithms
## Maze Router
最开始把layout抽象成grid，此时一个wire就是多个*连续*的格子。用过的格子不能再用。这就是最简单的抽象。
在这个假设上可以提出Maze Router，或者 lee router
Maze Router一次route一条线，每次都进行局部最优布线。这会导致一个问题，旧的布线阻碍后续布线。Maze Router主要的贡献是解决了这个问题。
### 算法实现
Maze Router分为三步：Expand，backtrace，clean up
 
Expand：
从source开始做一个dfs，标注距离，直到覆盖target。这会形成一个wave，到达target的是一个wavefront
![[Pasted image 20240511173225.png]]

BackTrace：
从target反向向后走到source。每次都找标记距离-1的。
![[Pasted image 20240511173420.png]]
Cleanup：
backtrace到source后将trace标记为obstacle

然后再对下一对ST进行这个算法。

对于一个source多个target的net也适用：每当expand到一个target，就进行一次backtrace和cleanup，然后把cleanup后的格子（也就是新的wire)都设置成source，再进行expand，backtrace和cleanup。

### Multi-Layer Routing
做expand的时候加一个维度，up/down。需要通过via向上或向下expand，via可以是任意的，也可以是规定好的地方
![[Pasted image 20240512135747.png]]

而实际上不同的grid可能是不一样的，比如via的电阻更大；而且实际实现上希望做到每个layer只有一个diretion的wire
因此可以做non-uniform grid cost。相当于每个grid的开销不一样。

### Software Implementation
grid是巨大的，可能有10^10那么多
所以需要更低代价的抽象
![[Pasted image 20240512140143.png]]
先做global routing。
先把chip划分为coarse grids。global placement走coarse grid。此时可以解决congestion，可以看到每个grid都有多少个什么走向的wire。
在global routing之后再在coarse grid之中做detailed routing
![[Pasted image 20240515150930.png]]

# Routing In Practice
metal layer is growing。工艺越好层数越多。
![[Pasted image 20240515151152.png]]
## Global Routing
Tracks：Metal layer之间最小的距离
先把floorplan分成GCell。每个GCell里面都会有几个track。track是DR，GCell是人为规定的coarse grid。
在划分GCell后进行global routing。
之后可以得到congestion map

## Detailed Routing
Track Assignment：给GCell里的net分配Track

DRC fixing：修复各种各样的DRV，图中右侧几种情况。
![[Pasted image 20240515152304.png]]

## Timing-Driven Routing
目标是optimize critical paths
route critical path first，还可以增加关键路径的width来降低电阻

# Signal Integrity(SI) and Design for Manufacturing (DFM)
## Signal Integrity (SI)
routing阶段的SI主要是Crosstalk（串扰）
Crosstalk（串扰）是在电子电路中常见的一种现象，指的是信号在传输过程中相互干扰的情况。当两条电路或信号线路靠近并且共享相同的地平面时，它们之间可能会发生电磁耦合，导致其中一条线路上的信号影响另一条线路上的信号，从而产生不良的影响。
switching的叫aggressor，affected的net脚victim
两个主要影响：
- signal slow done：aggressor和victim switch方向不同，victim的信号可能会更慢到达。影响setup
- signal speed up：方向相同的时候，可能会更快到达。影响hold

Infinite Window Analysis：一种分析SI的建模方式。考虑wire间的cap。在90之前的工艺好用。
Propagated Noise Analysis：现在的用法。构建min/max vec，分别是transition最早开始的时间和最晚开始的时间，这会形成一个transition window。不同aggressor的windows的overlap会发生worst case Crosstalk。
![[Pasted image 20240515160045.png]]

prvention：
- 减少并行
- 增加wire之间的距离
- 在wire外面放shield
- 提高driver drive能力

## Design for Manufacturing (DFM)
design for yield
确保生产时错误少的设计

 Via Optimization
via电阻较大，而且可能有内部defect，因此需要
- minimization。能不用就不用
- 用multi-cut vias替代。用多个联通的vias替代一个单独的via。

可以增大可靠性，减少EM

 Wire Spreading
增加wire之间的距离：
- 减少cap
- 减少random partical来带来的短路

