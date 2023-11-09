## Cache
6.75kb op cache
64byte cacheline
32kb l1,有prefetch。icache 32byte fetch
### L1I
- 32kb 
- 8-way
- 64byte cacheline
- 32-fetch 
- bpu会做一个类似prefetch的功能
- LRU
- 奇偶校验

## L1D
- 32kb 
- 8-way
- 64byte cacheline
- write back
- 3 memory operation per cycle.All three may be loads, but at most two may be 256-bit or 128-bit loads. Up to two may be stores, but at most one may be a 256-bit or 128-bit store.
- ECC校验
- prefetch
- int: 4/5 delay 
- fp: 6/7 delay
- load 64 byte边界，如果有跨边界ld，延时1
- store两种边界，tlb是64byte，写是32byte
- 跨页的ls有可能有较大的penalty
- banked
- aligned mem access 可以提升很多性能
- utag + way-predictor

### L2
- 8-way
- unified
- wb
- inclusive
- 1mb
- load-to-use < 14 cycle
- 

### L3
- shared
- 16-way
- 某种意义上exclusive: store和独占读invalide cache line
- 50 cycle的load-to-use

## Memory Translate
### L1 ITLB
- 全相连
- 64
- 4kb/2mb/1gb page

### L1 DTLB
- 全相连
- 72
- 4kb/16kb/2mb/1gb page

### L2 ITLB
- 8-way
- 512
- 4kb/2mb 1gb的page只在L1中被存储

### L2 DTLB
- 24-way
- 3072 
- 4kb/16kb/2mb/pde page 1gb的page只在L1中被存储

## Branch
misprediction：11-18 cycle
- next-address logic
- btb
- ras
- indirect target predictor
- _advanced conditional branch direction predictor_
- fetch window tracking

### next-address logic
### BTB
2 level BTB
L1 :
- 1536 entries
- no bubbles for direct branch, 1 cycle bubble for calls, returns and indirect branches

L2:
- 7680 entries
- 3 bubbles

### RAS
- 32 entry
- 大部分时间可恢复

### indirect branch predictor 
- 3072

### conditional branch predictor
- 只有有不同取向历史的branch会进入记录
- global history。
- _Global history is not updated for never-taken branches_
- 默认预测是not-taken。被taken后会进入btb，默认预测是taken。再次not taken后会进入dynamic state，用conditional branch predictor预测

### _Fetch Window Tracking Structure_
96 entry stack
==没看懂干啥的==

# LSU
3 pipeline
ldq：48个未结束的load和88个结束的load
stq: 64 entry
load bypass load/store
supports store-to-load forwarding
![[Pasted image 20231108214534.png]]
MAB类似MSHR，24个inflight miss

### Prefetch
L1： 
- stream：连着取
- stride：等差取
- region：局部pattern

L2：
- stream：连着取
- Up/Down: 决定要不要取

## Merge buffer

_Advanced dynamic branch prediction_

4-way decode _with sideband stack optimizer_

ooo+预测执行

5 exu
3 agu
4 fpu
二级tlb

6 pc

![[Pasted image 20231107234433.png]]
