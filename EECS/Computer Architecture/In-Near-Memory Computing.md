---
Author: Daichi Fujiki, Xiaowei Wang, Arun Subramaniyan, and Reetuparna Das
Year: 2021
Journel/Conference: book
Summary: BlaBlaBla
Rate: 3
Question: None
Eureka: None
---
# 1. Intro
PIM/PNM的核心思想：
what if we could move computation closer to memory

最初PIM在1990年左右被提出来，但因为两个问题没有被继续发展：
- 可计算DRAM太贵
- 摩尔定律还非常有效

随着3dic，PIM在2010s又被找了出来。3dic可以把dram和computation用不同的node做出来，然后bia在一起，3dic带来的更大带宽很适合computation

# 2. Tech Basic and Taxonomy
PIM这个term实际上的意思是near memory computing，实在dram 阵列外的logic。
![[Pasted image 20250124122959.png]]
通常near memory computing是一个用3dic或者2.5dic链接dram的逻辑电路，会有巨大的带宽。这和在dram上3d一个cpu不一样，原因主要是nmc的带宽巨大，而且没有cache，也不用通常的指令。
![[Pasted image 20250124123921.png]]

in-memory comouting就会改变memory cell了
主要分为两种：
1. in-memory(array) IMA.直接在array里原地计算，好处是bandwidth和energy efficiency好，但延迟大。并且要动array cell是非常麻烦的事情。
2. in-memory(periphery) IMP。不动array，在东西读出来后计算
![[Pasted image 20250124124547.png]]

# 3. Computing with DRAM
### 3.1 Early NMP Arch
![[Pasted image 20250124132711.png]]
这个做法的缺点是processor用dram工艺，性能不好
可以做的优化：
1. 用更简单的core，比如inorder pipeline。
2. 将processor离array更近

## 3.2 Stacked DRAM and NMP
![[Pasted image 20250124134706.png]]
每个pe是根据不同application不同的设计，可以直接访问local stack，也可以通过mesh noc访问其他stack

## 3.3 A Commercial Case

Pitfall: NPM可以减少大量data movement cost
1. DRAM的能耗主要在data movement，NPM只能减少其中的一部分
2. SRAM的主要能耗也在data movement，NPM没法减少这点，IPM可以
==没看完==

## 3.4 In-DRAM Computing and Charge Sharing Techniques
### 3.4.1 DRAM工作原理和TRA-based computing
和SRAM类似，先给bitline charge到1/2 vdd，然后打开wordline开关，此时bitline会有一个delta的电压变化。如果DRAM的cap是1，则是+delta；否则是-delta。然后SA会平衡bitline和$\bar{bitline}$的电压。最后由于DRAM用的是cap，还需要SA看情况给cap重新充电。
![[Pasted image 20250124140244.png]]
Ambit这篇文章发现了一个有趣的事情。在charge sharing那里，如果打开3个wordline让3个cap相连，会发现：如果3个cap里有0-1个是1，则最后三个都是0；如果2-3个是1马扎尔最后三个都是1。这就想一个3输入majority gate
![[Pasted image 20250124141047.png]]
逻辑可以写作AB+BC+AC，然后可以转换为$C(A+B) + \bar{C} (AB)$ ，因此可以控制C来拿DRAM cell做运算。Ambit还可以加一个transistor就实现NOT（其实就是通过开关这个transistor给bitline放电或者充电）。
下图实现了nand。
![[Pasted image 20250124141320.png]]
### 3.4.2 TRA-based computing优化
TRA-based computing主要有四个问题：
1. 需要改cell。_ComputeDRAM_ 指出，可以用现有DRAM就能做到TRA-computing，不需要新设计DRAM cell。只要在控制上支持multiple active就行
2. operator有限，只有and or not nand。_DRISA_ 则让其可以支持多种操作
3. Sensitivity to charge variance。ROC解决了这个。==这个没看懂==
4. Destructive operation:每次操作都会覆盖原有数据

### Discussion
因为现有DRAM非常成熟，新的In-DRAM tech不该challenge旧有体制，要不就需要太多work了

# 4. Computing With SRAM
## 4.1 SRAM basic
skip
## 4.2 Digital Computing Approach
一般在LLC进行运算
三个优点：
1. 不需要data movement
2. area efficient
3. power efficient
基本运算：
![[Pasted image 20250126170725.png]]
and和nor。都是直接激活两个word line
只有都是1才能保持BL precharge后电压上升，这就是and。同理对于BLB来说，只有两个bitline都是0，才能让BLB是1
计算用SRAM需要设计，否则不够balanced
![[Pasted image 20250126171544.png]]
采用bit-serial的运算方式。
为了达到这个目的，data和正常sram不一样，是transposed layout，也就是一行data被映射到一列（bitline）而非一行（wordline）。然后每次运算都是像上述and和nor一样操控不同的wordline，然后写回到其中一个bit
上图是一个加法，每cycle激活一位，然后串行计算

浮点加法也可以做，书中说了 但我没看、

pitfall：in-sram computing的density减小，而且会增加访问延迟
错误的，IMA会更改cell的设计，导致density会小很多；但IMP不更改array，实际density和delay不会有太大变化，一些work生成可以直接embedded into cache

## 4.3 数模混合方法
skip了 不懂模电

## 4.4 near SRAM computing


# 5. Computing with NVM
 skip

# 6. Domain-Specific Accelerator
## 6.1 ML
### 6.1.1 ML with NVM
pass
### 6.1.2 ML with In-SRAM Computing