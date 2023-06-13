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
#### two-Level Prediction Tables.