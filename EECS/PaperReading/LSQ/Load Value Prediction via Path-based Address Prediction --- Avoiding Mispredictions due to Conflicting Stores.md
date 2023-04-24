---
Author: "Rami Sheikh"
Year: 2017
Journel/Conference: "MICRO"
Summary: "BlaBlaBla"
Rate: 3
Question: "None"
Eureka: "None"
---
### Abstract
load的value prediction面对两个问题：
1. store会改变memory，让load predictor记住的值变stale
2. value mispredict会引发costly的pipeline flush
因此本文提出Decoupled Load Value Prediction (DLVP)。DLVP通过将value prediction替换为load address prediction来减轻stale。他通过一些投机取巧的方式通过address从dcache里获得对应的值，从而进行value prediction


### Motivation


### Solution


### Evaluation


### Unsolved Question


### Related Works
#### Later Works

#### Previous Works
[[Practical data value speculation for future high-end processors]]
#### Similar Works
