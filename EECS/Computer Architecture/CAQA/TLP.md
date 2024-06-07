---
Book: CAQA
Chapter: 5
Summary: BlaBla
---
# Introduction
parallel processing:一个任务多个线程
request-level parallelism。多个任务多个线程
## _Multiprocessor Architecture: Issues and Approach_
现在的multiprocessor分为两种：
1. SMP： _symmetric (shared-memory) multiprocessors_：也叫 _centralized shared-memory multiprocessors_ 核少 有一个shared的memory，访问不同存储器的延迟是一致的，因此也被称为UMA(Uniform Memory Access)。与之相对的是NUCA(NonUniform Cache Access)。
2. DSM：_distributed shared memory (DSM)._ 核多。访问local的bandwidth高，lantency小
![[Pasted image 20231103110708.png]]
![[Pasted image 20231103110716.png]]
两种模式下线程通信都是用共享地址做的，因此地址空间都是共享的。
## _Challenges of Parallel Processing_
两个主要challenges：
1. 程序并行度不够
2. communication开销大
体系结构主要解决第二个，第一个大多由软件解决。

# _Centralized Shared-Memory Architectures_
也就是SMP构型。
这部分讲的是cache coherence，可参考[[A Primer On Memory Consistency and Cache Coherence]]
## 基本解决方案
主要有两类：
- directory。内存块的共享状态放在一个directory里。SMP和DSM的directory实现方式很不一样。SMP使用集中目录，DSM使用分布式目录
- snooping。通过广播-监听的方式来实现。每个缓存都监听自己关注的内存块。
## Snooping
现在多是write invalid protocol。写的时候会让其他副本无效
另一种是write update protocol。每次写都更新所有该条目的共享cacheline，因此占用很多带宽，不常用。
snoop bandwidth 可能不够用
最基本的MSI协议：
![[Pasted image 20240604155844.png]]
在此之上有MESI，加了一个exclusive，代表clean且独占。可以进行写入，升级为M态。
此外还有MOESI，加了一个owned态，代表已修改且memory中的副本过期，其他核read miss时由owned提供副本。原本M态下别的核要read shared，需要把m态内容写回然后降级为S，加入O了之后是将副本发到对应核然后从M态降级为O态，减少了写回的过程。

# _Performance of Symmetric Shared-Memory Multiprocessors_
两种miss：
1. _true sharing misses_: write获得权限时的miss和read一个被更改的word时产生的miss
2. _false sharing misses_：读一个没有被更改，但该block其他word dirty导致整个block invalidate。这种情况是可以通过改变颗粒度解决的
### online transaction-processing (OLTP) workload
这个workload是一组发出请求的客户端进程和一组处理这些请求的服务器组成。71%的时间在用户模式下花费，18%在os系统中，11%是idle（等IO）
![[Pasted image 20231103115908.png]]
这是一个online transaction-processing (OLTP) workload的结果，比较有意思的是，大部分的gain来自1mb->2mb的扩大，L3再大对性能的增强就不多了。
![[Pasted image 20231103120133.png]]
可以看出，1mb时最大的两个latency来源是instruction和capacity/conflict。当cache变大后，这两个source会变得很少，少于sharing miss。但两种sharing miss跟cache大小关系不大，因此cache再变大也不会减少延迟了。

那sharing miss跟什么有关呢？
![[Pasted image 20231103120423.png]]
可以看出，核数越多，true sharing越多。
![[Pasted image 20231103120501.png]]
block size越大，true sharing越小；在false sharing越大，但大的幅度很小。

### OS workload
![[Pasted image 20231103133728.png]]
os workload会有很多idle time。主要是等disk。
![[Pasted image 20231103133920.png]]
os的kernel代码更少，但会有更多的miss，原因有二：
1. os初始化pages，会有很多compulsory misses
2. os share data，会有coherence misses
![[Pasted image 20231103134233.png]]
图中可以看到，cache的大小不影响compulsory miss，因为kernel就是有很多page initial。
![[Pasted image 20231103134443.png]]
随着block size，traffic也会变多（因为传递的是block data）。
# _Distributed Shared-Memory and Directory-Based Coherence_
这部分讲的是cache coherence，直接看[[A Primer On Memory Consistency and Cache Coherence]]

