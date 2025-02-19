---
Author: Hosein Yavarzadeh，Dean Tullsen
Year: 2023
Journel/Conference: IEEE SP
Summary: 通过将两个互不信任的代码片段的pc值的一位（文中是PC[5]）强行设置成不一样的值，从而将他们的PHT分开，从而防止了specture类的攻击
Rate: 3
Question: None
Eureka: None
---
# Tags
# Abstract
通过将两个互不信任的代码片段的pc值的一位（文中是PC[5]）强行设置成不一样的值，从而将他们的PHT分开，从而防止了specture类的攻击
# Background
现在的control flow isolation主要要发生在两个地方
btb和cbp
cbp是做出跳转或者不跳转决策的模块，bht是cbp中存放历史pattern的组件

btb可以被软件flush，但是cbp目前没有这种做法。
cbp比btb要复杂很多，很难flush

cbp隔离很难，原因有二：
1. 制造商保密，需要逆向工程
2. 在现代cbp中，会有几百个bit参与决策。因此很难去判断哪些需要隔离，哪些不需要

## spectre
![[Pasted image 20250219213255.png]]
攻击程序先训练predictor，让其在target address上predict correct
然后victim的target branch无论对错，都会预测执行。
图中的例子中，由于这个data用来做为地址读了，因此这个data会带来cache上的改变，可以有一系列方法读出这个data


# Motivation


# Solution
本文提出了一个纯软件的cbp partition方式。
在对intel的逆向工程后，发现一个没有经过处理（hash）的bit，参与了每个表的索引。因此这个bit不同的两个branch永远不会影响彼此
同样因为这个特性，分成两个容易；分更多就很难了

## 逆向工程：Global History
第一步假设
假设使用了global history，像tage那样的
### 测GH大小
两个相关的branch，中间插入不相关的，然后逐渐拉大距离
![[Pasted image 20250219214455.png]]
结果如下：
![[Pasted image 20250219214527.png]]
发现，gh的大小是记录93个taken branch。而non taken不被记录。
常用的ghr有两种：
- GHR。taken为1 nontaken为0的移位寄存器
- Path  HR。只有taken会记录，可能插入1个bit或者多个bit
因此接下来要搞懂PHR怎么work的
### PHR input
一般而言，phr会插入pc中的一些bit。
假设插入的是pc的低i位和target address的低j位会影响phr的低k位。
现在要搞懂这个是i j k是什么
![[Pasted image 20250219215754.png]]
这个程序首先用dummy branch把一些branch刷进去。这些branch的i和j都很大，确保刷进去的是0
然后一个个调整i和j来看什么时候产生预测
结果如下
![[Pasted image 20250219220203.png]]
发现用到了pc的19位，target的6位

接下来看是哪几位
![[Pasted image 20250219220319.png]]
发现pc的后三位没有用到，\[18:3\]用到了（毕竟fetch group)
target用到了\[5:0\]
### 寻找hash function
假设需要hash，毕竟（16+6）\*93 太大了，假设里面有一些位会是xor的。
还是用listing5的程序，每次选择22位中的一对，然后看这两个是否被xor了。两个bit xor是0，test branch就会预测失败；否则test branch预测成功
结果如下：
![[Pasted image 20250219221704.png]]

然后还需要知道phr移位有多少
方法是跑listing3，这次只flip pc和target中一位。然后看插入多少个dummy branch会让这个flip的bit学习的内容失效。
比如B5这个bit置高后，89个dummy branch可以覆盖它。总共depth是93个branch，说明b5不是在最后一位。
比如再测B6.发现B5和B6的结果是一样的（论文里没说，我猜的），就说明这俩是一起被shift出去的，一次就是shift2位。
于是得到了下面的结果![[Pasted image 20250219223137.png]]
然后推测出function是
**![[Pasted image 20250219223157.png]]

## 逆向工程：PHT
在tage中，pc和gh的一些bit被用来寻址PHT，然后pht里面装的才是饱和计数器这种决定是否跳转的东西
### PC的哪些bit用来寻址PHT
由于已经知道ghr结构，现在可以设计ghr，然后单独看pc如何。
方法论就是通过调整pc，控制ghr，造成不同pc的branch发生alias。
![[Pasted image 20250219224053.png]]
两个test branch低位相同，方向相反（一个taken 一个不taken）。如果链各个branch alias，也就是落在一个pht上，大概率会让第二个branch miss predict。因此随着alignment上升，会在某一刻造成alias，然后造成miss rate上升
### find associativty
pht的结构类似cache，也会有组相联
方法论是构造出 index一样但tag不一样的\<pc, phr\>
![[Pasted image 20250219224733.png]]
先固定phr，然后尝试pc
结果：
![[Pasted image 20250219225358.png]]
首先发现大概只能记住4个不同的pc。很有趣的是，pc\[5\] = 1之后，又多记了四个pc。这说明pc\[5\]在index中，选择到了另一个set。
![[Pasted image 20250219225622.png]]
在明白pc的作用后，固定pc研究phr。tage中不同的长度有不同的pht去记录和学习，因此更长的history会用到更多的set，也就会有更多的正确预测。
phr185-phr0，这186位一位一位地翻转，pc5=0。结果如下：
![[Pasted image 20250219230226.png]]
这说明有三个pht，其history分界线分别在21和57
### 寻找hush function
现在我们只用185-57这一段，此时只会影响最大的那个pht。这时候可以来找hush function了。此时pc5固定位0，防止不同set
branch分为两组：
- 第一组：固定bit为1，四个不同index的branch。比如固定位是phr184
- 第二组：调整bit为1，四个不同index的branch。此时每次iteration这个bit可以为0到185中的任何一个
正常来讲会分到不同set，但如果分到了同一个set，就会出现eviction，从而第一组的再次执行就会预测错误。
结果如下：
![[Pasted image 20250219230715.png]]
两个series，1、17、33 和8、24、40.相隔16。发现就是一个16bit的fold
找到的规律如下：
![[Pasted image 20250219234448.png]]
总结起来就是
![[Pasted image 20250219234458.png]]
这时候我们惊喜的发现，==所有set可以被pc5来划分为两个==，这使得partition成为可能

## Half and Half

# Evaluation


# Unsolved Question


# Related Works
## Later Works

## Previous Works

## Similar Works
