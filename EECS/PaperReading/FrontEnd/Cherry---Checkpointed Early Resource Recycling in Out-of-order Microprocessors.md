---
Name: "Cherry: Checkpointed Early Resource Recycling in Out-of-order Microprocessors"
Author: "J.F. Martinez, Josep Torrellas"
Year: 2002
Journel/Conference: "MICRO"
Summary: "提前回收部分已经完成的指令的资源。在已经执行过的instr上打checkpoint，当中断异常的时候回滚到哪些checkpoint"
Rate: 4
---
### Abstract
Cherry relies on state checkpointing and rollback to service exceptions for instructions whose resources have been recycled. Cherry leverages the ROB to (1) not require in-order execution as a fallback mechanism, (2) allow memory replay traps and branch mispredictions without rolling back to the Cherry checkpoint, and (3) quickly fall back to conventional out-of-order execution without rolling back to the checkpoint or flushing the pipeline
cherry这个东西不需要io execution来回滚，对于memory replay和branch missprediction而言是不需要回滚的，并且可以快速地回到正常ooo执行（退出cherry mode）。

cherry有针对三种东西的提前回收，prf stq和ldq。

cherry需要checkpoint回滚的原因是：有时候回收资源但没retire的指令出现异常了，这时候就需要重新执行到这条异常然后再处理这个异常。

### Motivation
正常rob需要顺序commit，然后只有在retire的时候才会回收资源。这是很浪费的。
cherry就decouple了回收和retirement。
### Solution
Point of No Return. 
有四种情况会造成提前回收出问题：
- 分支预测失败
- memory replay。这个大概就是瞎jb发ld，发完st后发现后面有个ld已经发完了，这时候需要重新发这个取到了错误数据的ld。
- exception
- interrupt

前两个其实好办，因为这两个相对常见，没必要提前回收这两种指令所用的资源。
因此只需要针对后两种情况做checkpoint recovery。

PNR就是能收到一个能受到misprediction和memory replay影响的指令。比PNR老的指令能recycle，比PNR新的不行。

注意三种指令$U_B, U_L, U_S$，代表最老的还没有执行完的Branch，LD和ST。
简单来说，$PNR = Oldest\{U_B, U_L, U_S\}$

但实际上不是所有情况都需要满足PNR要求。

ld单核下只需要比us老，多核下要比ul和us老。因为需要保存更新的ld来看load-load reply trap（多核限定） 和 load-store replay trap。 branch就不用管，不会影响任何事情。==啥是ld-ld replay trap==

st需要满足pnr。很好理解。

prf单核满足us ub，多核需要满足pnr。us和ul的解释和ldq类似，ub是因为要等mispredict被处理。显而易见。
### Evaluation
![[Pasted image 20230413221731.png]]
![[Pasted image 20230413221829.png]]
base2是lsq/stq/prf各加32，base3是各加64，base4是各加96，limit是无限大。
可以看出这玩意基本增长落在base2和base3之间，还是蛮不错的。

