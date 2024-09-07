# Intro
![[Pasted image 20240905000539.png]]
七大问题

# Motivation
conventional wisdom vs new wisdom

Power是免费的.
power是很贵的，我们应该用更多的cell，然后想办法在不用的时候关掉大部分

dynamic power最重要
实际上static power能到40%

单核芯片可靠性很高，错误只会出现在pin上
实际上65nm之下错误率变高了

可以不断提升chip size
65nm以下提升chip size是不合理的

研究者应该通过流片来验证自己的想法
太贵了，应该有其他方式

Performance improvements yield both lower latency and higher bandwidth
实际上bandwidth的提升速度基本上是latency的平方倍

load store开销小
实际上load和store的开销是最大的，比浮点运算还高

ILP很好找到
实际上不好找到了，ooo，bp，VLIW已经到头了

摩尔定律
早就寄了
![[Pasted image 20240905001511.png]]

增加频率是提升性能的重要手段
早就提不上去了，现在重要的是并行度

# Application and Dwarfs
## seven dwarfs
提出的七个重要的程序问题，这些问题对于计算机科学来说非常重要
- Dense Linear Algebra
- Sparse Linear Algebra
- Spectral Methods: fft这种
- N-Body Methods
- Structured Grids 比如微积分
- Unstructured Grids
- Monte Carlo

## more dwarfs
文章主要提出了四种：
- Combinational Logic：大量数据做简单运算，比如计算Cyclic Redundancy Codes
- Graph Traversal 比如查表就是图遍历
- Graphical Models：Bayesian networks and Hidden Markov Models这种
- Finite State Machines
还有几个大方向
- ML
- Database
- Computer Graphics and Games
![[Pasted image 20240905002602.png]]
## Composition of Dwarfs
很多任务都有很多dwarf，其组合模式主要有两种主要有两种模式
- 时间离散：在同一组核上占据不同的时间
- 空间离散：分布在不同的核上
这就提出了两个软件上的问题：
- 如何选择组合模型：是时间离散好还是空间离散好
- 在dwarf间如何转换data structure。

## Intel Study
将computation主要分为三个
- Recognition：获取data 然后 构成数学模型
- Mining：在大量数据中寻找一个数学模型
- Synthesis：生成新model。
这合在一起就是RMS。常见的RMS就是multimodel recognition and synthesis over large and complex data sets
![[Pasted image 20240905003247.png]]

# Hardware
## Processor: Small is beautiful
频率快到头了，连intel都知道了。因此多核系统和并行变得重要了

小processor如今是作为未来多核系统的最好的block，因为：
- 并行省功耗
- 并行系统的单位面积性能好
- 巨多小核可以做更精细的dvfs
- 小核容灾性强
- 小核好开发
- 每个都低功耗，而且在设计的时候容易被计算出来

### 多大合适？
假设并行度很高而且area无限大，我们应该看的是energy per instruction，但显示不是这样的。
我们要寻找的方式是尽可能不增加energy per instruction或者energy per area的手段

总体而言，如果用energy-delay product(SPEC^2 / W)来衡量微架构的energy-delay，那么可以说，流水线是更划算的，而ooo会不可避免的使energy-delay product变坏。

目前来看，比较短的顺序处理器是area-energy最划算的（不是性能！）
5-9级的流水线，加上FPU，Vector，SIMD处理单元，看上去是比较合适的未来处理器单元。

### 我们真的会有1000多核的commercial chip吗
因为最优架构固定了，因此manycore的数量可能是随着工艺而指数增长的

答案是可能的 多核系统的数量可以赶上工艺进步

### One size fits all？
并不
异构：可以用ooo核跑并行度低的代码

## memory unbound
DRAM的latency就到这里了，但是可以提高带宽

## NOC
#skip 没看完

## Communication primitives
传统的symmetric multiprocessors (SMPs)因其对称性放弃了很多优化可能，因此chip-scale multiprocessors (CMPs)出现了：
- inter-core bandwidth比正常smp要大得多
- inter-core latency要小得多
- 同core内的coherence处理简单的多，
#skip 

## Dependability
#skip 

## Performance and Energy Counters


# Programming Models
就是硬件抽象层，OpenMP那种
Programming Models必须在性能和实现便利性上做折中。
#skip

# system software
#skip 

# Metric of success

后面 #skip 了