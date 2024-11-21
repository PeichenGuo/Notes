---
Author: "Harold W. Cain, Mikko H. Lipasti"
Year: 2004
Journel/Conference: "RIOSJournel"
Summary: "in-order重新执行load指令，并对比第一次执行和重新执行时的值来判断是否产生了memory conflict。通过这种方式避免load queue的associative search。"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
由于传统的associative lq时序差、功耗大、硬件开销大，本文提出了一种基于re-execution的fifo lq来替换传统的lq。
本文的方法非常直观，就是in-order地重新执行一遍retire之前的load指令，并对比re-exec的值和正常执行获得的值。如果相同，则正常retire；如果不同，则把依赖之前load的指令squash

### Motivation
传统lq的CAM结构很拉。本文想的办法是取消associative search，从而不需要CAM结构

这么做的原因是把time critical的任务放在backend执行而非本来就已经任务繁重的阶段执行

### Solution
本文的解决方案非常直观，就是in-order重新执行load指令，并对比第一次执行（premature execution)得到的结果。
![[Pasted image 20230501144111.png]]
本文给流水线加了两个阶段，在wb之后replay load，并cmp load 得到的值
下面三点保证了所有memory ordering violation都能被捕获：
1. replay load前的所有store都已将其值写入L1D
2. 所有load必须以program order进行
3. 造成replay squash的load不应该被执行第二遍。

由于replay的开销不小，可以通过filter的方式来选取需要replay的load从而减少replay的load的总量
==具体没看==

### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
