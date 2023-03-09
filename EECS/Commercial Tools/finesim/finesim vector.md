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