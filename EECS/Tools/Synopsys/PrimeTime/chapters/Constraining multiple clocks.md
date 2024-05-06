---
chapter: 4
---
# Objectives
1. How many kinds of clocks
2. What type and where are the clocks defined
3. Which clocks are interacting

# 3种时钟
## Primary
input pin上的

## Generated
propageted的
rise-rise和rise-fall不行，fall-rise和fall-fall可以
### 三种同步方式
#### 系统同步
两个组件共用系统时钟
![[Pasted image 20240504154143.png]]

#### source synchronous interface（源同步接口）
在PT workshop中介绍了这种同步方式
把时钟和数据一起发过去，这样方便接受方同步，减少了data和clk之间不匹配的问题
![[Pasted image 20240504154152.png]]
![[Pasted image 20240504154201.png]]

#### 自同步
同时发送clk和data
![[Pasted image 20240504154227.png]]
## Virtual
没有source的clk
serve as reference for input and output

# asynchronous, synchronous, and exclusive clock
同一个PLL(phased-lock loop)产生的时钟就是同步，不同pll产生的就 是异步
