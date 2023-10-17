# 概念
## 基本概念
**基本块**：内部没有出入口的程序块
15%-25%的指令是if，因此基本块大不了，因此基本块内的ILP提升不大，因此我们要将ILP做到块间并行

**循环级并行**：尝试在循环迭代间并行
比如循环展开这种
```cpp
for (int i = 0; i < 1000; i ++)
	x[i] += y[i]; // 这个是完全可以并行执行的
```
本章主要讲循环级并行

与之相对的是数据级并行[[DLP]]


# ILP的基本编译器技术
## 基于流水线调度和循环展开

## 分支预测
### 2-bit
用其他branch的结果做预测的叫Correlating Predictor
可以看出4086entry就接近理想性能，1024entry也不会车差很多
![[Pasted image 20231017154203.png]]

### Tournament predictor
多个predictor竞争
![[Pasted image 20231017154420.png]]
可以看出tournament相比另外两个有进步，但没有明显进步。
![[Pasted image 20231017154611.png]]
### Tagged Hybrid Predictor

