##### Look Up Table(LUT)
fpga里有
可以做基本的逻辑运算,其实就是存储，然后把对应的地址存上对应的内容，从而模拟一些组合逻辑

# Timing 
## 延时
input到output有延迟，这些延迟构成了timing
主要是由电容和电感的物理作用形成了延迟。[[RC延时模型]]

重要的是最长延迟和最短延迟
![[Pasted image 20230629163606.png]]
污染延迟$t_{cd}$，就是最短延时，也就是建立时间
传播延迟$t_{pd}$，就是最长延时，

## output glitches
不同的路径会得到不同的值
![[Pasted image 20230629164454.png]]
![[Pasted image 20230629164646.png]]
glitch并不是时刻都重要，因为可以被时序掩盖。

## 时序timing


# Verification 
