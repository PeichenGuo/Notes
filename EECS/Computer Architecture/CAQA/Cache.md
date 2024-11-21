---
Book: CAQA
Chapter: 2
Summary: BlaBla
---
参见[[超标量流水线设计]]的cache章节
## 地址转换问题
https://zhuanlan.zhihu.com/p/107096130
由于在MMU前使用的都是虚拟地址，而实际访存用的是物理地址，因此存在一个地址转换问题。即索引cacheline的是virtual的还是physica的，存储的tag是virtual的还是physic的。
这个问题就产生了三种cache。VIVT，VIPT，PIPT。（PIVT没有实际意义）
在介绍三种cache前要介绍两种问题：ambiguity和alias
### ambiguity和alias：歧义与别名
**Amibiguity 歧义** 指的是不同的数据在cache中有相同的tag和index，这样就没法区分该cacheline是否对应正确的数据。这种情况只会发生在同一个虚拟地址对应不同的物理地址的情况，也就是不同进程间切换时会发生的问题。
因此在切进程的时候，OS会flush掉所有的cache进入memory。

**alias 别名**指的是同一个数据在cache中有不同的tag和index，这样写一个cacheline的时候无法更新另一份副本。这种情况会发生在不同虚拟地址对应同一个物理地址的情况，也就是不同进程间通信（使用同一变量）的情况。
可以通过直接访问不走cache的办法。

### VIVT
![[Pasted image 20240827143148.png]]
 主要的问题是只能通过flush的办法解决ambiguity问题。

### PIPT
![[Pasted image 20240827143816.png]]
由于要先经过MMU换算，因此hit time增加。但优点是绝对没有ambiguity和alias的问题，不需要软件维护

### VIPT
![[Pasted image 20240827144000.png]]
**VIPT不存在ambiguity问题。**
因为VIPT的tag长度是根据物理页对应的，也就是出去物理页offset外的那个页码，就是ptag。因此一个tag只对应一个页，不可能出现同一个tag对应两个页的情况，再加上offset唯一，因此VIPT每个cacheline只能对应一个物理地址。

**VIPT可能存在alias问题。**
当cache一路的大小小于等于页大小的时候，就不会出现alias问题。
我们现在假设直接映射cache是8k，cachline是256byte，页大小是4k。<7:0>是cacheline offset，<12:8>是VI。此时<11:0>是页内偏移，因此VI的<11:8>是等于PI的，但第12位VI不一定等于PI。因此就会出现两个VI对应一个PI的情况，即指向同一物理地址的不同虚拟地址同时存在在cache之中。
举个例子，对于上面的那个8k组大小，4k页大小的cache，os把两个进程的两个虚拟地址分配到了同一个物理地址，比如进程0的虚拟地址0x1000和进程1的0x420000都分配到了同一个物理地址。此时在cache中，根据VI寻址会分配到两个entry，一个0x1000和0x0000。而由于存的是PT，上面两个虚拟地址0x1000和0x420000会分别分配到对应的两个entry里，但是他们指向的是同一个物理地址，此时发生alias。
解决方案就是在操作系统固定分配地址的时候，确保不会有两个VI对应一个PI。也就是在映射虚拟地址到物理地址时，如果是那种会共享物理地址的信号量，则其共享位需要是同一个数字。以上一个例子而言，就是第12位要固定一个数，1或者0，这样一个VI就对应一个PI，即对于同一个物理地址，其虚拟地址不能既是0x1000又是0x0000。
硬件的解决方案就是让当cache一路的大小小于等于页大小（一般是4k）的时候。
VIPT 除了使用cache大小对齐的方式还可以使用cache bank的方式来解决；  
在8K的cache中，被分为bank0和bank1；使用\[11:0\]来寻址index，同时命中bank0和bank1的cache line；  
tag使用\[31:12\]来做MMU转换，hit后，通过Paddr\[12\]来选择选择bank0或bank1的cache line；

### 总结
VIVT Cache问题太多，软件维护成本过高，是最难管理的高速缓存。所以现在基本只存在历史的文章中。现在我们基本看不到硬件还在使用这种方式的cache。现在使用的方式是PIPT或者VIPT。如果多路组相连高速缓存的一路的大小小于等于4KB，一般硬件采用VIPT方式，因为这样相当于PIPT，岂不美哉。当然，如果一路大小大于4KB，一般采用PIPT方式，也不排除VIPT方式，这就需要操作系统多操点心了
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

