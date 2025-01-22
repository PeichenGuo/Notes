# Only Buffer When You Need To
本文提出了一套软硬件结合的方式来支持GPU的atomic性质


现有的很多GPGPU算法，比如随机梯度下降，需要atomically update shared weights
现有的GPU使用一种简单的、software-driven的方式，导致硬件开销很大

重要insight：
一些算法，比如graph analytics和ML training weight update，其atomics access都是commutative的，每个操作原子性很重要，但操作之间的顺序不重要。

硬件上的创新：
buffer partial device-scope atomic updates locally at each SM in a small local atomic buffer (LAB)
