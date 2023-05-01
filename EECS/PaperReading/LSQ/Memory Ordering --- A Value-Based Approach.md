---
Author: "Harold W. Cain, Mikko H. Lipasti"
Year: 2004
Journel/Conference: "RIOSJournel"
Summary: "retire前in-order重新执行load指令，并对比第一次执行和重新执行时的值来判断是否产生了memory conflict。通过这种方式避免load queue的associative search。"
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


### Evaluation


### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
