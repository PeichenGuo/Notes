---
Chapter: 6
---
# Intro
timing constraints:
![[Pasted image 20240509201248.png]]
tcq是ffc到q的延迟，tlogic是组合逻辑延迟，tsetup和thold是ff的setup时间
delta skew和margin

## 对timing的影响
skew：不同reg间clock arrival time的差别
jitter：同一个reg不同cycle间的差异
slew：transition time（trise/tfall)
insertion delay：source到ff的delay，一般讲average insertion delay
![[Pasted image 20240509201815.png]]
skew/jitter来源：
- generation：pll生成的时候就有
- distribution network：在传播中因为各种各样的原因导致clk信号有不同延迟：buffer，device差异，耦合效应
- environment variation：温度，power supply的差异
![[Pasted image 20240510173712.png]]
positive skew指的是capture clock比launch clock慢。
对于max delay而言，慢了相当于时间变多了，因此加在左式；jitter则是时间变少了，所以是减在左式
对于min delay而言，慢了相当于始终边沿更慢到达，hold time变长，因此加在右面。

## 对power的影响
什么是Ceff（有效电容）
可以分为clock的和非clock的。其中alpha是活动因子，指多少percent在活跃。对于clk是100%。因此Cclk非常重要，因为100%影响，实际上clk的power占很大。
![[Pasted image 20240510174331.png]]

## 对signal integrity的影响
slew不能太快：功耗太大，对其他电路有影响
slew也不能太慢：driver太弱会有noise，而且reg functionality会差（各种delay会变长）。最好让rise和fall占clock period 10%-20%
![[Pasted image 20240510174754.png]]

## 对area的影响
PLL和clk network其实挺大的，比如intel itanium就占据了4%的M4/M5

# Clock Distribution
#skip

# Clock Concurrent Optimization （CCopt)
#skip 

# CTS in EDA
之前placement已经摆好，所有clk sink，clk pin都已知。
要给clk net做buffer，来meet DRV(Design Rule Violation)，比如max fanout，max capacitance，max transition，max length，和meet clocking goals，比如min skew和 min insertion delay

clock tree 组成：
- start point：clock generator
- leaf：clock sink
- buffer/inverter：配平的
- special logic：比如做clock gating的mux，分频的clock divider

skew group：一个group内skew接近。
![[Pasted image 20240510183109.png]]
start pin：产生时钟的地方，比如clk input pin，clock gate output pin，pll output pin
stop pin：clk tree的终止点
Ignore pin： 不算在任何skew group内的pin
exclude pin：类似ignore pin，但clk不会buffer up to an exclude pin #q 没懂
through pin：一种stop pin，但我们希望clk从其中传播过去，比如clock gate的input
