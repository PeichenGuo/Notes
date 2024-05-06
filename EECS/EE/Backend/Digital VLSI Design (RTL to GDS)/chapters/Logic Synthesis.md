---
Chapter: 3
---
# Introduction
三种输入：
- RTL
- standard cell library
- design constraints

一个输出：netlist

 目标：
 - minimize area
 - maximize performance
 - minimize power

basic flow：
- syntax analysis: 检查rtl语法
- library definition：设置工艺
- Elaboration and Binding: 将RTL转换为boolean structure，绑定leaf cell
- constraint definition：设置SDC
- pre-mapping optimization
- mapping：映射到具体工艺
- post-mapping optimization


# Compilation (syntax analysis)
skip

# Library definition
## Cell 参数
![[Pasted image 20240506163757.png]]
cell height，cell width 尺寸
voltage rails：图中黄色覆盖vdd和gnd的区域，相当于电源线
well definition: 图中绿色的，是n well
pin placement：图中蓝色的是放置pin的位置
PR boundary：紫色框。cell的边界，注意voltage rail只覆盖了一半，因为另一半是其他cell用的。voltage rail会被两个对称的cell共享
Metal layer：metal怎么布线。一般只占据M1
## Cell 类型
![[Pasted image 20240506164648.png]]
同一个cell对应不同fan out有不同模型
Fan-in cmos不适合高fanin 因此大部分cell的fan in都<=4
ECO cell： engineering change order cell
clock cell：可以balance rise fall delay来minimize clock skew和用来做CTS的
delay cell：用来fix hold violation的
level buffer：不同Vsup之间沟通用的buffer
### Multiple Drive Strengths and VTs（V threshold)
![[Pasted image 20240506164948.png]]

### Clock cell
![[Pasted image 20240506165406.png]]
大部分standard cell为了更好的性能设计，但不balance，会导致fall rise的delay不一样，然后导致不好的skew
clock cell性能一般不好但balance
一般而言，clk不需要逻辑，只需要buffer和inverter

### Sequential
![[Pasted image 20240506165526.png]]

### Level Shifter
![[Pasted image 20240506165620.png]]

### Filler and Tap Cells
![[Pasted image 20240506165940.png]]
Filler是填充在有用cell之间的，用dummy cell填充，来获得更好性能

### Engineering Change Order ECO Cells
![[Pasted image 20240506170926.png]]

## Standard Cell Library中有什么？
![[Pasted image 20240506171634.png]]
- behavioral level: vhdl, vital（也是一种hdl，现在不常见）
- physical level：GDSII，做drc lvs，但比较复杂；LEF，做PnR，RC提取，相比GDSII更抽象
- transistor：Spice/Spectre 做LVS和transistor level simulation
- Timing/power: liberty，通常是.lib或.db
- Power grid: 用来做ir drop

## Library Exchange Format (LEF)
![[Pasted image 20240506172233.png]]
有以下内容：
- pin information和layer 信息，没有poly diffusion这些内容
- outline
- blockage

### Technology LEF
![[Pasted image 20240506172858.png]]
用来描述tech的：
- layer：都有哪些，性质是什么
- site：最小的单元，类似lego的一个凸起。用来定位
- via
- unit
- grid
![[Pasted image 20240506173031.png]]
用Track来表示tech的宽度。比如vdd和gnd之间有8个距离可以放m1，就叫8-track lib

## Liberty Timing Model (.lib)
![[Pasted image 20240506184307.png]]
跑spice太笨，建一个简单的模型比较方便
对于每个timing arc，计算：
- $t_{pd}$
- $t_{rise}$ $t_{fall}$
输入：net transition和output load capacitance
实际上这个model根据case不同有较大差异

