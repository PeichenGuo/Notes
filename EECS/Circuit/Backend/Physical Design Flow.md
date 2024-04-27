# Video
https://www.youtube.com/watch?v=VXjkcHhYIr8
## Design Initialization
## PnR
innovas，icc2这种工具 

### System Partitioning
soc divided into convenient components
rare 在设计的时候就会partition
interconnection和pin assignment会在这个stage做

### Floorplanning & power planning
规划placement和power 会迭代进行

### Placement
minimize wire length

### Clock tree synthesis
clock tree 需要知道各个部件的摆放

### Routing
在不同的layer做connection

### Dummy cell and metal and oxide fill
dummy cell就是假的东西，不会通电，但为了良率会在空白处填dummy cell

### RC extraction
routing metal来

在placement后的每步都做STA来检查时序收敛，如果合格再做下一步

## 

### STA

### timing sign off
各种corner被考虑，noise之类的

## physical verification
### DRC
design rule check

### LVS 
layout vs schemetic 
layout has same function as schemetic

## Noise and Reliability
### EM 

### IR

