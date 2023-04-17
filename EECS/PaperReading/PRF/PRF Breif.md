## 优化时序
### banked
[[Banked Multiported Register Files for High-Frequency Superscalar Microprocessors]]. 
	简单的多bank，每个bank端口也不多，证明这种情况下也能有还可以的性能。但实现上加了一个周期。



### multi-level
[[Multiple-Banked Register File Architectures]]
	做两层prf，把bypass数据放在L2，非bypass数据放在L1。并设计了一些机制把L2的fetch到L1上来减少access lantency。

[[Reducing the Complexity of the Register File in Dynamic Superscalar Processor]]
	做两层prf，把没有consumer的放在L2。也相当于提前retire了，只是在L2做了备份。

## 优化性能
### 改变preg生命周期
#### 提前deallocate
[[Checkpoint Processing and Recovery---Towards Scalable Large Instruction Window Processors]]
	在没有人读且被重写后可以释放preg。checkpoint算是一个consumer以确保checkpoint释放前不会有preg被错误释放，这一点有点类似[[Out-of-Order Commit Processors]]中对preg生命周期的管理。

[[Cherry---Checkpointed Early Resource Recycling in Out-of-order Microprocessors]]
	提前回收最老的未执行结束(Unsolved) 的branch、store、load后的preg。对于多线程$Oldest\{U_B, U_L, U_S\}$后的preg，对于单线程$Oldest\{U_B, U_S\}$。如果出现中断异常，checkpoint回去然后ooo执行

[[Increasing processor performance through early register release]]
	和[[Checkpoint Processing and Recovery---Towards Scalable Large Instruction Window Processors]]类似，只是给每个bitcell都做了一个shadow bitcell，在sram层次上做checkpoint，恢复的更快。

[[Reducing the Complexity of the Register File in Dynamic Superscalar Processor]]
	见上文。

[[Speculative Register Reclamation]]
	在被redefine的时候就释放preg，不用等待rename的指令commit。并把其中的数据存到payload ram之中。

#### 推迟allocate
[[Virtual-Physical Registers]]
	通过VP reg的方式在exec结束后再allocate preg。decode rename先给个VP reg，用来做wakeup、dependency之类的事情，exec后再给个preg用来放数据

[[Delaying Physical Register Allocation Through Virtual-Physical Registers]]
	和[[Virtual-Physical Registers]]内容一样，只是在处理死锁上有差别。前者是分配一定数量的preg给最老的指令，确保这些老指令一定能拿到preg。本文的方法是当老指令执行结束发现没有preg时，从年轻指令那里抢一个。

#### 减少allocation
[[Spartan --- Speculative avoidance of register allocations to transient values for performance and energy efficiency,]]
	对于short-lived（在wb结束前就已经被rename）的reg不进行allocation

### 共享preg
[[A novel register renaming technique for out-of-order processors]] 
	consumer可以和producer共用preg

[[Register Packing --- Exploiting Narrow-Width Operands for Reducing Register File Pressure]]
	把短的数据放在同一个reg的不同片段，从而可以让多个数据共用一个preg。