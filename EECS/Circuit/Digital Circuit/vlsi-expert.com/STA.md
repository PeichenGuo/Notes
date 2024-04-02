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