---
Author: Haoyang Zhang, Jian Huang
Year: 2023
Journel/Conference: MICRO
Summary: BlaBlaBla
Rate: 3
Question: None
Eureka: None
---
## Authors

## Abstract

## Background
![[Pasted image 20240917104835.png]]
GPU memory architecture

## Motivation
GPU DNN Memory特性：
1. 在 DNN 训练期间，GPU DRAM 中只有一小部分张量处于活动状态且需要。见图2
2. 在 DNN 训练期间，许多张量长时间处于不活动状态。不活跃的时间是远大于ssd访问延迟的。见图3
3. 由于tensor的diversity很大，不同的长度的不同inactive时间不一样，所以需要好好设计swap算法。见图4
4. 静态swap算法无法适应要求，需要动态算法

![[Pasted image 20240917105044.png]]
![[Pasted image 20240917105050.png]]

## Solution
主要由三个部分组成
(1) 张量活力分析器，在我们编译 DNN 模型时量化张量大小和活跃度
(2) 张量迁移调度器，用于提前规划张量迁移
(3) 统一内存系统，用于简化 GPU 内存管理并实现透明张量迁移
![[Pasted image 20240917105513.png]]
### Tensor Vitality Analysis
这步主要干两件事情：
- 区分global tensor和intermediate tensor
- 计算tensor的inactive time period

global的比如weight就在training迭代中被反复用到，要尽可能放在UVM里
intermediate是中间生成的，比如activation and gradient，这部分就要计算生成到消失的时间，这是一个intermediate的life time。下面的图可以很好解释什么是lifetime和inactive time。其中灰色斜线是life time之外，灰色是inactive time。
![[Pasted image 20240917110040.png]]
### Evict算法
这主要涉及三个点：
- evict对象
- evict desination。是host memory还是ssd，前者带宽好，后者容量大。优先ssd，ssd带宽满了放host
- 要最大程度利用bandwidth

evict对象
用GPU memory pressure来衡量evict benefit。GPU memory pressure就是所有没有evicted的tensor大小。

下面是算法，可以看出：每次evict优先mem pressure减得多的；如果ssd满了才放host
![[Pasted image 20240917111714.png]]

### prefetch算法
G10遵循latest safe prefetch time，即最晚不影响性能的prefetch时间；还有optimized prefetch time，即最早不会爆gpu cap的prefetch时间。如下图：
![[Pasted image 20240917112245.png]]
如果能放在t’i就放，否则放ti

通过instrumentation插入prefetch指令

### Unified GPU Memory and Storage
G10用统一虚拟地址来规划整个memory
migration流程如下：
![[Pasted image 20240917113722.png]]
## Evaluation
![[Pasted image 20240917114043.png]]
效果看上去很不错
具体没有看

## Unsolved Question


## Related Works
### Later Works

### Previous Works

### Similar Works
