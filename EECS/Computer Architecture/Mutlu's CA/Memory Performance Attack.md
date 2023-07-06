insight: 大核太复杂，所以多核。

3个问题：
1. 为什么slowdown
2. why disparity in slowdown
3. 怎么解决

为什么slowdown？
下面这个例子中matlab会产生更多的miss，如果不加以限制，dram controller会更倾向于让matlab访问dram，从而产生unfairness，会让gcc的性能大打折扣
![[Pasted image 20230706155418.png]]

DRAM的原理不再赘述。其处理request优先级有不同的方式，有些是rowhit first，即先处理hit的row；有些是old first，即fifo。
但rowhit first会让locality更好的程序优先，oldest first会让访问mem更多的程序优先。
