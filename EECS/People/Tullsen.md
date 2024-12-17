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

[[Half&Half：Demystifying Intel’s Directional Branch Predictors for Fast, Secure Partitioned Execution]]
2023
通过将两个互不信任的代码片段的pc值的一位（文中是PC[5]）强行设置成不一样的值，从而将他们的PHT分开，从而防止了specture类的攻击

### Indirector: High-Precision Branch Target Injection Attacks Exploiting the Indirect Branch Predictor
btb攻击