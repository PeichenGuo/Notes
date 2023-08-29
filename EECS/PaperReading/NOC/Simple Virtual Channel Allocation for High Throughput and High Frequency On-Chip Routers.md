---
Author: "Yi Xu"
Year: 2010
Journel/Conference: "HPCA"
Summary: "BlaBlaBla"
Rate: 5
Question: "None"
Eureka: "None"
---
### Abstract
两种virtual channel allocation (VA) 机制： Fixed VC Assignment with Dynamic VC Allocation (FVADA) 和 Adjustable VC Assignment with Dynamic VC Allocation (AVADA)

### Motivation


### Solution
basic design是[[A Low Latency Router Supporting Adaptivity for On-Chip Interconnects]]的两级流水

VC用一个table来管理
其中的一个entry如下图
![[Pasted image 20230829134912.png]]
RP和WP是read pointer和write pointer。buffer是个ring buffer，如果在一次写后，wp=rp，说明buffer full了；如果一次读后，wp=rp，说明buffer空了。
status说明vc在哪个stage
Pre route 是downstream port
OVC是downstream的VCID

VA+SA是bottle neck。大的arbiter在面积、功耗和delay上显著高于小的，因此本文要做小arbiter。FVADA是4VC port，AVADA是2-5port

两个arbitor特征：
1. VCnumber小，因此arbitor小
2. 为了让VC尽快被释放，body和tail flit要优先于head做allocation

#### **FVADA**
FVADA给每个inputVC map到一个output port上。比如East input有4个VC，分别是W N S和PE。不同目的地的packet会放在不同的VC里。这样做的好处是，如果其中一个方向的downstream卡了，不会block其他方向

arbitration是2级的，第一级选vc的winner，第二级选从winner中选可以被发出去的。因为VC和output port绑定，两次选中的vc一定映射到不同的output port。既不会让第一次选中后拖累第二次选中（第一次选中后对方busy了），也不会连续两次选中同一个busy的port。

由于每个VC只绑定一个，高负载下容易慢。因此当一个input发现home VC满了后，会找一个free的VC然后填进去，如果没找到free VC，就继续绑定home VC，只是此时会stall住

#### **AVADA**
就是input有很多VC，会后期映射到一个output port上

#### implementation


pipeline
![[Pasted image 20230829155729.png]]
Architecture
![[Pasted image 20230829155956.png]]


### Evaluation


### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
