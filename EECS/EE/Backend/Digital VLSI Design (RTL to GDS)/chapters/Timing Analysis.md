---
Chapter: 3
---

# Intro
## FF三个关键parameter
- $t_{cq}$：clk到q的延迟，一般就是ff的传播延迟
- $t_{su}$: setup delay
- $t_hd$：hold delay
## Timing Constraint
max delay：时间不够传到下一个reg，即传播延迟 >时钟周期-setup时间
min delay：同一个上升沿穿透多个reg，即传播延迟<hold时间

# STA
优点：
- fast：比timing-driven, gate-level simulation都快
- exhaustive：穷举，所有情况都会被考虑
- vector generation not required：不需要考虑input vector
缺点：
- 不管functionality
- 需要define timing requirement：如果设置错了，STA也不会对
- 只能做synchronous电路
![[Pasted image 20240507153758.png]]

![[Pasted image 20240507154125.png]]
vertices是wire，edegs是gates和pin（其实就是delay）。

![[Pasted image 20240507154330.png]]
at向后传播，是src到达这个点的最长路径；rat向前传播，是snk到达这个点的最长路径。slack是rat-at。
![[Pasted image 20240507154937.png]]
![[Pasted image 20240507154620.png]]
对于node n，at(n)的前序节点p的at加上edge(p,n)长度中的最大值，rat(n)就是后续节点s的rat减去edge(n,s)中的最小值。

# Design Constraint
## Timing Constraint
三种：
- clock def
- modeling  the world external to the chip
- timing exception
### Clock Definition
create_clock。
![[Pasted image 20240507172106.png]]
具体参考PrimeTime Workshop：[[Constraining multiple clocks]]

### IO constraint
![[Pasted image 20240507174509.png]]
对于reg2reg的情况，只知道clk就可以sta
但对于in2reg，reg2out，in2out的情况需要知道前一个或者后一个reg才能进行sta
因此需要io constraint来模拟未知的reg
![[Pasted image 20240507172638.png]]
也可以用virtual clock来代替io constraint 
设置input delay：从clk这个clock的rising edge后设置0.8ns的延迟到所有除了名字叫clk的inputs
```tcl
set_input_delay 0.8 -clock clk [remove_from_collection [all_inputs][get_ports clk]]
```
![[Pasted image 20240507174447.png]]
类似的，有set_max_delay。设置一个from/to pin的最长delay，从而给io留出充足时间，比如下面就是给出了clk以外的所有input设置了5ns的最长delay。这里的最长delay指的是从这些pin到下个寄存器的组合逻辑最长5ns
```tcl
set_max_delay 5 -from [remove_from_collection [all_inputs][get_ports clk]]
```
还需要通过set_dring_cell 来设置input driver，用set_load来设置output的load cap
![[Pasted image 20240507174537.png]]
### Timing Exception
set_false path（具体skip）
- 逻辑上不成立
- 不同clock domain
- 有些多周期comb逻辑，用set_multicycle_path
## Design Rule Violation
![[Pasted image 20240507182252.png]]

# Timing Reports
## Other checks
types:
- setup delay (max delay)
- hold delay (min delay)
- recovery
- removal
- clock gating
- min pulse width
- data-to-data
### Recovery, Removal and Min Pulse Width (MPW)
recovery和removal都是给asynchronous用的。
recovery是指 rst信号在deasserted之*后*距离下一个时钟上边沿之间最短的时间
removal是指  rst信号在deasserted之*前*距离上一个时钟上边沿之间最短的时间
MPW指的是rise/fall后需要保持stable的最短时间
![[Pasted image 20240507183037.png]]
### Clock Gating Check
所有除了inverter和buffer的，在clk路径上的组合逻辑都算clock gate
![[Pasted image 20240507183226.png]]

## report_timing
这里展示的是cadence的工具，和PT肯定有所差距
结果分为两个部分 header和launch path

### header
path#：按照worst顺序排序
rf指的是那里的逻辑是rising 还是 falling。由于rising和falling的delay大概率是不一样的，因此会分别计算

clock edge中，launch是0，capture是一个perios+launch，也就是6
source latency和net latency指的是clk在propagation中遇到的delay，具体会在CTS章节[[Clock Tree Synthesis (CTS)]]讲

setup指的是这个path到达的FF(endpoint ff)的setup time
uncertainty指的是clock uncertainty
Cppr：arrival-setup-jitter
launch clock是clk到达时间
data path：实际arrival time
slack：最后这个path的实际slack
![[Pasted image 20240507185321.png]]

### Launch Path
![[Pasted image 20240507185553.png]]
用full_clock来看clock propagation
```
report_timing -path_type full_clock
```

剩下在教具体用法了 skip

## Multi-Mode Multi-Corner (MMMC)
