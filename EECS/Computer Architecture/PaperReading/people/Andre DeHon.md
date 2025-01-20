# PLD: Partition Linking and LoaDing on Programmable Logic Devices
目的：解决HLS+FPGA编译慢部署慢的问题
insight：软件compiler会有o0-o3的treadoff来选择编译速度还是优化程度，而fpga编译就是一个全局优化问题。本文通过将fpga划成page，并且基于dataflow的方式，来提供了o0-o3几个编译选项，来让软件工程师可以快速开发，缩短编译时间
![[Pasted image 20250120221742.png]]
本文是基于dataflow的，需要一个top.cpp来描述flow，flow的每个节点都是一个operator，右图是其中一个operator flow_calc.cpp的实现
![[Pasted image 20250120222050.png]]
本文第二个点在于把fpga分成多个page，每个page预先部署一个picorv32 core。而不同page之间留出空间做连线
![[Pasted image 20250120221751.png]]
## O0
![[Pasted image 20250120221846.png]]
o0非常聪明，直接把flow的每个operator编程elf，然后每个预部署的core执行这些elf。top.c描述了整个flow，会生成一个dfg.ir来描述flow，用来构建driver c和连接不同的page
相当于每个operator的执行是由一个通用小核来进行。
图中的overlay lib是描述了不同page的情况，比如其资源、边界、接口等等。
## O1
![[Pasted image 20250120222549.png]]
o1非常符合直觉，每个operator.c单独编译城一个.v，然后部署在对应的page上，同样的top.c生成链接，和host.c一起生成host程序
在o1中，overlay lib可以给不同的operator提供page context，这样每个page可以单独编译，不需要管其他事情
## O3
![[Pasted image 20250120222746.png]]
统一综合，全局规划
# Architectural Support for Software-Defined Metadata Processing

# 要问的问题
Could you please let me know how to correctly pronounce your name



The problem of HLS + FPGA
- The performance problem. The frequency of HLS generated FPGA is hard to be reasonably high
- The compiling problem. Compiler is not mature and it is very slow.
- The programming model. It is different than normal software language so it is hard for software engineer's usage
- The debug problem. HLS is hard to debug and breakpointing. 
