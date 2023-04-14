---
Name: "Checkpoint Processing and Recovery: Towards Scalable Large Instruction Window Processors"
Author: "Haitham Akkary Ravi Rajwar Srikanth T. Srinivasan"
Year: 2023
Journel/Conference: "MICRO"
Summary: " 用branch confidence的方式和定期checkpoint的方式做recovery"
Rate: 4
---
### Abstract
We focus on four critical aspects of a microarchitecture: 
1) scheduling instructions, 
2) recovering from branch mispredicts, 
3) buffering a large number of stores and forwarding data from stores to any dependent load, and 
4) reclaiming physical registers.

Our CPR proposal incorporates novel microarchitectural schemes for addressing these design issues—a selective checkpoint mechanism for recovering from mispredicts, a hierarchical store queue organization for fast store-load forwarding, and an effective algorithm for aggressive physical register reclamation.

### Motivation
大的issue窗口需要更多buffer和组合逻辑，其时序会变差。本文展示了CPR微架构，用更简单的结构来实现相同的性能，从而得到一个较好的时序。

### Solution

### brief
1. Confidence-based checkpoints and aggressive register reclamation.相当于预判了一下哪些branch可能预测错误，然后在其上打断点，减少断点开销。
2. 新的stq，可以用更好的时序做st-ld forwarding
3. Scheduling window的size没有那么重要。128 entry的scheduling window就能满足一个2048 entry的scheduling window
4. bulk retirement。一次可以retire很多条指令。

### sw的大小不重要
![[Pasted image 20230413212403.png]]
可以看到instruction window都是2k的时候，schedule window大小对性能的影响不大。

### checkpoint
在missprediction之后需要回复rename table这种buffer，所以需要做checkpoint。这个的做法就是猜那条branch更容易miss，然后在他上面做断点，如果每256条指令没做断点，就加个默认断点。
每次恢复是恢复到最近的断点，然后重新执行到错误预测的branch。
![[Pasted image 20230413213106.png]]
可以看出性能不错（sel-ckpt)，和开销很大的history based map + walk机制有来有回，而且很接近ideal了。
**zlt说这个东西做太快没用，因为前端fetch周期是固定的，比如四周期fetch。你恢复再快也没用，前段flush完了新的指令还没取回来。**

### STQ
实现了一个两级STQ。第一级存最近n个store，第二级存其他store。L1的st过期了会移到L2。通过一个叫membership test buffer(MTB)的东西简化查找ld是不是hit二级stq，大概就是把地址的一部分映射到一个counter上，如果这个counter是0，就说明二级stq里没对应的st。
**没太懂这个MTB到底有啥用**
### Evaluation
见前文
