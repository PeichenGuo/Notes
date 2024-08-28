---
Book: Semiconductor Memory Device and Circuit
Author: Shimeng Yu
Summary: BlaBla
---
# 1. Semiconductor Memory Technology Overview 

# 2. Static Random Access Memory
## 2.1 6T Cell
![[Pasted image 20240826112828.png]]
![[Pasted image 20240826112914.png]]
一个6t一bit信息
### 读
先由precharger充到vdd。然后断开充电。此时BL线就像电容一样。然后wl打开后，bl里的电就会失去平衡，如下图。此时左边的反相器是0，右边的方向器是1。打开后BL的电就会溜走，非bl的电会留着，从而产生一个电压差。
![[Pasted image 20240827205001.png]]
在一段时间后，sense amplifier启动。这个电压差可以被察觉到，然后输出结果。
![[Pasted image 20240827211800.png]]
### 写
![[Pasted image 20240827212242.png]]
写的时候往bl里充电，然后打开VDD。然后bit翻转，然后再关上gate。