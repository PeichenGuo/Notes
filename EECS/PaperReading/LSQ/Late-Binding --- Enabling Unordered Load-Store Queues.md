---
Author: "PeichenGuo"
Year: 2007
Journel/Conference: "ISCA"
Summary: "通过unorder、Late-Binding的LSQ来缩减ls在lsq中的时间，从而有效减小lsq大小。并提出了三种基于network flow control的处理partitioned lsq的overflow的方法。"
Rate: 4
Question: "None"
Eureka: "None"
---
### Abstract
本文通过在unorded的lsq中做late-binding，也就是在ls issue后才把ls指令放到lsq中，而非decode中放入lsq。
同时为了让lsq更scaliable，本文把lsq分成了多个bank。
本文也提出了三种基于network flow control的处理overflow的方法;
1. instruction replay, 
2. skid buffers
3. virtual-channel buffering

### Motivation
##### lsq
lsq中很多ls指令进来的时候都不ready，没法issue，白白浪费空间。不如issue时在发射。

##### overflow
现在为了让处理器有更多的in-flight指令，会使用partitional architecture，说人话就是把各个部件都切碎，从而提高时序性能，比如[[Distributed Microarchitectural Protocols in the TRIPS Prototype Processor]]。first-level cache可能使用基于地址的partition，比如按地址映射到某个bank。如图：
![[Pasted image 20230417203913.png]]
但这样映射就会造成地址分配不均，可能某个lsq被填满，但其他lsq还空闲。
可以单纯地扩大每个lsq，但这样扩大一个就是扩大N倍，很不划算。
因此本文是设计一个有效的overflow应对机制

### Solution
##### LSQ
LSQ主要需要干的三件事：
1. commit store。当store是speculative free的情况下，再commit到cache中
2. violation detection：抓raw错误。因为load是乱序发的，如果有个更老的st后于ld进入lsq，就需要replay这个ld，因为这个ld读到的东西是错的
3. store forwarding：用store的data给load做forwarding

因为本文设计是unordered的，不像之前age-index的，没法知道新旧顺序，所以需要一个buffer来存age，从而完成上面的三件事。本文采用了一个Age CAM结构来做这件事。Age CAM输入是一个数和greater/less/equal，输出大于、等于或小于这个数的数据。
![[Pasted image 20230417194810.png]]
前两个的解决方案很直观，store forwarding略显复杂。如果老store只hit中一个，那么就简单地返回这一个就行：如果有多个hit了，那就按年龄从新到老，每cycle读出一个覆盖在一起，然后用这个数据返回。

#### overflow handling in partitional LSQ
传统方式是遇到overflow就flush流水线，很浪费。
因此应对方法就是加buffer来存放overflow的指令，避免频繁flush。
但这里有个deadlock可能，当lsq充满了speculative的指令，导致一个non-speculative指令进不来，会导致lsq里面的东西没法commit，前面依赖的指令进不到lsq，从而死锁。
解决方案就是提高non-speculative的优先级

有三个地方可以放：
![[Pasted image 20230417204222.png]]

Issue Queue buffer
相当于就是放在issue queue里先不出来。
需要lsq发一个nack回去给issue queue要求重发。这个nack有种发射时机。

Memory Buffering：Skid buffer
就是放在lsq前面
当预测执行的ls发现lsq满后，放到skid buffer里等着。等前一个non-speculative block commit后，把下个non-speculative block（也就是之前的speculative block）的指令从skid buffer中取出来放到lsq中；如果是non-speculative的ls发现lsq满了，就flush流水线重新执行。

Virtual Channel：
放在network的channel里。
设计两个channel，一个传speculative的，优先度低；一个传non-speculative的，优先度高
剩下的和skid buffer一样，都是non-speculative的卡主就flush流水线；speculative的卡主就在buffer里等着

### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
