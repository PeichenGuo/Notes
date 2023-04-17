---
Author: "D Balkan"
Year: 2006
Journel/Conference: "PACT"
Summary: "有很多数据可以bypass且很短命。本文预测出这些指令，并不对其进行preg allocation。"
Rate: 2
Question: "None"
Eureka: "None"
---
### Abstract
本文发现有很多指令是可以bypass获取数据并且short-lived的，于是对这些指令进行预测，不对其进行preg allocation，从而可以节省大量的preg。
在SPEC2000中这类指令有45%左右，本文的方法预测正确率能达到97%。

### Motivation
定义short-lived:
假设指令a的rd是某架构寄存器ar1。如果当指令a还没执行完写回ar1的preg前，ar1就被映射到其他的preg上了，此时这个指令被称为short lived。
这样的指令在spec2000中占86%

定义transient指令：
1. short lived
2. 只有一个consumer
3. consumer指令必须在exec结束前issue（可以用到bypass）
4. 在renamer指令和这条指令间没有branch（不会产生miss prediction从而导致没有存到prf的指令数据丢失）
5. consumer不会因为load latency misprediction或者memory dependence misprediction而重新执行。（和4原因一样，replay会导致数据丢失）

这样的指令在SPEC2000中占45%
![[Pasted image 20230415132423.png]]
spec2000的指令占比

根据这个insight，本文要预测出transient指令并且不对其做preg allocation

### Solution
大概做法是给icache上加个bit，代表它是不是transient。默认下猜不是，每次执行完后更新这个bit。这个prediction可以留存icache时间那么长，还是挺不错的。
正确率：
![[Pasted image 20230415133256.png]]
==具体实现没看==

### Evaluation
![[Pasted image 20230415133038.png]]
性能上平均ipc好28%

![[Pasted image 20230415133025.png]]
功耗上平均好36%
### Unsolved Question


### Related Works
#### Later Works
[[Speculative Register Reclamation]]
#### Previous Works

#### Similar Works
