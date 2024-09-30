# Basics
见CAQA的[[Cache]]和[[超标量流水线设计]]的cache章节

# Picorio2
![[Pasted image 20240930142915.png]]
2r1w的cache
s0做arbitration ，准备读valid ram，tag ram和ewrq。

ewrq miss的情况下，s1读出tag vld后对比是不是hit，如果miss则进入mshr。如果hit，则更新lru。
否则是ewrq占据写，kill掉可能的inflight write。替换一个line进入ewq。