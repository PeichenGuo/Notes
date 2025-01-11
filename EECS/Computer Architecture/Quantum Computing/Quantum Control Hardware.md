# RISCQ
RISCV extension for real-time quantum controlling
![[Pasted image 20250111200455.png]]
Wave in wave out
need adc and dac
process asap
112 Gbps to control a single bit 
==High Frequency==


![[Pasted image 20250111200847.png]]

使用RFSoC： SoC+adc+dac

![[Pasted image 20250111201140.png]]

need high level：
![[Pasted image 20250111201450.png]]
HLS？ I dont think so
Protocol and IR

HL HDL。 It is not that handy

HLS problem is the low frequency and undebugable 

# ARTIQ
他们搞了一套自己的DSL来FHDL，然后用这个东西做了一个叫做Migen的python base toolsits来构建硬件
然后再用MiGen做了一个MiSoC来控制qbit

他们还有一个叫做 Milkymist的开发板，用的sparta 6，主频83M

# QubiC
部署在一个Xilinx ZCU216 RFSoC上，大概16000刀。相当于一个UltraScale FPGA加上两个5G的ADC/DAC，价格比较fair