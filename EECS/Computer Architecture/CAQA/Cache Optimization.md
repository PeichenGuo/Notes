---
Book: CAQA
Chapter: 2
Summary: BlaBla
---
# 优化方式
## 基本优化方式
![[Pasted image 20240827003657.png]]
### 增大cache块大小——减少miss rate
![[Pasted image 20240827002343.png]]
在同样大小下 太大会导致line数量不够而性能下降
### 增大cache——减少miss rate


### 提高associative——减少miss rate
两个经验规律：
1. 对于特定大小的缓存，8路和全相连差不多
2. 2：1规律。N的直接映射和N/2的两路组相联类似

### 多级缓存——减少miss penalty

### 读缺失大于写缺失——减少miss penalty

### 避免在索引缓存期间进行地址变换——缩短hit time
![[Pasted image 20240827003422.png]]
这是一个VIPT。

总结
![[Pasted image 20240827003811.png]]
## 10 advanced optimizations of Cache performance

### Small and Simple First-Level Caches —— 减少hit time
- reduce hit time and power
- direct map has lowest hit time. 
- **as associativity grows, power and hit time grows.** 
- However, modern cache implement pipeline. Longer hit time do not impact directly on the clock cycle time. 
- 可以把cache按bank分开，从而每次访问只激活一个bank。l3很适合这个方法

### way prediction —— 减少hit time

2-way 90% accurate
4-way 80% accurate
when prediction success, lower hit time and lower energy consumption 
when fail, extra cycle and same energy consumption
难以pipeline
性能提升不多，但有很大的能耗提升

### pipelined access and mutibanked caches——增大bandwidth
bandwidth ++
pipeline能增大带宽，提高频率。但会增大hit lantency。

多个bank可以并行提供数据，实现并行访问。理想情况是多个mem req映射到不同的bank上。所以bank mapping很重要。
以最简单的bank mapping sequencial interleaving为例。这种mapping是把相邻的地址映射到不同的bank上。比如addr mod 4 然后分配到四块bank上。这样相邻的访问就会分散到不同的bank上

### nonblocking cache —— 增大bandwidth， 减少miss penalty

非阻塞缓存有较大提升。
实现nonblocking的时候会遇到两个问题。1.如何仲裁ls的先后。什么样的可以bypass。2.如何handle乱序返回的miss resp——引入了MSHR。

### critical words first and early restart——减少miss penalty 

处理器需要的内容往往只是一个word，因此可以有两种优化：
critical word first: 先请求需要的，然后立刻返回它，然后再请求剩下的
early restart：正常请求，当请求到需要的内容时立刻返回
cacheline越大 bonus越大

### merging write buffer——减少miss penalty
miss penalty --
实现write buffer，并实现stb合并。即新来的store可以直接更改在stb上，新来的load可以直接从stb里读东西。
一次写多个字可以写的更快
注意：IO不能映射到stb里，这样会错
![[Pasted image 20240827004727.png]]


### 编译器优化 —— 减少miss rate
miss rate --
- loop interchange：可以更改loop的顺序来增强数据的空间一致性
- blocking：可以把loop分块，从而减少循环的区间，让数据可以放在cache中，从而减少capcity miss。如果block size足够多，所有数据都能放在寄存器里
### hardware prefetching —— 减少miss rate
可以用stream buffer。prefetch来的东西可以放在stream buffer里，然后对比req和stream buffer内的内容。
可以减少大概50%-70%的miss
prefetch的关键在于，可以一边执行prefetch 一边处理器继续流水线运行

### 编译器控制的prefetch—— 减少miss rate
编译器要控制的prefetch要注意nonfaulting，也就是不要产生exception也不产生violation

### 加HBM来拓宽memory heirarchy 
问题：巨大的l4如果block和line size一样，就需要巨大的tag ram，因此block不能很小
带来两个问题：
第一个是大块中由很多用不到的内容。这回导致存了太多没用的东西，让传输效率降低，污染dram缓存。可以通过子块的方式，让一部分block内容没用
还有个解决方案就是把tag和data放在hbm sdram的同一行里，这样打开一次就可以看到tag和data。这个需要特殊设计的dram。（L-H缓存），延迟较大，毕竟还是需要打开行。
还有融合缓存(alloy cache)的方法，直接把tag和data放在一起，并direct map。miss rate会很高，但一个HBM周期就能访问到

![[Pasted image 20230310211054.png]]

### 优化总结
减少miss rate的办法：
1. 更大的cache block
2. 更大的cache
3. 更高associatuve
4. 编译器优化
5. prefetch (hardware and software)
6. evict queue

减少miss penalty的方式：
1. 多级缓存
2. load miss first
3. MSHR
4. critical word first

减少hit time：
1. VIPT 防止地址转换
2. way prediction
3. 更小的直接映射L1cache

