# Introduction
1994年。IBM设计，摩托罗拉生产，500nm工艺。

The 620 was the first 64-bit superscalar processor to employ:
- 4-width issue
- true out-of­ order execution, 
- aggressive branch prediction, 
- distributed multientry reservation stations, 
- dynamic renaming for all register files, 
- six pipelined execution units, 
- and a completion buffer to ensure precise exceptions.

POWER architecture: Performance Optimized With Enhanced RISC 

寄存器：
- 32 general purpose register (GPR)
- 32 floating point register (FPR)
- 32-bits Condition Register (CR) which can be split into 8 4-bits field(CRF)
- Count Register (CTR) and Link Register (LR) for branch instr.
- integer exception register (XER)
- a floating-point status and control register (FPSCR)

uses six parallel execution units: 
- two simple (single-cycle) integer units, 
- one complex (multicycle) integer unit, 
- one floating-point unit (three stages), 
- one load/store unit (two stages), 
- and a branch unit

5个stage, namely 
- the fetch, 
- dispatch, 
- execute, 
- complete, 
- writeback stages.

## Pipeline Brief
![[Pasted image 20230603132933.png]]
#### Fetch
4-issue
preliminary branch prediction is made using the branch target address cache(BTAC)

#### Instruction Buffer
8-entry

#### Dispatch
eight integer register rename buffers, eight floating-point register rename buffers, and 16 condition register field rename buffers
在dispatch阶段，branch的resolution会被立刻处理，但如果该resolution没有立刻有结果，会用Branch Prediction Table （BHT）来预测。如果BHT结果和之前的BTAC结果不同，则使用BHT的结果refetch

#### Reservation Station
每个eu都有自己的reservation station。rs里的每个指令都会等它的source operand到齐

#### Execution Units
2-stages LSU
3-stages FPU
执行结束后送到destination rename buffers然后forward给等待的指令

#### Completion Buffer
16-entry，类似ROB。在dispatch时被allocate

#### Complete Stage
最多每次retire4条指令。

#### WriteBack Stage
就wb stage

# Fetch
## Branch Prediction
在两个phase进行：
- Fetch阶段，通过BTAC
- Dispatch阶段，通过BHT，更精确

BHT是一个2048-entry的直接映射2-bit history table。
BTAC 256-entry 2-way。只存taken的指令的目的地址。BTAC只负责预测无条件跳转和pc relative跳转。不负责link register跳转和count register跳转。因为BTAC只存这两种的值，而这两种是立即数，因此BTAC存的目的地址一定正确。
link register跳转由单独的link register stack负责记录，其可以记录跳转地址和return地址。
BTAChit就意味着token，否则是not token。BTAC没有用到history，因此BTAC错误但BHT正确的情况比较多。可以看到下表，这种情况最高也不超过0.5%。
![[Pasted image 20230603174344.png]]
## Fetching and Speculation
两种影响fetch bandwidth的可能：
1. miss prediction
2. icache miss
下图说明了百分之多少的cycle是由于上面两种原因miss的。可以看出来大部分的fetch cycle miss来自于misprediction。
![[Pasted image 20230603174650.png]]

# Dispatch
顺序dispatch
## Instruction Buffer
下图左是instruction buffer。前两种是int指令，后两种是float指令。可以看到float指令的空和满都比较少。
下图右是completion buffer。float指令占用率显著高一些。
![[Pasted image 20230603181658.png]]

## Dispatch Stall
有以下几种stall的可能：
- Serialization Constraints. 类似aqrl那种，需要前面的指令都完成才能dispatch。这种情况不多见
- Branch Wait for mtspr (move to special purpose register). 有些branch需要访问count reg(CTR)，此时前面的mtspr指令会对CTR进行操作，branch会卡主
- Reservation Station Saturation. 
- Rename Buffer Saturation
- Completion Buffer Saturation
- Another Dispatched to Same Unit.

## Dispatch Effectiveness
下图是主要的几个buffer的utilization:
![[Pasted image 20230603200452.png]]
dispatch stall的频率和原因。reservation stall和rename buffer stall比较常见，资源冲突也是。
![[Pasted image 20230603200715.png]]
下图是各个阶段并行数的概率。至少有一半的时间有指令被dispatch。
![[Pasted image 20230603202114.png]]

# Execution 
## Issuing Stalls
- Out of Order Disallowed.
- Serialization Constraints. 一些特殊指令。原子指令是一种。
- Waiting for Source Operand
- Waiting for Execution Unit
issue stall的占比
![[Pasted image 20230603212232.png]]

## Execution Parallelism
参见上上图的b、c列。

## Execution Latency
下图描述了不同种类的指令的平均延时，这个延时等于其在Reservation Station中的等待时间+执行延时。
![[Pasted image 20230603213250.png]]
# Instruction Completion
## Completion Parallelism
见上上上图的d列。
The integer benchmarks with their more consistent execution latencies usually have one instruction completed per cycle

## Cache affect
2bank, pseudo-2-port D cache。但只支持一个ld和一个st同时进行。这违反了程序特点：load更多
做了non-blocking
store in order, load bypass store. POWERPC支持的relaxed模型
不支持store-load forwarding
可见到 alias问题其实没有很严重。
![[Pasted image 20230603220149.png]]
# Conclusion
BPU表现不错，fp正确率在94-99；但int正确率在90上下。需要更聪明的算法。
LSU是瓶颈
过少的FPU也是瓶颈。

和[[Intel P6]]的对比：分布式reservation station还是单独的reservation station

一个很有趣的点：
IPC重要还是frequency重要？
powerpc系列在提ipc，但alpha在提频率。
![[Pasted image 20230603222245.png]]