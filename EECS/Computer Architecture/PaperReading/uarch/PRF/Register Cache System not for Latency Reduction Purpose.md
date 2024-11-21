---
Author: Ryota Shioya，Shuichi Sakai
Year: 2010
Journel/Conference: " MICRO"
Summary: BlaBlaBla
Rate: 3
Question: None
Eureka: None
---
## Authors

## Abstract

## Background

## Motivation
之前的做法当cache miss的时候要不是stall，要不就flush，总之会产生很多bubble
![[Pasted image 20240911235139.png]]
![[Pasted image 20240911235225.png]]


## Solution
这篇文章把cache访问拉长成两拍，和main prf时间一样，这样一来普通miss就没有bubble了
![[Pasted image 20240911235325.png]]
只有在cache miss了，且main memory读不出来那么多个时才会pipeline disturbance
这么做实际上不会降低太多IPC，因为fully pipeline时，只有bubble会降低IPC；除此之外就是预测错误或者exception需要重启流水线时多一拍读少一拍读对IPC会有影响。
## Evaluation
6发射的MIPS R10000， 6发射，8r4w
### IPC
对比的是8r4w的perfect prf
![[Pasted image 20240911202203.png]]
### miss rate
![[Pasted image 20240911235758.png]]
可以看到USE-B很重要嗷

### area and power
这方面比较重要。因为这个设计其实目的就是为了在不影响性能的情况下减少prf读写口，面积和功耗
![[Pasted image 20240912000906.png]]
![[Pasted image 20240912000915.png]]
IPC-energy trade-off
从左往右每个点分别对应4 8 16 32 64 entries
![[Pasted image 20240912001020.png]]

## Unsolved Question


## Related Works
### Later Works

### Previous Works

### Similar Works
