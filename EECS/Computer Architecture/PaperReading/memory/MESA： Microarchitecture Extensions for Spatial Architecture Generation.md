---
Author: "[[Nam Sung Kim]]"
Year: 2023
Journel/Conference: ISCA
Summary: 用DBT的方式监视并自动配置spatial accelerator
Rate: 4
Question: None
Eureka: None
---
### Authors
[[Hadi S. Esmaeilzadeh]]
[[Nam Sung Kim]]

### Abstract


### Motivation
1. 现代处理器有很多accelerator在闲置，可以利用
2. 用dynamic binary translation来解决spatial accelerator不方便实用的问题

### Solution
MESA三个作用：
1. 监视cpu看有没有什么可以加速
2. 把binary换成data flow graph然后映射到spatial accelerator
3. 迭代优化spatial accelerator
![[Pasted image 20231015134840.png]]
总共有三步：
![[Pasted image 20231015142146.png]]
过程如下：
![[Pasted image 20231015142155.png]]

MESA的架构如下：
![[Pasted image 20231015142428.png]]

==具体没看==
### Evaluation


### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
