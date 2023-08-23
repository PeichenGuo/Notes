三个要点：
- 拓扑结构
- routing算法
- buffering和flow control

## Topology: 拓扑
bus只有一条线，全相连复杂度太高，


![[Pasted image 20230822180431.png]]
NOC主要处理的是cache miss和memory的操作

buffer可以增加带宽，但增加能耗、延时和面积
![[Pasted image 20230823143916.png]]

## BLESS: Bufferless router
![[Pasted image 20230823150504.png]]
performance减小很少（4%），energy减少很多（40%）
大部分情况下network不是很heavily loaded
![[Pasted image 20230823154750.png]]
BLESS或许是未来的方向

