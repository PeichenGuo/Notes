---
Author: "Teresa Monreal, Antonio González, José González"
Year: 1998
Journel/Conference: "HPCA"
Summary: "利用virtual physical register将prf存数据和用来wake up的id解耦合，从而在wb阶段做allocation的办法"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
提出了Virtual-Physical Registers，可以在issue或者wb阶段进行allocation，从而减少physical register的占用时间。后面证明了wb阶段更好。
### Motivation
physical register的占用时间过长。主要有两个点产生waste：
1. decode但还没执行。这段时间preg被分配但没有被用到
2. 所有用到preg的指令都commit了，但下一个写该logic reg的指令还没有commit。在传统方法中，需要在overwrite preg的指令commit的时候再retire掉旧值所在的preg。

后者可以用计数的方式，数pending consumer的数量。
本文主要解决的是第一个问题。
### Solution
##### 两个table
general map table (GMT)
用logic reg做index，每个logic reg对应一个。记录三个东西：
1. VP reg： Virtual Physical reg的序号
2. P reg：Physical reg的序号
3. valid： 如果是1，则p reg有效

physical map table (PMT).
记录VP reg到P reg的mapping。可以是CAM，用P reg的大小；也可以用VP reg的数量。

decode阶段如果GMT中Lreg对应的valid是0，也就是没有preg映射，就先分一个VP Reg，然后执行后再分一个P reg；如果valid是1，直接分preg。
非常好理解的东西。

##### deadlock
主要难点在解决deadlock。如果执行完成后没有preg free了，且后面占据了preg的指令要等这条指令先commit，就会造成死锁。
本文通过一个NRR机制来解决问题。NRR就是number of reserved registers (NRR)。相当于预留了一定数量的preg给较老的指令，比如留32个寄存器相当于确保32条老指令可以获得preg。
此时如果一条指令结束后，发现剩余的preg不够老指令使用，则这条新指令重新送回iq重发。这样就解决了deadlock。

##### issue or wb
如果在issue而非execution阶段结束时allocate preg就不会产生deadlock问题，从而避免re-execution。但后面的性能测试表明了，在iss阶段搞allocation性能不好


### Evaluation
![[Pasted image 20230414164150.png]]
在int测试中，平均下来NRR=32的时候好，但好的不多；在fp时NRR=32时基本上完胜。
fp执行时间长，delay allocation收益很大。
![[Pasted image 20230414164332.png]]
这个可以看出wb完爆issue阶段allocation。

### Unsolved Question

### Related Works
#### Later Works
[[Dynamic Register Renaming Through Virtual-Physical Registers]]
[[Delaying Physical Register Allocation Through Virtual-Physical Registers]]