# 2023
## CIFER
CIFER: A Cache-Coherent 12-nm 16-mm2 SoC With Four 64-Bit RISC-V Application Cores
first open-source, fully cache-coherent, heterogeneous many-core
![[Pasted image 20231011234036.png]]

efpga 可以用来加速


## Beyond Static Parallel Loops
a work-stealing runtime 来解决many core编程困难问题
看不懂

## EVE: Ephemeral Vector Engines
用pim sram+bit-ephemeral 技术解决vector engine太大的问题

## big.VLITTLE: 
On-Demand Data-Parallel Acceleration for Mobile Systems on Chip

area-efficient high-performance architectures
support next-generation vector architectures to efficiently accelerate data-parallel workloads
把小核临时配成vector machine


# 2021
## UMOC
UnifiedModularOrderingConstraintsto UnifyCycle-andRegister-Transfer-LevelModeling
两个observation: 
RTL里组合逻辑，写是先于读的（写的一瞬间其他读都改变）
CL里deque是先于enque的
这里是统一的 因此可以尝试unify cl和rtl

## A Tensor Processing Framework for CPU-Manycore Heterogeneous Systems
在cpu异构架构上扩展了pytorch

## CAPE
PIM的 没看

## Ultra-Elastic CGRAs for Irregular Loop Specialization
CGRA coarse-grain reconfigurable arrays
没看 估计看不懂

# 2020
## Towards a Reconfigurable Bit-Serial/Bit-Parallel Vector Accelerator Using In-Situ Processing-In-SRAM
Process in SRAM : 减少multi port sram开销

## Implementing Low-Diameter On-Chip Networks for Manycore Processors Using a Tiled Physical Design Methodology
一种新的NOC方式：
we explore mesh and torus topologies with internal concentration and/or ruche channels

## PyMTL3
PyMTL3: A Python Framework for Open-Source Hardware Modeling, Generation, Simulation, and Verification

## Efficiently Supporting Dynamic Task Parallelism on Heterogeneous Cache-Coherent Systems
没看

## TypeFreezing
compiler的

# 2019
## Evaluating Celerity
Evaluating Celerity: A 16-nm 695 Giga-RISC-V Instructions/s Manycore Processor With Synthesizable PLL

## PyOCN
PyOCN: A Unified Framework for Modeling, Testing, and Evaluating On-Chip Networks
PyOCN is the first comprehensive framework for modeling, testing (, and evaluating on-chip interconnection networks.’
 
# 2018
### An Architectural Framework for Accelerating Dynamic Parallel Algorithms on Reconfigurable Hardware

## Mamba
Mamba: Closing the Performance Gap in Productive Hardware Development Frameworks

