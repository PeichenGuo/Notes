---
Author: Daichi Fujiki, Xiaowei Wang, Arun Subramaniyan, and Reetuparna Das
Year: 2021
Journel/Conference: book
Summary: BlaBlaBla
Rate: 3
Question: None
Eureka: None
---
# Intro
PIM/PNM的核心思想：
what if we could move computation closer to memory

最初PIM在1990年左右被提出来，但因为两个问题没有被继续发展：
- 可计算DRAM太贵
- 摩尔定律还非常有效

随着3dic，PIM在2010s又被找了出来。3dic可以把dram和computation用不同的node做出来，然后bia在一起，3dic带来的更大带宽很适合computation

# Tech Basic and Taxonomy
PIM这个term实际上的意思是near memory computing，实在dram 阵列外的logic。
![[Pasted image 20250124122959.png]]
通常near memory computing是一个用3dic或者2.5dic链接dram的逻辑电路，会有巨大的带宽。这和在dram上3d一个cpu不一样，原因主要是nmc的带宽巨大，而且没有cache，也不用通常的指令。
![[Pasted image 20250124123921.png]]

in-memory comouting就会改变memory cell了
主要分为两种：
1. in-memory(array) IMA.直接在array里原地计算，好处是bandwidth和energy efficiency好，但延迟大。并且要动array cell是非常麻烦的事情。
2. in-memory(periphery) IMP。不动array，在东西读出来后计算
![[Pasted image 20250124124547.png]]

# Computing with DRAM
### 3.1 Early NMP Arch
![[Pasted image 20250124132711.png]]
这个做法的缺点是processor用dram工艺，性能不好
可以做的优化：
1. 用更简单的core，比如inorder pipeline。
2. 将processor离array更近

## 3.2 Stacked DRAM and NMP
![[Pasted image 20250124134706.png]]
