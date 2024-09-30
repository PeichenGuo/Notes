# GreenRio2
![[Pasted image 20240930132325.png]]
两个queue：ldq stq
做TSO，即其中load会bypass store。
store会先进入stq等wakeup，load则可以直接进入agu。

五个kill：
- S1阶段如果出现missalign则kill掉当前指令
- S2阶段可能出现下面四种kill
	- ordering failure kill。在search的时候发现load bypass了前面的同地址store或load，这种情况下包括了bypass。
	- pma kill。做pma的时候发现这个pa是dma，kill掉cache访问
	- TLB miss kill。
	- pmp kill

四个resp data来源：
- DMA从IO读
- stq fwd
- dcache hit
- mshr resp

# Alpha 21264
见[[Alpha 21264]]的LSU章节