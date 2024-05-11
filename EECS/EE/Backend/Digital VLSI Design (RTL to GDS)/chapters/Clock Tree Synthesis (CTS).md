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
CTS目标：
- minimize skew
- meet target insertion delay

## technology trend:
Timing:
- higher clock freq
- better jitter
- noise increase: power supply noise and temperature gradients
New interconnection material:
- 铜导线（之前是铝）：更低RC，更好slew和更小skew
- low-k dielectric（低介电常数材料）：介电材料指的是导线中的绝缘层，传统用的是二氧化硅，可以减少cap，从而减少Clock power
Power：
- 更多pipeline：更多reg，clk更多load cap
- 更大chip：更长导线
- 更复杂：更多clk
- dynamic logic：更多clk

## 不同的CTS方法：
![[Pasted image 20240511133003.png]]
### Clock Tree
naive实现：让wire差不多长来balance RC。很坏的方式，因为RC会较大而且影响SI
实际方式：buffered tree。布线更短，rc更低，buffer更多会让每个buffer drive的距离更短，slew更大，insertion delay也更小。

H tree：长得像H的一个tree，可以让wire长度到每个sink都一样。理论上完美解决方案。但实际上FF不会分布的那么规律均匀，因此不是很能实现。
![[Pasted image 20240511133604.png]]
standard cts appoarch：
不要求那么balance，因为ff具有局部性：有逻辑联系的ff通常离得较近，会在同一个leaf上，其上的skew和jitter都比较一致。
![[Pasted image 20240511133741.png]]
### Clock Grid
更小的skew
巨大的driver来drive一个mesh
但power和布线开销非常大，可能都会占用一层metal
![[Pasted image 20240511134201.png]]
DEC用到的一些clock grid设计
![[Pasted image 20240511135828.png]]
### Clock Spines
tree和grid的中间体。htree的叶子结点是一个spine，互联的。
![[Pasted image 20240511140004.png]]
### Summary
 ![[Pasted image 20240511140149.png]]
# Clock Concurrent Optimization （CCopt)
active skew management (deskewing)
分出不同的skew region，不同的region可以是不同的tree。因此每个tree复杂度都变低了
![[Pasted image 20240511143507.png]]

为什么minimum skew？minimum skew真的重要吗？
实际目标是meeting timing和DRV。minimum skew只是为了在post cts后可以更好的ta
因此我们可以在cts的时候只考虑timing而非套一层考虑skew，这种方式是CCopt

CCopt的方法论：
- build the tree，让他满足DRV
- 然后针对所有timing violation，fix it
相比之前的build a perfect tree，CCopt更像哪里有问题修哪里。这样可以避免在很多不关键的地方对skew进行优化。这样可以有更少delay，更少buffer，更少的peak current（因为cell 少了）。


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

# Clock Routing and Clock Tree Analysis
由于clk routing对正确性非常重要（glitch会导致有严重错误，过快slew对旁边的net有影响，过慢的slew导致time violation），因此clk会在cts阶段就进行routing，还会用higher、thicker的metals，并且会在clk周围围一圈vdd和gnd形成保护（防止串扰），还会加解耦电容。
![[Pasted image 20240511144517.png]]

how to route?
special routing rules （Innovus，NDR）
![[Pasted image 20240511144743.png]]

# Clock Generation
最简单的办法，把很多inverter串成环
还可以用off chip 晶体来生成clk，但最高100mhz
因此现在一般是用pll
chip之间沟通会顺便把clk也发过去作为reference
![[Pasted image 20240511161550.png]]
PLL：
phase detector可以发现phase difference，然后loop filter可以把发现的difference转换为control signal，control logic输入voltage-controlled oscillator VCO产生新的clock 信号。

# Clock Domain Crossing (CDC)
不同时钟域之间不能直接沟通，毕竟clk都不一样，这就需要CDC技术
metastability: 亚稳态。cdc面临的最大问题。亚稳态发生的概率其实很高。
cdc还可能导致data丢失和incoherence
![[Pasted image 20240511171234.png]]
可以通过synchronizer解决。synchronizer是两个相连的ff。这可以解决亚稳态，因为瞬间的亚稳态会被挡在两个ff之间。但也是有可能发生错误的，但概率极小。
![[Pasted image 20240511171246.png]]
data loss：
- slow到fast不会丢data
- fast到slow可以让fast多hold几个cycle
data coherence就比较麻烦了，可能需要handshake protocol之类的。