# Synchronization: The Basic
主要讲lock和unlock的实现
构建同步操作的操作拥有的共同特性：读取和更新存储器值的方式可以判断是不是以原子形式执行。比如test-and-set这种。
riscv用的是lrsc机制。lr会创建一个reservation，如果在sc操作前有store对该单元进行了修改，则reservation失效，或者进行了context switch，那么sc失败；否则sc成功，并返回0。
lr sc实现一般是使用reserved register来实现。这个reg会跟踪lr的地址，sc会核查该地址和reserved reg之间的差异来看是否sc成功。context switch，interrupt，或者其他lrsc会让该寄存器失效。因此在lrsc之间插指令要万分小心，如果出现interrupt或者exception就会deadlock

# Memory consistency
这部分讲的是Memory consistency，具体看[[A Primer On Memory Consistency and Cache Coherence]]

sequential consistency:每次执行结果一样，仿佛按顺序处理所有memory req，不同处理器的req任意交错。

程序员视角：
如果对共享数据的所有访问都是由同步操作进行排序的，那么就称这个程序是synchronized。什么是同步操作排序？就是在读前写后用同步操作隔离。
当没有同步操作排序的时候，称为data race，否则就是 data race free。

## relaxed consistency model 
允许读写乱序进行，用同步操作保序。一共有四种冲突，RAR，RAW，WAR，WAW。
- 如果只放松RAW顺序，就是TSO(total store order)
- 放松WAR和WAW得到PSO(Partial Store Order)
- 放松四个冲突的有多重模型。powerpc的weak ordering（WO）和RISCV使用的release consistency。
release consistency区分了acquire和release。这样的区分基于一个insight：acquire在使用共享数据前执行，release在使用共享数据后执行。因此我们发现，aq之前的sl不需要在aq之前完成，rl之后的的sl不需要等rl结束。换句话说，aq后的sl需要等待aq结束，rl需要再之前的sl结束后开始。
FENCE指令用于确保其之前的指令全部完成，即aq和rl一起的效果。
# Cross-Cutting Issue
## 编译器与consistency model
实际上编译器优化和consistency有很大关系。在没有显式同步操作的时候，编译器不能随意交换读写操作
## 用speculation来hide strict consistency model的latency
 乱序执行ls，如果发现执行后的结果和sc结果不一样，就rollback重执行。好处有三：
 - 这种方式下的sc和更宽松的内存模型类似
 - 对于支持预测执行和ooo的core而言实现没有太大overhead
 - 程序员面对的是简单的sc模型，需要考虑的事情少很多
这个解决方案遇到的主要是：编译器可以从宽松consistency model中获利。

## Inclusion and its implementation
多级缓存之间可能是inclusive的，即上层是下层的子集。
这会导致下层的invalidation需要转发上层。
当不同层级的cache的block size不一样时会比较麻烦，因为可能出现下层一次invalidation需要上层多次invalidation的情况。
现在主流实现还是inclusion+多级block size一致。



# _Putting It All Together: Multicore Processors and Their Performance_
三种multiprocessor
![[Pasted image 20231103140411.png]]
![[Pasted image 20231103140522.png]]
![[Pasted image 20231103140531.png]]
![[Pasted image 20231103140631.png]]

# _Fallacies and Pitfalls_
Pitfall： Measuring performance of multiprocessors by linear speedup versus execution time.
Fallacy: _Amdahl’s Law doesn’t apply to parallel computers._
Fallacy: _Linear speedups are needed to make multiprocessors cost-effective._
Pitfall: Not developing the software to take advantage of, or optimize for, a multiprocessor architecture.
