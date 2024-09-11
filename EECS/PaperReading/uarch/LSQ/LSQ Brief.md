---
Author: "PeichenGuo"
Year: 2023
Journel/Conference: "RIOSJournel"
Summary: "LSQ Brief"
Rate: 5
Question: "None"
Eureka: "None"
---
## scaling问题
本质是避免CAM结构

### split
[[Using Virtual Load Store Queues (VLSQs) to Reduce the Negative Effects of Reordered Memory Instructions]]
直接在LSQ上加窗，减少lsq ooo发射的概率，从而减少很多conflict。简单粗暴但有效


### value approach ordering
[[Fire-and-Forget --- Load Store Scheduling with No Store Queue at All]]
通过记录store的forward历史，并记录其forward的ldq相对位置，从而避免sdq的cam结构，从而保持scalible

[[Memory Ordering --- A Value-Based Approach]]
in-order重新执行load指令，并对比第一次执行和重新执行时的值来判断是否产生了memory conflict。通过这种方式避免load queue的associative search。

[[Store Vulnerability Window (SVW) --- Re-Execution Filtering for Enhanced Load Optimization]]
针对lsq re-exec带来的dcache带宽压力，本文使用了一个boom filter来过滤掉不需要re-exec的load。这篇没看完，需要的时候再看

### search方法改进
[[Scalable Hardware Memory Disambiguation for High ILP Processors]]
通过hashing的方式来减少search能耗。只有在hash hit的时候才进行search，从而避免了绝大部分不需要search的load进行search。


### dependency prediction
[[Reducing Design Complexity of the Load Store Queue]]
本篇有两个工作：
1. 预测store-load pair来减少sdq的search
2. 记录所有ooo load，因为只有ooo load才会发生load-load conflict，从而减少ldq的search

## load-value time 问题
有三种解决方案：
解决load-store ambiguity问题——早点发
解决回滚问题——降低随便发的panelty
直接预测结果——从根源上降低load value time问题
### value prediction
[[Value locality and load value prediction]]
这篇提出了value prediction

[[The Predictability of Data Values]]
本文评估了stride预测（预测等差）和computation预测（根据历史pattern预测)。上古重要工作。

[[Correlated Load-Address Predictors]] 
用load addr prediction替代load value prediction，从而提高正确率。但占用d$带宽

[[Efficient Load Value Prediction using Multiple Predictors and Filters]]
用了四种predictor结合在一起，没啥创新的，但确实是state-of-art

[[Highly accurate data value prediction using hybrid predictors]]
提出了一种2-level predictor（4 history trace+pht），并提出了一个2-level+stride 的hybrid predictor。

[[Load Value Prediction via Path-based Address Prediction --- Avoiding Mispredictions due to Conflicting Stores]]
DLVP：用预测load的地址来代替预测load的值，从而减少load-store conflict。

[[Practical data value speculation for future high-end processors]]
将branch history考虑到value prediction中。



### Memory Dependency Prediction
[[Dynamic Speculation and Synchronization of Data Dependences]]
通过记录store-load pair，如果对应的store没有执行，则load也不会发射。这篇比较早

[[Feedback-directed MemoryDisambiguation Through Store Distance Analysis]]
软硬结合。用软件统计load-store的 store distance。当一个distance经常出现时(95%)以上，可以认为该load依赖固定的那个store。当load进来时，直接按照相对store distance去找对应的store。

[[Memory Dependence Prediction using Store Sets]]
通过store set(每个load的所有历史dependency)来预测load-store依赖。这个store set记录了多个store，因为本文相信1ld-多st或者多ld-1st的预测是有价值的。

[[Memory Renaming --- Fast, Early and Accurate Processing ofMemory Communication]]
用类似preg的renaming方式来处理memory Dependency


[[Store vectors for scalable memory dependence prediction and scheduling]]
通过存load-store相对位置+bit matrix的方式来简化之前memory dependency prediction的CAM结构

