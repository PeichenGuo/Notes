---
Book: A Primer On Memory Consistency and Cache Coherence
Chapter: 1
Summary: introduction
---
多核共享内存的情况下甚至很难定义什么是”正确”

==Consistency models define correct shared memory behavior in terms of loads and stores , without reference to caches or coherence.==
consistency只用load和store定义memory behaviour而忽略其他内容，比如cache和coherence。
consistency是单线程问题，是在处理load和store的顺序问题

==a coherence problem can arise if multiple actors  have access to multiple copies of a datum and at least one access is a write.==
coherence涉及到多核，多个核一起访问一个datum的情况

Last level Cache (LLC) 可以看做是memory side的cache，而非core side的cache。这样就有很多事情好解释了
![[Pasted image 20230712141605.png]]

Coherence问题的根本只有一个：对同一个对象的多个access，并且其中一个是写。

### 两种coherence：
1. Consistency-agnostic coherence。write完成前被传播到所有core，ie，write被同步传播。所有使用这种协议的core都可以假设在访问一个完美的atomic memory。大部分都是使用的这种同步 。**下面说的coherence在不加前缀时，都默认是这种**
2. Consistency-directed coherence。write被异步传播，write可以在被传播给所有core前完成，ie，可以看到stale value。主要是gp gpu在用

### COHERENCE INVARIANTS for Consistency-agnostic coherence
==single-writer–multiple-reader (SWMR)==： 每次最多只有一个写者，可以有多个读者。
Data-Value Invariant：最新写入的值要在下次被读前被正确地传播给所有人


### coherence vs consistency
==Cache coherence does not equal memory consistency.  ==
==A memory consistency implementation can use cache coherence as a useful “black box.”==

