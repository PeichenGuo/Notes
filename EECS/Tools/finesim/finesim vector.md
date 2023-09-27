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
or
```
mask mask_name mask_pattern
```

#### check_window
Specifies the time window of the vector strobe that is defined as an output of the io statement. The output comparison is done within the time window
![[Pasted image 20230309161456.png]]

第一种声明， the begin_offset and end_offset are the start and stop offset values. The default value and unit are 0 and ns.
The time window is \[t – begin_offset, t + end_offset\], t is vector stop time. 
steady 是1或0.
If steady=1 and the unexpected output range or time exists in the check window, the result is an error. 
If steady=0 and the expected output range or time exists in the check window, the result is correct.
If mask_name is specified, the check window is applied to the signals defined in the mask statement. mask可以选择signal

第二种使用方式：
The time window is \[t – begin_offset, t + end_offset\], where “t” is first_time.
The check will be repeated every period_time.
相当于定期检查

*steady*相当于维持在一个稳态？不管是低于VOL还是高于VOH都行

### Tabular Data
三种tabular data：
![[Pasted image 20230309163247.png]]
![[Pasted image 20230309163254.png]]
![[Pasted image 20230309163300.png]]
*没懂cascade是干啥的*
#### State
![[Pasted image 20230309163321.png]]
z是高阻抗 

#### period
可以快捷地生成周期激励
Defines the time interval of the tabular data. If a period statement is specified, the tabular data contains not only signal values but times.
![[Pasted image 20230309163503.png]]

#### “-”
In the period during which an output variable has ‘-‘ values, output comparison is disabled during simulation time. The ‘-‘ character in an IO vector file implies a **don’t care** region of out comparison checking.
用“-”表示的信号意思是不关心，不会被output检查。the output comparison will be disabled for the period of ‘-‘
*和x/ui有啥区别？*

A good example
```
; enable generation of expected output vectors and comparison result waveforms. output_wf 1 
; radix specifies the number of bit of the vector. 
radix 2 2 4 
; io defines the vector as an input or output vector. 
io i i o 
; vname assigns the name to the vector. 
vname A[1:0] B[1:0] P[3:0] 
; tunit sets the time unit. 
tunit ns 
; trise specifies the rise time of each input vector. 
trise 1 
; tfall specifies the fall time of each input vector. 
tfall 1 
; vih specifies the logic high voltage of each input vector. 
vih 2.5 
; vil specifies the logic low voltage of each input vector 
vil 0.0 
; voh specifies the logic high voltage of each output vector 
voh 2.0 
; vol specifies the logic low voltage of each output vector 
vol 0.5 
0 0 0 x 
200 3 3 x 
400 1 2 0 
600 2 1 9 
800 3 1 2 
1000 1 3 2 
1200 2 2 3 
1400 3 2 3 
1600 2 3 4 
1800 0 0 6 
2000 0 0 7
```