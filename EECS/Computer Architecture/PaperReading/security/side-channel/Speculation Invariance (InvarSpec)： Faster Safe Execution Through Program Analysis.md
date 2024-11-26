---
Author: Zirui Neil Zhao, Josep Torrellas
Year: 2022
Journel/Conference: MICRO
Summary: 通过更细粒度地判断某条指令operand是否有预测执行风险，通过软硬件结合的方式来提前让一些指令可以被安全执行。
Rate: 3
Question: None
Eureka: None
---
# Tags
# Abstract

# Background
## Transient instructions.
有些指令在OOO执行中可能被squash，这些可能被squash的指令叫做transient
specture就是对transient进行攻击

# Motivation


# Solution
定义Speculation Invariant：
- 该指令不是一个speculative state的函数
- 该指令的oprands不是一个speculative state的函数
如果该指令成为了Speculation Invariant，那么就称之为达到了Execution-Safe Point ESP

squashing 的指令有一些，主要是branch和load。
只有ESP指令可以保证，哪怕被squash了，其operand和squash前也是一样的

只用hardware来判断ESP的话，instruction达到ESP当：
- 所有operand ready
- 前面所有的squashing instruction都执行并有结果 (称之为达到Outcome Safe Point OSP)
只用硬件做会很保守，而结合软件的程序分析可以扩大这些safe指令的范围。
对于某条load，什么是safe指令？
- safe branch。无法决定该条load是否被执行，也无法决定该指令operand
- safe load。之前的load的return data和该条load的operand无关
本文会提供一个safe set SS来存放那些safe指令的pc，硬件执行的时候看SS，如果pc在SS里就不用

==具体实现没看==
# Evaluation


# Unsolved Question


# Related Works
## Later Works

## Previous Works

## Similar Works
