# Back to Future
2015
判断incoming line是cache friendly还是cache averse
![[Pasted image 20250119155707.png]]
## Belady’s algorithm

## OPTgen
OPT在本文的意思是Belady's algorithm
首先定义usage interval。在一个line两次被使用的时间，就是usage interval。可以见下图。本文用到了一个usage vector来算usage time，见图C，其每个entry指，当某个entry access时，有多少个entry在等待被用。
比如T=1的时候，B加入进去，由于没有未来知识，我们不知道t0的a和t1的b会不会被再次使用，于是都是0.当t=2时，b的第二次访问到达，我们知道了t=1的b在等这次访问，因此t=1到t=2的所有entry+1.可以一看到t=6时就是如此，所有entry加一因为我们知道t=0的a在一直等着t=6的a。
再看t=9的时候，t9的e进来时看到上一个e是t5，然后看t5-t9之间所有的usage，发现t5的时候usage=2，而这个例子的cache最大就是2，说明t5e进入的时候，usage已经满了，t5e不能被cache，因此这个t9e是cache miss。
![[Pasted image 20250119162717.png]]
## The Hawkeye Predictor
根据opt gen的结果来预测line是否该被驱逐
如果没有的该被驱逐的，则用LRU替换

# Efficient Metadata Management 
提出了Managed Irregular Stream Buffer， MISB
这里的metadata指的其实是每个address对应的信息，比如下图，在GHB中metadata就是一个fifo pattern。而在这个MISB中贼是physical address到structural address的映射。比如下图例子，收到地址A后，进行Physical-structural Mapping (PS mapping)到19，然后19的后一个是20，然后再将20进行Structural-Physical Mapping（SP）得到地址B，然后prefetch B
![[Pasted image 20250119183103.png]]
设计这个metadata management的两个原则：
1. delay要小，否则没法及时prefetch。late prefetch is toxic
2. traffic overhead要小，否则开销太大
为了实现这两个原则，MISB有三个组成部分：
1. metadata cache
2. metadata prefetcher
3. metadata filter。来防止没必要的prefetch发生
![[Pasted image 20250119183034.png]]
![[Pasted image 20250119184351.png]]
因为很多时候cache miss，会发现对应的metadata其实根本不存在（metadata需要被store进memory，也就是至少被访问过一次），此时load这些metadata是无意义的。filter就是为了减少无意义的load。本文的filte人就是一个address的hash，store时会置1。

training就是跟踪address flow。更新谁和谁挨着之类的。
==traing没有细讲，我比较好奇的是，具体是怎么实现的== 

# Temporal Prefetching Without the Off-Chip Metadata
本文设计了一个不需要off chip metadata的Temporal Prefetcher: Triage，因为：
1. 只有一小部分metadata是有用的
2. 大部分情况下，一个有效的prefetcher带来的好处大于一个巨大的data cache
triage将address按照PC分为两个stream，然后metadata存储的是一个address映射到其pc划分的下一个adress
![[Pasted image 20250119192241.png]]
这个triage还可以动态地调整llc中data和metadata的partition。它会存两个optgen的copies，同时做预测，如果大的效果好过5%，则调整成大的大小；如果小的效果没有落后5%，则调整成小的大小
![[Pasted image 20250119193213.png]]
llc access的同时查metadata，如果查到了就prefetch
同时会更新training unit，然后会update metadata（3），然后会更新replacement state（4）。replacement unit会定时计算是否需要更新partition sizena
# Applying Deep Learning to the Cache Replacement Problem
hawkeye没有加入PC信息
这个工作项学习一个load的pc和其是否cache friendly的关系
为什么用PC？
- PC相比address来说更少
- LSTM的大小和学习时间取决于input space，pc更合适
本文提供了一个方法论：
- 用LSTM训练
- 分析LSTM的attention layer，从而得到一些insight
- 通过insight建立一个support vector machine，用他来进行预测

获得的insight：
1. long history of PC，大概30个，是很有用的
2. 输入顺序和预测成功率没有太大关系
3. 模型的成功主要来自于几个有用的source （PC）

# A Hierarchical Neural Model of Data Prefetching
提出了Voyager。将地址分为了page和offset，然后提出了一种方法来学习这两者的关系
data prefetch有class explosion问题，因为其输入空间和输出空间都是地址空间，非常大。
其次data prefetch也是labeling problem，是完全可以通过程序执行来知道对错的
![[Pasted image 20250119213512.png]]
voyager结合pc信息和address，尤其是把address分为了offset和page，并且还有一个page-aware offset embedding来提取page和offset的信息，这些信息一起作为input训练LSTM
![[Pasted image 20250119213518.png]]
本文将offset比作word，page比作context，page-offset就和NLP的典型问题有了关联。
因为不同的labeling对不同的问题有不同的效果，比如有些城西有较好的spatial memory access，此时spatial labeling就好使，而有些pointer多的程序就适合pc labeling。本文用了五种label
1. global：整体访问的address stream的下一个
2. pc：同pc的下一个地址，通常是指针访问好用
3. basic block：同一个bb访问的下一个地址
4. spatial：represents the next address within a spatial offset of 256 ==没懂==
5. co-occurance：下10个access中出现次数最多的那个access
![[Pasted image 20250119214726.png]]
Voyager不practical，给出了三种practicle的思路：
- Neural-Inspired Practical Prefetchers.
- Profile-Driven Training with Online Inference。offline训练，isa配置hardware
- Completely Online Neural Prefetchers

# Effective Mimicry of Belady’s MIN Policy
提出了一个multiclass predictor，mocking jay，这个基于PC的predictor可以学习每个line的reuse distance，然后根据这个distance evict line。其结果非常接近Belady’s MIN，一个理想算法，并且需要未来信息。
![[Pasted image 20250119220947.png]]
sample cache里存放block address（cacheline）到其last access timestamp和last PC signature。sample cache会记录8倍于set大小的历史记录。
Reuse Distance Predictor（RDP）：学习PC -> reuse distance。是一个用PC signature index的direct mapped cache。
estimated time of arrival (ETA) counter: 

# A New Formulation of Neural Data Prefetching
提出了一个Twilight prefetcher。insight是：大部分的指令，其合理的successor其实只有几个，output space不是整个地址空间。还提出了T-Lite，使用聚类把input空间也变小了
![[Pasted image 20250119222948.png]]
![[Pasted image 20250119223033.png]]
![[Pasted image 20250119223539.png]]

# 要讨论的事情
why only LLC? delay is not that  important in LLC access? or it can gain a largest bonus. 

core id / process id is important? The PC trace and address trace speak for itself, but will it help to add this information into considerations?

for inter-chip connections, like through NUMA, UMA,  which may happen a lots in HPC and data services. How about NUMA for interchip connections? It is not taken into considerations is because its high delay? 200-500 cycle is beyond cache usage cycle? 8x size of cache is more than 200-500cycle, so a numa access should be take into consideration



make it more practicle
1. how to shrink the model size
2. how about isa-configuring hardware prefetcher

