---
Author: "Subbarao Palacharla; Norman P.Jouppi; J.E. Smith"
Year: 1997
Journel/Conference: "ISCA"
Summary: "BlaBlaBla"
Rate: 5
Question: "None"
Eureka: "None"
---
### Abstract


### Motivation
对per cycle issue的追求和对更快时钟频率的追求是矛盾的。
前者势必增加硬件开销从而影响后者。
因此这个trade-off是architecture亘古不变的主题之一。
本文针对这个矛盾提出了一个complexity-effective microarhitecture

本文研究的对象拥有下面的特点：
1. delay是issue num的函数。这会导致在wide issue时不好用
2. dispatch和issue逻辑。这部分是ooo处理器的核心。
3. 依赖于boardcast的逻辑。这部分形成cam会导致不scaling

因此本文研究的对象：
1. Register Rename Logic
2. Wakeup Logic
3. Selection Logic
4. Bypass Logic

### Solution


### Evaluation
#### Register Rename Logic
Register Rename Logic如下图所示
![[Pasted image 20230517210943.png]]
其中dependence check logic是组合逻辑，不在critical chain上
map table的访问在critical chain上
Map table有两种实现方式：
1. RAM：每个areg对应分配的preg
2. CAM：每个preg对应分配的areg
CAM大多弱于RAM，因此本文只对RAM进行了研究
$$
T_{rename} = T_{decode} + T_{wordline} + T_{bitline} + T_{senseamp}\\
$$
decodeh和issue width没有关系，但剩下三个有。
$$
T_{wordline},T_{bitline},T_{senseamp} = c_0 + c_1 \times IW + c_2 \times IW^2
$$
最终其delay是这样的
![[Pasted image 20230517211835.png]]
observationa:
1. IW增长约线性
2. SRAM访问的三个延迟的占比上升

#### wake-up logic
$$
Delay = T_{tagdrive} + T_{tagmatch} + T_{matchOR}
$$
tagdrive是tag bit的驱动时间
$$
T_{tagdrive} = c_0 + (c_1 + c_2 \times IW)\times WINSIZE + (c_3 + c_4\times IW^2) \times WINSIZE^2
$$
可以看到 tag的驱动时间是winsize的二次方左右
tagmatch和matchor分别是tag比对时间和match后or的时间。
$$
T_{tagmatch},T_{matchOR} = c_0 + c_1 \times IW + c_2 \times IW^2
$$
![[Pasted image 20230517214844.png]]
可以看到在8发射的时候，已经能大概看出二次关系了

#### Selection
选择其实就是一个仲裁树，显然，仲裁树的延时是winsize的对数函数
$$
T_{selection} = c_0 + c_1 \times log_4(WINSIZE)
$$
#### Databypass
bypass线路数量是和issue number成平方关系
bypass可以理解为一个简单的RC延时模型
$$
T_{bypass} = 0.5 \times R_{metal} \times C_{metal} \times L^2
$$

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
