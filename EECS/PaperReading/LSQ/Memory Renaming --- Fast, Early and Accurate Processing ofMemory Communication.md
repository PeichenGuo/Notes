---
Author: "Gary S."
Year: 1999
Journel/Conference: "International Journal of Parallel Programming"
Summary: "BlaBlaBla"
Rate: 2
Question: "None"
Eureka: "None"
---
### Abstract
Memory renaming 用预测load stoer communication的方式提高memory operation的性能，从而更好的ooo执行load。本文方法检测store指令和load指令的依赖关系，然后把未来的communucation映射到一个value file的reg file里，从而让这部分的依赖像prf依赖一样被执行。
能达到不少的性能提升，最高41%。

### Motivation
memory dependency难以解决，但register的communication被解决的很好，因此本文尝试用reg的方式来解决mem的问题。

### Solution
本文解决方案分两步：
1. 预测store load的communication。详情见[[Dynamic Speculation and Synchronization of Data Dependences]]
2. 把store load映射到value map，并通过value map来解决依赖关系

当store和load被预测有关系的时候，这些load和store会被映射到一个value file上。之后的解决方案就和prf类似，比如一个load来发现其映射的vfreg valid，就会直接用这个reg里的值。
为了验证prediction正确性，且确保store可以改变memory，正常的访存操作会同时进行，并用多个周期后返回的值和vf提供的值进行对比。
![[Pasted image 20230422140457.png]]
这个表中可以看出，load store communication可预测性是有的，大约29%的SPECint和44%的SPECfloat的load指令使用的值和上次一样。后三列的locality表现的是，同一个load两次执行之间，其值、地址和producer的地址没有变，换句话说，上次映射的关系这次还能继续用。

==本文提供了很详细的流水线implementation，但鉴于其25年间引用数只有个位数，我就没看== 

### Evaluation
==没看==

### Unsolved Question


### Related Works
#### Later Works

#### Previous Works

#### Similar Works
