在多核中，只有部分操作是会引起歧义的，比如对同一个地址的操作。
上锁可以解决这样的歧义。
换个角度，锁之间的load和store是不会引起歧义的。
因此这些load和store就具有重新排序的潜力.
针对reordering可以做出下面的优化：
1. non-fifo的合并stb。其实是store可以bypass store
2. speculation支持更加简单。
3. 可以更好地和coherence进行耦合，从而提升效率

### an example of relaxed memory consistency
![[Pasted image 20230714122611.png]]
简而言之，其设计是：
1. 后面的load可以bypass前面的store
2. store和store，load和load，load后的store，还有RMW，如果不是同一个地址，可以随意排序。如果是同一地址，则需要保序。
3. 任何指令都需要和fence保序


