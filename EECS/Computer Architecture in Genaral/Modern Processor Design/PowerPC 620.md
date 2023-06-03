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
BTAC 256-entry 2-way。只存taken的指令的目的地址。BTAC只负责预测无条件跳转和pc relative跳转。不负责link register跳转和count register跳转。
link register跳转由单独的link register stack负责记录，其可以记录跳转地址和return地址。