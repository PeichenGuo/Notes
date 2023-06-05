# Introduction
三个关键组件：
in-order frontend
ooo middle
in-order retirement 
![[Pasted image 20230604215443.png]]
- 从p6开始，会产生μop
- deeply pipelined。多个pipeline拼在一起组成的deep pipeline
- a speculative, out-of-order engine.
- 

- Extract useful work via deep speculation on the front end (instruction cache, decoder, and register renaming).
- Provide enough temporary storage that a lot of work can be “kept in the air.”
- Allow instructions that are ready to execute to pass others that are not (in the out-of-order middle section).
- Include enough memory bandwidth to keep up with all the work in progress.

# Pipeline
![[Pasted image 20230604230832.png]]
11： BTB做预测
12：initiate I$ fetch
13：访问I$
14:  结束访问i$,数据给decoder
15、16：decode 生成μop
17：如果此时检查到一个BTB没有记录的branch（比如第一次见到的branch）flush流水线然后重新取。17因为算是一个bonus，和后面的flow没有顺序关系，因此17可以和21并行

20：decode 结束
21：访问RAT(Register Alias Table)，准备写reservation station
22：写reservation station

p6的reservation station是单独一个很大的，在以下情况下才会发
- The μop must have all its operands available; 
- the execution unit (EU) needed by that μop must be available; 
- a writeback bus must be ready in the cycle in which the EU willcomplete that pop’s execution; 
- 没有更重要的指令等着发。and the RS must not have other μops that it thinks (for whatever reason) are more important to overall performance than the pop under discussion

31、32：RS需要两个cycle发现并安排新进来的μop
33-3x：执行。

对于延时已知的指令，比如int指令。dispatch的时候可以知道bus scheduling。dispatch时bus一定可写，因此33时就可以安排bus write。
82：准备写bus
83：写bus

memory比较复杂。33在agu获得地址后，就可以进行dcache访问：
42、43：访问dcache
但dcache可能miss，此时就需要suspend这些op。MOB负责管理suspended memory op。当下层cache refill的时候，mob负责redispatch
40：refill wakeup mob
41：redispatch memory op

retirement比较好玩。不能retire μop，只能等着凑一整个指令再retire。在decode阶段，就会在μop上标记是不是指令开头或结尾，这样比较方便retire。

# Frontend
frontend和ooo core各跑各的。
比较特殊的是他的misprediction recover机制。先flush frontend，等backend跑到bad branch后再flush backend
![[Pasted image 20230605141611.png]]
![[Pasted image 20230605142621.png]]
上图是frontend的pipeline

### Branch Prediction
p6用的是2-level的adaptive training algorithm
The first level is the history of the branches. 
The second level is the branch behavior for a specific pattern of branch history

### Register Alias Table
As µops are presented to the RAT, their logical sources and destination are mapped to the corresponding physical ROB addresses where the data are found