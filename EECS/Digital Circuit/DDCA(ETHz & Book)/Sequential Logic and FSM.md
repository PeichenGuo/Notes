# Finite State Machine
一个dicrete-time模型
拥有所有可能是状态和transition

五个元素：
1. A finite number of states
2. A finite number of inputs
3. A finite number of outputs
4. An explicit specification of all transition
5. An explicit specification of what determines each external output value

三个组成部分：
1. next state logic
2. state reg
3. output logic
![[Pasted image 20230627133730.png]]


## D Flip-Flop (DFF)
![[Pasted image 20230627134502.png]]
about ==40+== transistors 

## FSM
![[Pasted image 20230627161318.png]]
区别在于mealy的output和input有关，moore没有


三种state encode方式：
1. fully encode。每个都排上。dff最少
2. one-hot。dff最多，但设计简单、next-state logic简单
3. output encode： 根据输出encode。类似于one-hot和fully之间的状态。


