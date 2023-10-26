---
Book: CAQA
Chapter: 4
Summary: BlaBla
---
# Introduction
SIMD: single instruction multiple data
SIMD理论上笔MIMD在能耗上更好，因为一条指令fetch可以对应多个data fetch。
# Vector Architecture
![[Pasted image 20231026145104.png]]
scalar register 可以用作vector load/store的地址，也可以是fpfu(float point function unit)的输入

RV64V将data size和不同的vector reg联系在一起