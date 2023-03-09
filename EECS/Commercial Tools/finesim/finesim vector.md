FineSim supports a tabular I/O-vector format to accommodate the digital simulation design environment

支持直接读VCD
```
.vcd2vec vcd_file signal_file
```
或者
```
.vcd2vec vcd_file signal_file
```
也可以用fscript把vcd转换为io_vector


## Vector File Format
An I/O vector file has three sections in the following order:
1.  Vector Pattern Definition
2.  Waveform Parameter Settings    
3.  Tabular Data

The vector pattern definition declares signal (vector) names, vector sizes, and vector types.
A vector type specifies whether the signal is an input or an expected output that should be used for comparison.

The waveform parameter settings define a variety of signal attributes.

The tabular data lists the values of input signals at specified times.
two ways to list:
1. the time may be listed in the first column, followed by signal values, in the order specified by the nodename statement of the vector pattern definition.
2. FineSim Pro provides the pwl_type definition you specify before the description of the tabular data but preceded by comment character ;

example:
```
; vector pattern definitions
radix
+ 	 1

nodename
+ 	 IN

io
+ 	 i

; waveform parameter setting
tunit 1.000000n
slope 1.000000 
vol   500.000000m
voh   2.000000 
vil   500.000000m
vih   2.000000 

; tabular data
                   0 0 
              1.2499 1 
                3.75 0 
              5.4999 1 
                 6.5 0 
              7.4999 1 
              8.4999 0 
                 100 0 
             1.1e+10 0 

```

### Vector Pattern Definition
#### Radix 
必选项
![[Pasted image 20230309154233.png]]
进制

#### Nodename
节点名称，类似signal name。
Single-bit signals have one NodeName, and multiple-bit signals (bus signals) have bus notation with \[i:j\], \[i-j\], \[i~j\], <i:j>, < i - j >, or <i~j>.
![[Pasted image 20230309154958.png]]
![[Pasted image 20230309155037.png]]
![[Pasted image 20230309155104.png]]

### IO statement
![[Pasted image 20230309155131.png]]

### waveform parameter setting
waveform 参数。 电压、步长之类的
#### Tunit 
time unit
default is 1ns

#### Slope
上升和下降时间。
可以用Tfall 和 Trise来重写覆盖
默认是1ps

#### Tfall and Trise
下降时间和上升时间
默认值是slope

VH 或 VOH
output为1的输出阈值电压，用来输出检测的
默认值是VIH/2
高于这个值算1

VL 或 VOL
output为0的阈值电压。用来输出检测的。
默认值是VIH/2
低于这个值算0

VIH 
Voltage In High
input为1的阈值电压，高于这个电压算1
必须设置，无默认值

VIL
Voltage In Low
input为0的阈值电压，低于这个电压算0
默认值是0

Mask
没懂啥意思
Sets a mask function for signals defined in the vector file. It works on all keywords such as vil/vih/trise/tfall/slope/tunit/delay. It is used when signals require special values other than the values defined globally.
he signals used in check_window statement also can be defined with mask.
```
 mask mask_name signal1 [signal2 signal3 ......]
```
