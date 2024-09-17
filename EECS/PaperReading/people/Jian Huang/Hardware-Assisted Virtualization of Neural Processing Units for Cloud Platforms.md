---
Author: Yuqi Xue，Jian Huang
Year: 2024
Journel/Conference: arxiv
Summary: 提出一个全栈的NPU虚拟化技术
Rate: 3
Question: None
Eureka: None
---
# Tags
#virtualization, #neural-processing-unit, #machine-learning-accelerator.
# Abstract

# Background
## NPU
NPU的架构如下：
![[Pasted image 20240917212014.png]]
其中主要有一个计算matrix加法乘法的Matrix Engine ME 和计算普通vector计算的vector engine VE。
主要有两个问题：
- 不同task对ME和VE的需求是不一样的。
- HBM利用是不均匀的

下面两个图可以看出来，在不同的task下对ME和VE的需求是不一样的
![[Pasted image 20240917212109.png]]
![[Pasted image 20240917212116.png]]
下面的图可以看出来HBM访问不均匀
![[Pasted image 20240917212235.png]]
## 现有NPU虚拟化技术
现有NPU虚拟化不成熟：
- 缺少system abstraction
- 缺少architectural support
- 缺少ISA support
![[Pasted image 20240917212549.png]]
ISA上的问题：
- VLIW线性执行会造成很多假dependency
- ME allocation没法在runtime进行。
还有一个关键observation：不同的ME和VE执行往往是分离的。如图8，对于在第一组ME和VE（蓝色）上执行的指令，和第二组ME和VE（绿色）上执行的指令是没有依赖关系的。
# Motivation

# Solution
## vNPU abstraction
![[Pasted image 20240917213702.png]]
使用起来有三步：
- 初始化的时候向H态的manager发送一个请求
- manager进行MMIO映射，相当于给guest分配了NPU资源
- 运行时NPU直接通过MMIO从guest的host memory获取data，不经过h mode
![[Pasted image 20240917214312.png]]
==后面都没看== #skip


# Evaluation


# Unsolved Question


# Related Works
## Later Works

## Previous Works

## Similar Works
