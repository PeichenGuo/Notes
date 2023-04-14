---
Author: "essica H. Tseng"
Year: 2003
Journel/Conference: "SIGARCH"
Summary: "用更少port的banked prf实现了很不错的效果"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
整了个性能很不错的banked prf。
主要的点在于，bypass的信号就不会占用读口了。
比之前的设计多了一些port，但由于每个bank都不大，整体的面积开销其实可以接受。
对于一个四发core而言，prf大小是之前的1/3，ipc只降了5%

### Motivation
prf太大
insight：bypass的数据较多，可以不抢读口

### Solution
每个bank 2r 2w。而且把rs分成了rs left和rs right，2个rd port，一个是left 一个是right，这简化了逻辑。
2个w是因为他们发现2个w是 性能和面积时序的最佳tradeoff点，可以解决大部分write conflict但也不至于过多port。
支持read sharing，即多个读公用一个port读同一个寄存器

![[Pasted image 20230414134608.png]]
多加了一个allocate阶段。如果上一阶段发现conflict，直接kill掉下一次issue的group。


但有一段没太懂，decode那段。

### Evaluation
![[Pasted image 20230414134747.png]]
bank/read/write/bypass/sharing
可以看出822yy是个不错的选择，并且bypass和sharing提升很大。

## Unsolved Question
