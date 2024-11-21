---
Author: "[[刘雷波]]"
Year: 2023
Journel/Conference: ISCA
Summary: BlaBlaBla
Rate: 3
Question: None
Eureka: None
---
### Authors

### Abstract


### Motivation
现有的ooo commit开销太大，提出non-collapsible

### Solution
#### IQ age matrix
![[Pasted image 20231016150522.png]]
每行每列都对应一个指令，intersection为1代表竖着的指令老于横着的指令。当一个指令进入matrix的时候，把row置1，把列置0。
当指令wakeup的时候，bit置1
ready的指令把row vector和bid按位与（又ready又老于这个指令的指令个数），如果这个数小于Issue Window，则这个指令可以issue。

#### ROB matrix
![[Pasted image 20231016152642.png]]
和IQ类似。SPEC vector代表这条指令是否处于speculative状态。row和spec vec按位与然后再与，如果是1，代表有一条比他老的指令还处于speculative状态，这条不能commit。

#### 指令dependency matrix
![[Pasted image 20231016153235.png]]
row在dispatch的时候，比他老的指令如果可能有exception，则置1
col在确保无法有exception后，置0
如果row都是0，则可以commit
#### LQ/SQ matrix
![[Pasted image 20231016153532.png]]
ld进入的时候，把更老的unresolved的st所在的格子置1
st进入的时候，把col置1；地址确认且无冲突后，置0
如果ld的row都是0，则是non-speculative的ld

#### lockdown matrix
![[Pasted image 20231016155917.png]]
这个是用来防止ld-ld的乱序执行的
load commit的时候，如果前面有load没开始，该row bit置1。
load perform的时候col置0
如果row全是0的时候，说明该ld前没有ld还没执行

#### wakeup matrix
![[Pasted image 20231016162429.png]]
dispatch的时候把其前置的instr置1
issue的时候把col置0
如果全是0，则wake up了


#### 整体架构
![[Pasted image 20231016162644.png]]
![[Pasted image 20231016163021.png]]
### Evaluation

==没看==
### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
