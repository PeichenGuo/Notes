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

# _Performance of Symmetric Shared-Memory Multiprocessors_
两种miss：
1. _true sharing misses_: write获得权限时的miss和read一个被更改的word时产生的miss
2. _false sharing misses_：读一个没有被更改，但该block其他word dirty导致整个block invalidate。这种情况是可以通过改变颗粒度解决的
### online transaction-processing (OLTP) workload
![[Pasted image 20231103115908.png]]
这是一个online transaction-processing (OLTP) workload的结果，比较有意思的是，大部分的gain来自1mb->2mb的扩大
![[Pasted image 20231103120133.png]]
可以看出，1mb时最大的两个latency来源是instruction和capacity/conflict。当cache变大后，这两个source会变得很少，少于sharing miss。但两种sharing miss跟cache大小关系不大，因此cache再变大也不会减少延迟了。
那sharing miss跟什么有关呢？
![[Pasted image 20231103120423.png]]
可以看出，核数越多，true sharing越多。
![[Pasted image 20231103120501.png]]
block size越大，true sharing越小，false sharing越大，但大的幅度很小。

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
