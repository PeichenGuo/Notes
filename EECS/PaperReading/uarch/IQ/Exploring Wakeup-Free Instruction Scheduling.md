---
Author: "Jie S. Hu, N. Vijaykrishnan, and Mary Jane Irwin"
Year: 2004
Journel/Conference: "HPCA"
Summary: "本文在cyclone的基础上，为了减少其资源冲突和issue port冲突，新设计下iq每个entry计时，计时结束后查看reg是否ready，如果ready再issue。"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
本文研究了不同实现的限制下，latency的可预测性。发现下面三点会影响boardcast-free结构的性能
1. 把指令提到队首的structure problem
2. issue candidate很少
3. issue port的resource conflict
本文根据其研究提出了一个解决方案：传统selection+precheck enhanced wake-up free

### Motivation
本文研究了[[Cyclone --- A Broadcast-Free Dynamic Instruction Scheduler with Selective Replay]]。下图为其结构：
![[Pasted image 20230514165110.png]]
为了解决资源冲突，cyclone中main queue的left column有更高的优先级。下图显示了资源冲突情况：
![[Pasted image 20230514165131.png]]
发现了两个问题：
1. 前两个column资源冲突太重
2. 发射端口不够用

### Solution
#### WF-Precheck
为了解决上面提到的问题，本文提出了下面的架构
![[Pasted image 20230514171844.png]]
每个entry都有一个timer，timer到了就发。
这样每个entry有timer避免了指令移来移去，从而解决问题1

为了解决问题2，也就是发射端口不够用。
可以先检查reg ready不ready再issue，从而减少issue port开销。

==后面还对segment iq，加不加load miss predictor，加不加load-store Dependency predictor做了测试，但没看==

### Evaluation
==后面没看，摸了==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works
[[Cyclone --- A Broadcast-Free Dynamic Instruction Scheduler with Selective Replay]]
#### Similar Works
