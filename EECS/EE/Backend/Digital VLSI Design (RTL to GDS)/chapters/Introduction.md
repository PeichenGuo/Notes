---
Chapter: 1
---

# Flow
![[Pasted image 20240506154513.png]]
 前面不介绍了 从logic synthesis开始
## logic synthesis
![[Pasted image 20240506154606.png]]
将rtl映射到网表
需要：
- tech lib 工艺库
- rtl
- constraint files，常用Synposys Design Constraint SDC, 描述optimization goal。
- Design for Test (DFT) definition

DFDT是在数字电路设计阶段确定用于测试电路的策略和方法的过程。DFTD 主要包括确定测试点、设计测试模式和确定测试方案等内容。
1. **确定测试点**：确定需要测试的电路功能和特性，以及测试的目标和要求。这包括确定关键路径、故障检测覆盖率要求等。
2. **设计测试模式**：确定测试时需要输入的测试模式，包括测试向量、时钟周期等。测试模式应该能够有效地覆盖电路中的各种可能故障，并且能够提供可靠的测试结果。
3. **确定测试方案**：确定测试的实际执行方案，包括测试设备、测试程序、测试时间等。测试方案应该能够在满足测试要求的同时，尽量减少测试成本和时间。

在post synthesis checks中的STA指的应该是PnR前的STA

## Physical Design
![[Pasted image 20240506155203.png]]
Floorplan 布局规划：
把各个模块放好

IO Ring：
周围一圈的buffer和io pin

Power Plan：
规划供电（vdd）

Inputs：
![[Pasted image 20240506155749.png]]
后端流程：
![[Pasted image 20240506160148.png]]
