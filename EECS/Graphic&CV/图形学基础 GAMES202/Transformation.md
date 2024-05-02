---
Chapter: 3
---
变换：
model transormation
viewing transformation:3d->2d projection

# 2D Transformation
## Scale 缩放
![[Pasted image 20240430174859.png]]
写成矩阵：非均匀缩放
![[Pasted image 20240430174941.png]]

## Reflection
![[Pasted image 20240430175043.png]]
## Shear 切变
![[Pasted image 20240430175220.png]]

## rotate 
![[Pasted image 20240430175616.png]]
在推导rotation的时候有一个关键insight：**只需要找到关键点就可以推到trans公式**
这里找的关键点是图中标黑的两个点，因为其一个是(x,0),一个是(0,y)，可以直接观察变换对x和y的变化
指的注意的是 旋转矩阵是一个正交矩阵（转置等于求逆）
![[Pasted image 20240430181637.png]]
## Translation 平移
![[Pasted image 20240430175855.png]]
## homogenous coordinates 齐次坐标
平移不是线性变化，怎么办？ **引入新的维度**，形成**齐次坐标**
![[Pasted image 20240430180007.png]]
2d vec通过最后一位是0保证平移不变性
另一种对2d point和2d vec最后一位1和0的讨论：
![[Pasted image 20240430180443.png]]
点加点是两个点的中点

## Affine map 仿射
![[Pasted image 20240430180645.png]]
![[Pasted image 20240430180750.png]]
仿射变换只需要存6个数，最后一行001没有意义。在其他变换中最后一行可能有意义。

## Inverse Transform
就是逆矩阵

## Composite Transform 组合
![[Pasted image 20240430181145.png]]

# 3D Transform
仍有homogenus coordinates
![[Pasted image 20240430181406.png]]
![[Pasted image 20240430181438.png]]
## 3d旋转
![[Pasted image 20240430181802.png]]
这里Ry不太一样，why？
这里得看旋转角$\alpha$的定义。x的旋转角定义是y的正半旋转到z的正半，是逆时针旋转；z的旋转角定义是x的正半旋转到y的正半轴，是逆时针旋转。
而y的旋转角是x的正半轴旋转到z的正半轴，对于y而言是**顺时针旋转**。
根据右手定则，此时旋转得到的y轴是方向向下，因此此处定义的旋转方向是反的，因此实际上y轴的旋转角在几何意义上是x轴和z轴的负角度，因此在角上得加一个负号，形成了如图的Rz

怎么处理任意旋转？分解成三种旋转。
Rodrigues' Rotation Formula
![[Pasted image 20240430185529.png]]
n是旋转轴，alpha是旋转角

# View / Camera Transformation
![[Pasted image 20240430190034.png]]
相机的位置 相机看的方向 相机的方向 三元素
![[Pasted image 20240430190205.png]]
为了简化，让object动。相机固定在原点，y轴是up，看向-z方向
![[Pasted image 20240430190517.png]]
先求旋转的逆变换，再求逆。求逆很简单，就是转置。