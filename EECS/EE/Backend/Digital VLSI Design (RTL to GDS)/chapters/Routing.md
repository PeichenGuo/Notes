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
先做global routing。先把它划分为coarse grids。