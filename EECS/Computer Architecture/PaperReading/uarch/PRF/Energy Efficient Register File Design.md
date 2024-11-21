---
Author: Jessica Hui-Chun Tseng
Year: 1999
Journel/Conference: PhD thesis
Summary: BlaBlaBla
Rate: 3
Question: None
Eureka: None
---
## Authors

## Abstract
58% of fetched operands are discarded while 39% of register file updates are redundant.
所以regfile有很大空间
这篇文章主要讲了三种方式来减少reg access，一种方式来降低bitline switch activity，一种方式来降低bitline switching capacitance。具体而言是：
- Precise Read Control。不同的指令有不同的source operand，可以根据此来仅fetch有用的operand
- Bypass Skip。bypass后就不从reg里读了
- 单独的x0
- 改变storage cell。根据1和0的分布不确定性来修改storage cell从而减少bitline的transition
- Split Bitline。有些reg经常被用到，有些reg不常被用到。可以把这些分开，从而减少电容。
## Background
### evaluation
CMOS翻转的消耗是:
$$
{1\over2}.{C_{switch}}.{V_{dd}^2}
$$
其中switching cap是寄生电容。位线和地之间的电容、位线与其他金属层之间的电容，以及位线本身的电容都属于这类电容。每次位线切换时，这些电容都会充电或放电，从而消耗能量。
因此，CPU的block的消耗大概是：
$$
E = \sum_r ({1\over2}.f_r{C_{switch}}.{V_{dd}^2})
$$
其中fr是block的平均翻转频率。
因此可以看到，如果想减少regfile的功耗，可以减小翻转频率，或者减小电容。

### cells
#### 8T cell 
其实不是我们现在说的8t，而是读写gate分开的6t
![[Pasted image 20240828161011.png]]
每个cell还有一个column controller。其中分为两个部分。
上面是读逻辑。
在clk为0的时候，接通readbit的gate，从而给readbit充电。与此同时，一对mos管中下面的NMOS会接通，将两个mos管之间的电荷接地，从而清空。
然后在clk=1时，充电停止，readbit会读出1或者0的数据。如果读出0， 则接通中间一对MOS中的PMOS，从而让两个mos的drain为1，最后rs输出0。如果读出0，则rs自然为1。
下面是写逻辑。一目了然。
![[Pasted image 20240828162916.png]]
#### Bypass Network
用Transmission gate（传输门）搭mux。 
![[Pasted image 20240828163916.png]]
https://shininglionking.blogspot.com/2017/07/eda-transmission-gate-logic-switch.html
传输门是用来当开关的，因为mos管栅极的电压要低于一个阈值才会导通，这个数值还是不固定的，会收到source和drain电压的影响，因此pmos和nmos在switch电压和电流上都有差异。
1. P-MOS 藉由在 gate (閘極) 訊號通入低電壓 (VSS) 導通電流，並且 P-MOS 比較善於導通高電壓的訊號
2. N-MOS 藉由在 gate (閘極) 訊號通入高電壓 (VDD) 導通電流，並且 N-MOS 比較善於導通低電壓的訊號
因此使用了Transmission gate，把NMOS和PMOS并联，类似下图，从而可以在较广电压区间里控制开关。这两个MOS管还是同开同必的，总有一个可以流过电流（取决于V1和V2的大小）
![[Pasted image 20240828164620.png]]
#### latch
锁存器在电平为1的时候都是导通状态，dff是只有上升沿导通
![[Pasted image 20240828165551.png]]


## Motivation


## Solution
### Precise Read Control
![[Pasted image 20240828172512.png]]

### Bypass Skip
bypass主要是RAW hazard。因此检测到RAW hazard就可以关掉wordline了
![[Pasted image 20240828182722.png]]

### 单独x0
skip
### 改变storage cell
第一种办法，只有一个bitline，减少一个读。
![[Pasted image 20240828183245.png]]
但这种方式会造成不对称，从而导致面积变大。并且由于有可能两个读都hit到一个cell，上面那个inverter不得不变大来drive两个output。
解决方案可以是再加个mos管来提高drive能力
![[Pasted image 20240828183852.png]]

### Split Bitline
## Evaluation


## Unsolved Question


## Related Works
### Later Works

### Previous Works

### Similar Works
