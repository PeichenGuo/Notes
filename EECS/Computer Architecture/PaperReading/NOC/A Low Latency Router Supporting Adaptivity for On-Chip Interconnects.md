---
Author: "Jongman Kim"
Year: 2005
Journel/Conference: "DAC"
Summary: "BlaBlaBla"
Rate: 5
Question: "None"
Eureka: "None"
---
### Abstract


### Motivation


### Solution
添加了一个pre-selection（PS）阶段。可以提前选Physical Channel(PC)
![[Pasted image 20230829121955.png]]
使用3-bitVCID

每个PC有三个VC。比如NE，一个包到NE来，就不可能要从N和E来，只可能从S、W或者PE来，因此只需要三个VC。同时这个是deadlock free的

到local PE的flit不走crossbar，直接eject，因此crossbar不是5x5而是4x4
![[Pasted image 20230829122531.png]]

PS function
会根据堵塞信息维护一个direction vector table
### Evaluation


### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
