---
Chapter: 5
---
# Intro
input：
- netlist
- floorplan
- tech file
output：all cells located in the floorplan
所有的cell应该是可以被routed

主要分两步：
- global placement：分为很多个bin，来minimize各个bin之间的connection
- detailed placement：给每个cell一个legal位置，尝试最小化wirelength和congestion。

# Random Placement
==skip==
