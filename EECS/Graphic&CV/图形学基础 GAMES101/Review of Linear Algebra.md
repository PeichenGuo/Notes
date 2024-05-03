---
Chapter: 2
---
# Dependencies
![[Pasted image 20240430153655.png]]
# vector
![[Pasted image 20240430153913.png]]
图形学常用单位向量  $\hat{a}$ 

## dot product
![[Pasted image 20240430154219.png]]
点乘得到数
常用来算夹角
![[Pasted image 20240430154413.png]]
图形学里两个常用用处：
1. 算angle
2. 算projection
3. 算两个向量有多近
4. 算两个forward/backward
![[Pasted image 20240430154616.png]]
![[Pasted image 20240430154717.png]]
点乘大于0则forward，否则backward，等于零垂直

## cross product
![[Pasted image 20240430154930.png]]
c垂直于ab
右手螺旋定则：axb = c 从a旋转到b，拇指的方向就是c
因此 axb = -bxc
方便建立坐标系，有xy算z
![[Pasted image 20240430155145.png]]
一个坐标系里如果$\vec{x} \times \vec{y} = \vec{z}$ 则是右手坐标系 反之是左手坐标系
![[Pasted image 20240430155412.png]]
图形学里的作用：
1. 判定左右
2. 判定inside outside
![[Pasted image 20240430155616.png]]
想象yx是一个平面，这里叉乘得到的z如果是正的，则b在a左侧，反之在右侧
![[Pasted image 20240430155718.png]]
判断p点在三角形abc内还是外。先判断ap在ab的左侧还是右侧，再判断bc，再判断ac。如果都在一侧(比如按照ab bc ca的顺序是都在左侧，如果按照ac cb ba的顺序就是都在右侧)，则在三角形内部；否则在外部

# Matrices
![[Pasted image 20240430160325.png]]
mxn，m是行数，n是列数。也就是以左上角为00，纵坐标是m，横坐标是n
![[Pasted image 20240430160539.png]]


