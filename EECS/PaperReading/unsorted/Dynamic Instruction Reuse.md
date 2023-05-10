---
Author: "Avinash Sodani and Gurindar S. Sohi"
Year: 1997
Journel/Conference: "ISCA"
Summary: "通过复用执行过的代码的结果来避免部分代码重复使用，并通过preg id和dependency来确定是不是可以使用复用值。"
Rate: 4
Question: "None"
Eureka: "None"
---
### Abstract


### Motivation
主要的reuse有两种：
第一种是branch prediction的squash，如下图所示。
![[Pasted image 20230510163032.png]]
有可能squash掉一个不需要squash的指令（图中的c）。
第二种是程序员喜欢写更通用的代码，比如函数。


### Solution
本文设置了一个Reuse Buffer来存储执行过的指令的结果，此时问题被简化为三个：
1. 如何确定哪个RB entry被access。这个好解决，用pc索引就行
2. 如何确定该entry reuseble。需要一个reuseable test
3. 如何mange RB。需要确定什么时候replace，并确定如何维护consistency
主要的问题是2，其他问题需要围绕这个开展。

关于2有三种解决方法：
1. 用指令的operand value判断。input一样自然指令结果一样
2. 用指令的preg id判断。同时每次一个preg被更新的时候，使RB中所有对应的preg失效（相当于维护一致性，让preg一样等同于value一样）。
3. 用preg id+dependency信息来解决。motivation来自下图：
![[Pasted image 20230510164625.png]]
一个不相干的preg覆写会让rb里有用的信息失效。在加入dependency信息后只有在source instr被invalid后，才invalid掉这个entry。

### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
