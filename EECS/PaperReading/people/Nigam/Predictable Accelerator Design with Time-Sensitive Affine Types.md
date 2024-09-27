---
Author: Rachit Nigam
Year: 2020
Journel/Conference: RIOSJournel
Summary: BlaBlaBla
Rate: 3
Question: None
Eureka: None
---
# Tags
# Abstract

# Background
![[Pasted image 20240928002929.png]]
对于上面一个代码，HLS会做unrollig，比如程序员显示地提示
```
#pragma HLS UNROLL FACTOR=8
```
就会unroll成八个乘法并行，如图3b。但此时memory因为资源有限，因此输出得做mux，因此性能受限，并且性能和时序都无法预测，如图4a
![[Pasted image 20240928003025.png]]
如此程序员就可以显示地提示
```
#pragma HLS ARRAY_PARTITION VARIABLE=m1 FACTOR=8 
#pragma HLS ARRAY_PARTITION VARIABLE=m2 FACTOR=8
```
意思就是将某个数据分为多个partition存储
很显然，partition的数量和unrolling的数量会有一定的关联，具体而言，==当unrolling factor整除partition factor时，面积和性能会好很多==，如图4b
![[Pasted image 20240928003248.png]]
而这个规则是隐式的
# Motivation
本文的目标就是为了显示表达这个规则。

# Solution
使用memory type，显示的给数组分配memory。这样就可以对读写进行限制了。同时还可以给memory显式进行banking，比如：
```
let A: float[10 bank 2]; 
A{0}[0] := 1; 
A{1}[0] := 2; // OK: Accessing a different bank.
```

使用了order分隔符。c中的分号;成为了并列分隔符，前后的语句可以并列执行；---成为了递进分隔符，不能并列进行


# Evaluation


# Unsolved Question


# Related Works
## Later Works

## Previous Works

## Similar Works
