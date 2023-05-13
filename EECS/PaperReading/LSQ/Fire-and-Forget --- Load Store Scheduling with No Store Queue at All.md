---
Author: "Samantika Subramaniam; Gabriel H. Loh"
Year: 2006
Journel/Conference: "MICRO"
Summary: "通过记录store的forward历史，并记录其forward的ldq相对位置，从而避免sdq的cam结构，从而保持scalible"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
由于SDQ开销过大，而其存在原因大部分是因为load lookup。本文发现，store往往forward给同一个load。本文通过预测forwarding store的queue index，从而完全放弃了sdq以减轻开销，从而达成scalible。

### Motivation
static load往往都依赖于一个static store，而且其相对位置甚至很可能是固定的。

之前有人做过SQIP（Store Queue Index Prediction）
![[Pasted image 20230512211930.png]]
有一个FSP(Forwarding Store Predictor) 和 SAT(Store Alias Table)
FSP是一个load的pc映射到一个store的pc。fsp是一个组相联的，一个pc的一部分索引到一个store的pc的一部分。
SAT是直接映射的，里面存放store的值。

本文FnF是SQIP的另一种思维方式。SQIP是站在load的角度上：一个static ld往往读一个static st。
FnF是站在st角度上的：一个static st往往给同一个static load送。
这种视角的转换可以直接干掉sdq。

### Solution
三个table：
1. SPCT(Store PC Table):记录store的pc和其对应的load的lq entry
2. LDP(Load Distance Predictor):记录load和store的相对距离
3. LCP(Load Consumption Predictor):决定load该不该利用forward data
![[Pasted image 20230513105644.png]]
上图：
a) 当一个store进来时，在sq里记下来它MRDL(Most Recent Dispatched Load)的LSN(Load Sequence Number)，相当于记下来load的绝对位置。
b) 当store commit的时候。以其目的地址做index写入SPCT，存下LSN和store的pc。相当于记录了一个load到store的映射。
c) 当一个load进来发现从dcache中取出了错误的值时，此时说明有一个previous store overwrite了。此时这个load会拿目标addr做index查SPCT，对应entry会有个store。此时该entry会有一个LSN，拿load的LSN和这个LSN相减，就是store forward在lq中的相对距离。此时把这个距离以store的index写入LDP，记录store的forward位置；再把LCP以load的pc为index置1，说明该load需要store forward。
d) 当store再次进来，查到LDP中有相对位置记录时，用MRDL的LSN加相对位置mod LQ大小得到forward lq entry的位置。
e) store直接把值写入lq对应位置。

这样一来sq完全不需要cam结构了。

### Evaluation
==没看，摸了==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