![[Pasted image 20240506184713.png]]
### Non-Linear Delay Model (NLDM)
Non-Linear Delay Model (NLDM):一个table，行是input transition（也就是input skew），列是output load cap，可以查到对应的delay。也叫delay template。用表上数据建模可以得到任意情况的delay。
但这种model在130之上不准，有新的办法。
![[Pasted image 20240506185132.png]]
sky130的13x8的delay template
```
...
cell_fall ("delay_template13x8") {
                    index_1("0.0100000000, 0.0173500000, 0.0260200000, 0.0390300000, 0.0585500000, 0.0878200000, 0.1317200000, 0.1975700000, 0.2963400000, 0.4444900000, 0.6667000000, 1.0000000000, 1.5000000000");
                    index_2("0.0000000000, 0.0052800000, 0.0105700000, 0.0211400000, 0.0422700000, 0.0845400000, 0.1690800000, 0.3381500000");
                    values("0.1443000000, 0.1724500000, 0.1931800000, 0.2276400000, 0.2863300000, 0.3912300000, 0.5900300000, 0.9828300000", \
                        "0.1449400000, 0.1730700000, 0.1937900000, 0.2282600000, 0.2869400000, 0.3918100000, 0.5906300000, 0.9834000000", \
                        "0.1456800000, 0.1738000000, 0.1945200000, 0.2289800000, 0.2876700000, 0.3926000000, 0.5913000000, 0.9845200000", \
                        "0.1467200000, 0.1748300000, 0.1955400000, 0.2300000000, 0.2886800000, 0.3935900000, 0.5924000000, 0.9851800000", \
                        "0.1480900000, 0.1761900000, 0.1968900000, 0.2313100000, 0.2900400000, 0.3949400000, 0.5937600000, 0.9864100000", \
                        "0.1495700000, 0.1776600000, 0.1983500000, 0.2328000000, 0.2915400000, 0.3964200000, 0.5952700000, 0.9880200000", \
                        "0.1515300000, 0.1795900000, 0.2002500000, 0.2346900000, 0.2934500000, 0.3984200000, 0.5970700000, 0.9903300000", \
                        "0.1555900000, 0.1841100000, 0.2049000000, 0.2394400000, 0.2982600000, 0.4032600000, 0.6020000000, 0.9949600000", \
                        "0.1662300000, 0.1952100000, 0.2164300000, 0.2515600000, 0.3109300000, 0.4161900000, 0.6149800000, 1.0080500000", \
                        "0.1891200000, 0.2188200000, 0.2404500000, 0.2761100000, 0.3362100000, 0.4422500000, 0.6413900000, 1.0341900000", \
                        "0.2263900000, 0.2576000000, 0.2801900000, 0.3171200000, 0.3785600000, 0.4858200000, 0.6855200000, 1.0785200000", \
                        "0.2801700000, 0.3137700000, 0.3380700000, 0.3775700000, 0.4420700000, 0.5522700000, 0.7535700000, 1.1464700000", \
                        "0.3554000000, 0.3921000000, 0.4187000000, 0.4619000000, 0.5319000000, 0.6487000000, 0.8551000000, 1.2492000000");
}
...
```

### Wire Load Models (WLM)
estimate RC of a net 但不好使。根据fan out查表：
![[Pasted image 20240506185801.png]]
只考虑fanout没考虑wire length

# Boolean Minimization (Elaboration and Binding)
![[Pasted image 20240506191118.png]]
根据rtl可以得到一个FSM，
sm可以推断到register+comb的一个逻辑电路，
这个电路有真值表，从x1 x2到f(x1,x2)的映射，
然后根据真值表写出二级逻辑表达式F1=x1x2 + x2x3'

==skip了==

# Constraint Definition
skip

# Technology Mapping
skip

# Timing Optimization
skip

# Timing Analysis
## Intro
### FF三个关键parameter
- $t_{cq}$：clk到q的延迟，一般就是ff的传播延迟
- $t_{su}$: setup delay
- $t_hd$：hold delay
### Timing Constraint
max delay：时间不够传到下一个reg，即传播延迟 >时钟周期-setup时间
min delay：同一个上升沿穿透多个reg，即传播延迟<hold时间

## STA
优点：
- fast：比timing-driven, gate-level simulation都快
- exhaustive：穷举，所有情况都会被考虑
- vector generation not required：不需要考虑input vector
缺点：
- 不管functionality
- 需要define timing requirement：如果设置错了，STA也不会对
