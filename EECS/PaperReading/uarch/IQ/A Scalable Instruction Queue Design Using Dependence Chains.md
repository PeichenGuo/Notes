---
Author: "PeichenGuo"
Year: 2002
Journel/Conference: "ISCA"
Summary: "将IQ分成了segment，每段指令在delay小于阈值后向下移动。并通过chain的方式简化countdown过程，只有chainhead才会被boardcast新的delay信息。chainhead一般选择关键指令。"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract


### Motivation


### Solution
![[Pasted image 20230514183959.png]]
将IQ分成了多个segment，每个指令只有在delay数小于该segment阈值后才会进入下一个segment。每个segment的delay阈值都相比上一个大二。
只有进入最后一个segment，也就是segment0的指令才会被issue。segemnt0的进入条件是delay为1或者0。在segment0中指令的行为类似传统iq，会进行dynamic schedule，会等待boardcast。

由于delay的更新是实时性的，需要boardcast新的delay信息，因此本文采用了chain的方式来简化这个传播。
chain是一串有依赖关系的指令。后面的指令会依赖于或者间接依赖于chainhead。chainnode的initial delay就是前面所有chain node的delay之和。
只有chainhead会每周期接受新的delay信息来更新自身delay。
当chainhead进入下一个segment时，更新该chain上所有人的delay。
当chainhead被issue后，其所有chainnode进入自由倒数模式，每个cycle自减1。

chainhead应该选取比较重要的指令，比如load。
一个指令可以同时是两条chain的chainnode，但这时这个指令应该同时是第三个chain的chainhead。

==具体实现没看==

### Evaluation


### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
