### Definition  
The result of any execution is the same as if the op- erations of all processors (cores) were executed in some sequential order, and the operations of each individual processor (core) appear in this sequence in the order specified by its program
符合以下约束：
1. 结果和顺序执行的结果一致
2. 执行顺序和程序升序一致
即，==memory order = program ordrer ==


<p 指program order 小于，<m指memory order小于。op指operation，S指store，L指load
![[Pasted image 20230712162937.png]]
![[Pasted image 20230712163153.png]]

### Optimized SC implementation with cache coherence 
#### Non-binding prefetch
prefetch下一个块
经常software做
与之有关的是speculative造成的squash，这个squash也会带来prefetch的效果

#### Dynamically Scheduled Cores
在ooo的情况下就会出现很多violation，所以有些ooo的core会做memory consistency speculation
两种check是不是有violation的方式：
1. commit load 前看眼对应的load的block是不是还在。如果不在了，说明L2前有个store改了这个block，block被invalid了。这个load squash掉重做。
2. commit的时候replay speculative load。[[Memory Ordering --- A Value-Based Approach]]

### Non-Binding Prefetching + Dynamically Scheduled 
