在多核中，只有部分操作是会引起歧义的，比如对同一个地址的操作。
上锁可以解决这样的歧义。
换个角度，锁之间的load和store是不会引起歧义的。
因此这些load和store就具有重新排序的潜力.
针对reordering可以做出下面的优化：
1. non-fifo的合并stb。其实是store可以bypass store
2. speculation支持更加简单。
3. 可以更好地和coherence进行耦合，从而提升效率

### an example
