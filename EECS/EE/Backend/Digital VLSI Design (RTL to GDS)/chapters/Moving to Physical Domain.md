---
Chapter: 4
---
# Intro
从这步开始进入backend
第一步是floorplan，在大的方面上做planning
然后是power planning，摆vdd gnd
然后是placement，考虑congestion和timing
接着是CTS
最后是routing，考虑DRC,timing, noise
如下图的例子。
![[Pasted image 20240508132542.png]]
这部分的输入输出
![[Pasted image 20240508132711.png]]
# Multiple Voltage Domain
手动划分不同的power domain，然后把不同的模块放入不同的domain
![[Pasted image 20240508133235.png]]
在不同的pd之间需要放置level shifter来在不同的pd间传输data
Power gating也经常被用到。相当于不同pd的开关。这个gate需要做的很大来减少电阻。
![[Pasted image 20240508133723.png]]
cadence用.cpf synopsys用upf
# FloorPlanning
## Intro
![[Pasted image 20240508142145.png]]

![[Pasted image 20240508142549.png]]
在floor planning之后就可以做standard cell placement了
## IO Ring
core周围一圈的io叫io ring。不仅要提供data，还要供电
io scale要低于moore's law

在早期是由core大小决定chip size，但现在很多情况下是io决定chip大小。
![[Pasted image 20240508143231.png]]
## Utilization
利用率指的是有效cell面积，一般的starting utilization在70%左右。高利用率很难close design，比如routing congestion会变大。有些cell的local congestion就是比较大，比如mux，这部分不容易提高利用率。

## Uniquifying the Netlist
所有sub-module只被ref一次。如果一个module有多个ref，就很难根据不同的引用locality来优化sub-module。如下图，图中amod中由一个buffer u1，在bmod中被ref两次，此时m1的u1无法在不影响m2u1的情况下被优化。
解决方式很dumb，就是copy改个名
![[Pasted image 20240508144110.png]]

## Hard Macro Placement
一般倾向于place在边角，因为placement算法在大的矩形平面上表现更好，要给自动布线留出足够大的整齐空间

## Placement Regions
指导placement算法：garbage in garbage out。
不同的guide等级：
soft guide：尝试把一些cell cluster在一起
guide：尝试把cell放在一个指定的area
region：必须把cell放在指定区域，其他cell也可以放在该区域
fence：必须把cell放在指定区域，其他cell不可以放在该区域（不常用）

## Placement Blockages and Halos
Placement Blockages / Halos：不应该被摆放的地方
也有不同等级：
- hard blockage：不能被摆放
- soft blockage：standard cell不能place，但优化时可以摆放
- partial blockage：lower utilization的area
- halo：macro周围一圈的清空区域
- routing blockage：不允许routing （不常用）

## Guidelines For a Good Floorplan
给standard cell area留一个大的矩形空间
RAM要放在边角
要留出大的routing空间，比如留给PLL产生的clk布线空间
pin 不要放在边角和narrow channel，不好布线
避免constrictive channel（如图），会有congestion
用blockage来给pin留出routing空间
![[Pasted image 20240508145944.png]]

# Hierarchical Design
巨大的design需要hierarchical的
缺点：
- time closure 更难
- planning更难
Time Budget： io constraint留下的冗余 
Pin assignment：macro的pin assignment要注意，corner上的pin可能不好routing。要给pin guide来布局
feedthrough：一些pin可能会穿过一些macro链接远处的模块，这部分也要提前考虑。比如IOpin要连B模块，但要经过A模块，可以绕过A也可以穿过A，后者叫feedthrough
![[Pasted image 20240509140419.png]]

# Power Planning
## Power Consumption and Reliability
power planning考虑dynamic power，static power 和 floorplan和grid design
处理两种问题：
- average or instantaneous power problem: IR drop, voltage Droop
- power density problem in the long run: electromigration(EM)
![[Pasted image 20240509141629.png]]
### IR Drop
由于wire有电阻，v=ir，会分摊一部分电压，导致supply voltage突然变低。
可以通过构建power grid的resistance matrix来模拟，从而寻找ir drop
![[Pasted image 20240509142039.png]]
一般通过hotspot方式看看ir drop问题
![[Pasted image 20240509143035.png]]
### EM
电流密度很大的情况下，金属原子可能会跟随电流迁移，导致metal wire破损
可以在power distribution解决
power line更宽，ir drop更少，em更少，dynamic drop更少，但开销更大，面积更大
![[Pasted image 20240509142719.png]]

## Power and Ground Routing
会使用PG mesh,让PG source到达destination间有多条路径，从而可以降低resistance
decoupling capacitors：PG之间的电容，防止电压波动
![[Pasted image 20240509143537.png]]
在走routing的时候主要还是做trade-off：em/ir drop和routing resource。
