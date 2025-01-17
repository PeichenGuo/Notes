---
Chapter: 9
---
# Sign-off Timing
## Best Case-Worst Case (BC-WC) Timing 
max delay在worst case跑，比如ss_0.9_125这种（slow slow，0.9v vdd，125度）
min delay在best case跑，比如ff_1.1_0
但这么做没有考虑On Chip Variation(OCV)，传统来说会分capture/launch来设置不同的delay来达到最pessimism(悲观)的估计。
![[Pasted image 20240510122322.png]]
这种过于悲观的估计会影响设计时间，影响产出，影响开销，影响性能。因此需要一些方式来纠正pessimism
## Clock Reconvergence Pessimism Removal (CRPR)
#Q CRPR没搞懂
通过在clock path中remove derating来限制pessimism。


## Advanced On Chip Variation (AOCV)
insight:worst case condition in a path depends on:
- distance. path上gate间距离
- depth：path的stage数
![[Pasted image 20240510123431.png]]
aocv实际上是考虑了distance和depth后建的模，放在.lib里。这样ocv derating能准确一些

## Parametric On Chip Variation (POCV)
aocv still pessimism, 因此提出POCV
POCV就是SSTA应用的一种。对于每个gate给一个distribuion
![[Pasted image 20240510124051.png]]

## Path-based Analysis (PBA)
STA一般是Graph-Based Analysis(GBA)。但一般GBA是过于悲观的，因为是传播worst case，不管是rise还是fall（rise和fall的slew不一样）
PBA是根据path来跑，会考虑具体情况。
举个简单例子，比如一个反相器a链接一个and gate，输入x输出y。实际上x上升沿的时候，y只能下降或不变；x下降的时候，y只能上升或不变。如果反相器的worst slew是上升，and gate的worst slew也是上升，那么GBA算出的delay就是两个worst case之和，但实际上的时序路径只可能是反相器上升andgate下降或者反相器下降and gate上升。
GBA一定比PBA更悲观。但GBA一定不会别PBA跑得慢。通常而言PBA会慢一个数量级。
因此可以GBA先跑，有violation再用PBA跑violation的path
PBA也可以signal off时候再跑

## RC Extraction
因为实际上metal之间不是规律的平整的，wires之间可能更近或者更远，因此需要一些extraction options：
- Cworst: min metal space, tall wires, max surface
- Cbest: max metal space, short wires, min surface
- RCworst
- RCbest
![[Pasted image 20240510125840.png]]
![[Pasted image 20240510125949.png]]
具体用哪个case？
因为只有c影响cell delay，r不影响，因此可以Cworst for setup，Cbest for hold
RCworst和RCbest用在长interconnection上，因为此时不能忽略电阻带来的wire delay了

## Aging
解决方式
- 在low vdd运行减少aging效果。但性能会变差
- STA时delta margin大一点算，但会悲观。
- 用aged lib model跑STA来看aging后会不会有violation
- 用age sensors来调整芯片运行的frequency和voltage。
![[Pasted image 20240510130401.png]]
# Sign-Off
