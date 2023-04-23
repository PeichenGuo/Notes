---
Author: "Yiannakis Sazeides, James E. Smith"
Year: 1997
Journel/Conference: "MICRO"
Summary: "本文评估了stride预测（预测等差）和computation预测（根据历史pattern预测）在SPEC95上的性能，后者性能好于前者。add、sub、branch好预测，shift、load不好预测。"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
本文评估了stride预测（预测等差）和computation预测（根据历史pattern预测）在SPEC95上的性能，后者性能好于前者。add、sub、load好预测，shift不好预测。这使得hybrid predictor很有用。

### Motivation
本文在value prediction问世后系统性地对现有(1997)方法做个总结

### Solution
##### value classification
value可以分成三种：
1. constant：一直是常数
2. stride：等差，有变化
3. non-stride：其他情况
根据循环可以分成三种：
1. 不repeat
2. repeat且是stride
3. repeat但非stride

##### data value prediction model
Last Value Predictor：
只记录上一个的值。每次预测上一次出现的值。[[Improving the accuracy and performance of memory communication through renaming]]这篇类似这种方式

Stride predictor：
用最近两个或多个的差值来做预测。
本文测试的是two-delta preditor。这种方法维护了两个stride，s1记录任何最近两个value的差，s2维护用来预测的差。如果s1的差连续对了两次，则把s1写入s2。

Finite Context Method Preditor (fcm):
n-order fsm记录过去n个pattern，然后记录该pattern下某值出现的频率。如下图所示：
![[Pasted image 20230423191523.png]]

##### 简单讨论
==这部分过于显然且直觉，以至于我看到这个part的时候非常怀疑本文的含金量，我就不写了==

### Evaluation 
测了三种model，last value predictor（l）， 2-delta（s2)和fsm
在SPEC95上跑

![[Pasted image 20230423191915.png]]
总的来说，l < s2 < fsm
3-fsm甚至能达到average 78%的正确率

![[Pasted image 20230423192011.png]]
![[Pasted image 20230423192018.png]]
按指令来看，s2和fcm在add sub logic上都有较好的预测性能，但shift表现不太行。
在add和sub中，s2表现不比fsm差多少。
这说明

![[Pasted image 20230423192348.png]]
上图说明了下图各个区域都是什么意思。
![[Pasted image 20230423192251.png]]
上图显示：
- 18%的指令预测不了
- 40%的指令被所有种类的predictor成功预测
- 20% 只有fcm可以预测
- 5%fcm预测不到

因此可以看到fcm确实更好。

并且60%的prediction s2可以做，s2的开销还小。这使得hybrid方法很诱人。60%给低开销s2做，剩下20%给fcm做。

对不同指令做hybrid也是可行的。

另外本文验证了value多样性，以确认用有限的entry可以完成预测。
![[Pasted image 20230423230249.png]]
- 对于static指令而言，50%的只产生一个值，90%的指令产生的值小于64个
- 对于dynamic指令而言，50%指令产生的值小于64个，90%的指令产生小于4096个值
- add sub load产生的值种类更多，logic和shift产生的值更少（必然
由此，有限的entry理论上能做到data prediction

并且以gcc做实验，这prediction对不同input不是很敏感
### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
