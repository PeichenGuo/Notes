### Mobilizing the Micro-Ops: Exploiting Context Sensitive Decoding for Security and Energy Efficiency
2018
可变的mircro op decoding，可以根据context而改变
主要应用可能有两点：
1. 防御cache base的侧信道攻击
2. 减少功耗

### Packet Chasing: Spying on Network Packets over a Cache Side-Channel
2019
在NIC上做side channel attack

### Understanding the Impact of Socket Density in Density Optimized Servers
2019
datacenter inter cluster socket的探究

### Composite-ISA Cores: Enabling Multi-ISA Heterogeneity Using a Single ISA
没看
2019 


### Context-Sensitive Fencing: Securing Speculative Execution via Microcode Customization
Mobilizing the Micro-Ops的延伸
可以更灵活地插入fence

### Temperature-Aware DRAM Cache Management - Relaxing Thermal Constraints in 3-D Systems
没看
2020

### Agon: A Scalable Competitive Scheduler for Large Heterogeneous Systems.
没看 
2021

### SecSMT: Securing SMT Processors against Contention-Based Covert Channels
2022
注意到了smt为了security而做出的性能损失，尝试让smt安全而非关掉smt

### Half&Half：Demystifying Intel’s Directional Branch Predictors for Fast, Secure Partitioned Execution
2023
通过将两个互不信任的代码片段的pc值的一位（文中是PC[5]）强行设置成不一样的值，从而将他们的PHT分开，从而防止了specture类的攻击
[[Half&Half：Demystifying Intel’s Directional Branch Predictors for Fast, Secure Partitioned Execution]]
### Indirector: High-Precision Branch Target Injection Attacks Exploiting the Indirect Branch Predictor
btb攻击


### Pathfinder: High-Resolution Control-Flow Attacks Exploiting the Conditional Branch Predictor
接着half half的工作。
假设攻击者可以invoke victim的代码片段
#### attack primitive
shift：一次branch shift两次，所以可以构建一个zero footprint dummy branch。构建方法是branch pc的低19位和target的低6位是0.具体看half half。
clear：shift 194次
write：用target的低2位写，其他是0.每次写两位，然后shift。

##### read PHR：
这个最关键，细讲。
key idea是构建两个path：
- phr0,victim function写phr
- phr1，attacker写。
然后对比两个phr来猜phr0是什么
![[Pasted image 20250220003944.png]]
图a中读p0的方式：
1. 首先生成一个random k。
2. 如果这个k是0，也就是branch taken：
	1. attacker clear phr，初始化0
	2. call victim。victim此时会讲phr写成一个p193-p0这么一个值
	3. attacker将phrshift193次，这样只剩下p0一个doublet
	4. 然后执行test branch
3. 如果这个k是1，也就是non taken：
	1. attacker自己写phr，这里每次循环是00，01，10，11，是固定的
4. 执行test branch
由于k是random的，而两条不同的路径提供了一样或者不一样的phr。
如果phr一样，则test branch在k=0或者k=1的时候，也就是test branch跳转或者不跳转的时候，都会被map到同一个pht上，概率是50%，因此这个pht无法被训练，miss rate会接近50
而如果phr不一样，则test branch在跳转的时候被映射到一个pht，在不跳转的时候被映射到另一个pht，这样pht可以被训练，非常完美的训练，miss rate会接近0%。
由此可知道，missrate高的时候，x和p0是一样的。

知道p0后可以同样的操作知道p1，以此类推

##### pht read write
在可以读写phr的后，pht的读写就非常简单了，因为可以完全寻址到对应的pht。
读就是经典的prime test probe流程。先都设0，然后victim跑，最后在同样的pc和phr环境下反复运行一个taken branch，看会猜错多少次。

##### 194bit读法
上面的读法只能读194个，如何读到更多的呢？
![[Pasted image 20250220011934.png]]
我们可以用现有方式读出193bit。
然后我们可以构造一个branch low bit一样的attacker branch。此时如果phr一样了，就会有alias。
这就会导致attacker branch干扰victim branch的训练。从而导致了miss prediction
==我猜测，此时attacker这个taken和nontaken需要试一试，有一个回合victim同向，无法干扰训练；另一个会反方向，正好干扰训练==