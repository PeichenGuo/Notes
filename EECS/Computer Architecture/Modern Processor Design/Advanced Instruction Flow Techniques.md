## Static Branch Prediction Techniques
比较简单的预测，不会和run-time交互，没有学习。但这也是一种优点，基本上不需要硬件资源。
profile-based的方法是用不同的pattern来近似，在编译时就产生这个profile。坏处是不能dyanmic处理

### Single-Direction Prediction
只猜一个方向，要么taken，要么non-taken
1997的一个论文说60%是taken的
虽然只猜taken有更正确率，但hardware实现也更复杂，要去算一下目的地址。

### Backwards Taken/Forwards Not-Taken
预测前向的branch taken，后向的not taken。一般loop是这样的。奔腾4用这个方式做backup

### Ball/Larus Heuristics
需要编译器的hint。有些branch总会走向一个方向，比如报错的branch。这个时候编译器可以给个hint，从而达到很好的预测效果。
Ball和Larus发明了一种启发法：
![[Pasted image 20230612170449.png]]

### Profiling
先跑一遍，收集跳转信息，然后再用跑出来的profile来做prediciton。
最好情况下甚至能有70%-80%的正确率


## Dynamic Branch Prediction Techniques
最简单的dynamic都能实现80%-95%的正确率
但dynamic的实现也有些占用面积
dynamic的实质在于每次要记住实质上的分支走向，并学习里面的pattern

### 简单算法
#### Smith的算法
最简单的算法
第一次提出了saturated counter和一个table
pc hash成m位，索引一个counter，这个counter决定是否take
![[Pasted image 20230613121153.png]]

#### Two-Level Prediction Tables.
用最近的branch历史，放在branch history register (BHR)中。第二层就是装saturation counter的pattern history table (PHT)。PHT依靠pc和BHR的索引。
![[Pasted image 20240826235300.png]]
![[Pasted image 20230615162043.png]]
因此PC的位数和bhr的历史宽度是一个tradeoff。
与global history 类似的是local history. 区别在于BHR变成了一个table(BHT)。
==开销相近时，global往往比local效果好==
![[Pasted image 20230615162729.png]]
另一种做法是用hashing方式把branch化成几个组，然后不同的组用一个bhr。这种称为SBHT，S for per-set。
Patt用G(Global) P(Per-address) S(per-Set)来记录三种不同的BHT。用小写的gps来描述PHT的寻址方式。g的意思是不考虑pc信息，p用lower pc寻址，s用hashing来寻址。
Patt还用xAy来表示寻址方式。x是GPS，y是gps

#### Index-Sharing Predictors (gshare)
两层寻址开销太大，于是gshare被提出。做法很简单，就是BHR和PC hash到一块
![[Pasted image 20230615165045.png]]
与之类似的是pshare，区别是用了per-address的bht

#### Reasons for Mispredictions
1. fundamentally unpredictable。第一次无法预测
2. random unpredictable. 程序中的随机部分无法预测
3. 硬件受限。存储的历史是受限的。
4. aliasing

### Interference-Reducing Predictors
其实PHT就是一个cache类似的东西，cache的3C原则也适用于它。这一部分的方法都是来解决branch aliasing的。

#### The Bi-Mode Predictor
解决bias的方式
两个PHT，再多一个choice predictor来选择哪个PHT提供结果。choice predictor是一个用pc索引的2-bit saturation counter。
有taken bias的branch放一个PHT，not-taken bias的branch放另一个pht。这样即使发生alias，两个不同的branch映射到同一个PHT，这两个branch还是更可能有一样的bias。
被选中的pht更新，不选中的不更新。choice updater永远更新。
![[Pasted image 20230615173610.png]]

#### The gskewed Predictor.
解决bias的方式
有多个bank，每个bank都用不同的hash选中。最后的结果是多个bank投票产生。
原理很简单，不同的hash方法让同一个pc+history一样概率很小，换句话说，两个指令只会在一个bank上产生conflict，总有另两个bank有正确的值。
![[Pasted image 20230615174017.png]]
update方式有两种：
1. 全update。
2. 部分update。不update mispredict的bank，用更具体的方式说，当三个bank有一个产生的预测和另外两个不一样时，说明这次映射和其他的branch冲突了，因此只update两个一样的entry。

升级版的gskewed：pht1和2用hash，pht1用pc低位。没看懂为啥更好