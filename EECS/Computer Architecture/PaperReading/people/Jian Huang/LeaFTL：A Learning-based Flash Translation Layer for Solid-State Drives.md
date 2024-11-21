---
Author: Jinghan Sun，Jian Huang
Year: 2023
Journel/Conference: ASPLOS
Summary: runtime时学pattern的ssd flash translation layer，做LPA-PPA的regrassion。相当于做address prediction
Rate: 3
Question: None
Eureka: None
---
# Tags
#Learning-Based-Storage, #Flash-Translation-Layer, #Solid-State-Drive
# Abstract

# Background
## SSD和FTL
![[Pasted image 20240917232910.png]]
右边是meta data structure
其中：
- ACM是address caching table的cache
- GMD记录所有的address caching table的page位置
- BVC记录每个block里valid的page（用来做garbage collection)
- PVT记录一个block里哪些page是valid的
# Motivation


# Solution
LeaFTL利用databuffer（ssd会把写集中在一起写入，相当于store buffer。data buffer时临时存放写的地方）来学习LPA-PPA的规律
![[Pasted image 20240918000112.png]]
LPA-PPA的规律大概如上，会有部分线性，可学。

==剩下没看==
# Evaluation


# Unsolved Question


# Related Works
## Later Works

## Previous Works

## Similar Works
