![[Pasted image 20240402133603.png]]
timing path delay可以分为两个，一个是cell delay, 一个是net delay
net delay is a function of:
- net resistance
- net capacitance
- net topology
有多种算rc的方式，lumped把wire看成一个大的电阻加两个电容
![[Pasted image 20240402133842.png]]
distributing把wire看成很多小的：
![[Pasted image 20240402133919.png]]
![[Pasted image 20240402133926.png]]
对于不同的model有不同的时间延迟
这里10%-90%指的是output voltage从10%input voltage升到90%input 所需要的时间是多少RC。RC指的是电阻×电容。

## setup-hold violation
对于两个ff之间的combination circuit的delay：
hold time of capture ff < delay < clock period - setup time of capture ff
所以我们可以推导出来
clock period >= setup time + hold time of capture ff

![[Pasted image 20240402183327.png]]
(T_capture - T_launch) + T_hold < Td < clk_period + (T_capture - T_launch) - T_setup
其中T_capture和T_launch是capture ff和launchff的时钟延迟，Td是组合逻辑的delay
上述不等式很简单，前面的小于号是，delay要大于hold时间，免得input变化在hold前就传播到；后面的小于号是说，clk period得最够大（T_capture - T_launch实际上是clk period实际有效的时间），在delay延迟后也足够setup

总结：
![[Pasted image 20240402183837.png]]
![[Pasted image 20240402183911.png]]
