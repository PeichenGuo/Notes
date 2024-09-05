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

