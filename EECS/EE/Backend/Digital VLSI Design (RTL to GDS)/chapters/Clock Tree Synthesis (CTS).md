---
Chapter: 6
---
# Intro
timing constraints:
![[Pasted image 20240509201248.png]]
tcq是ffc到q的延迟，tlogic是组合逻辑延迟，tsetup和thold是ff的setup时间
delta skew和margin
## clock  parameters
skew：不同reg间clock arrival time的差别
jitter：同一个reg不同cycle间的差异
slew：transition time（trise/tfall)
insertion delay：source到ff的delay，一般讲average insertion delay
![[Pasted image 20240509201815.png]]