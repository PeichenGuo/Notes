---
Author: "D. Ernst; A. Hamel; T. Austin"
Year: 2003
Journel/Conference: "RIOSJournel"
Summary: "BlaBlaBla"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
通过编译器来实现好的schedule算法太复杂
通过硬件来实现好的schedule硬件开销太大

本文旨在提供一种算法不太难且硬件开销也不太大的实现方法。

在软件上执行类似compile-time list scheduling的做法，即预测指令latency然后排列指令来填充等待窗口。
在硬件上，front-end加了一点东西来提升这个list scheduling的性能

### Motivation
通过编译器来实现好的schedule算法太复杂
通过硬件来实现好的schedule硬件开销太大


### Solution
![[Pasted image 20230513122923.png]]
简单来说，就是把每个指令预测一个latency放入一个count down queue里，每周期前进一个entry，当走完时插入main queue。当count down过半的时候，就把该指令插到main queue的后一个中（如图）。这个做法就相当于预测+重排，只是实现比较简单。

当main queue走到头的时候就进入exec。
先检查operand是不是齐了，如果齐了，就进入exec；如果没齐，就replay，重新插到countdown queue末尾。

==具体实现没看，但感觉实现起来有些问题==

### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
