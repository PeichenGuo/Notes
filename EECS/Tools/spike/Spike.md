https://zhuanlan.zhihu.com/p/137575836 这篇写的不错
![[Pasted image 20231204130634.png]]
pk是轻量级kernel，application是riscv程序，spike是simulation程序，fesrv是host的代理。
pk和fesrv之间是靠HTIF来传递信息
# 代码解析
## processor.cc
processor.h的take_interrupt()可以处理interrupt
### decode_insn(insn_t insn)
可以decode。返回一个函数指针。
具体做法是查看一个opcode_cache,如果match就返回cache，如果没match就覆盖那行。这个不重要，主要是会返回对应行的一个insn_desc_t对象`return desc.func(xlen, rve, log_commits_enabled)`，里面存着insn_func_t
```cpp
struct insn_desc_t
{
insn_bits_t match;
insn_bits_t mask;
insn_func_t fast_rv32i;
insn_func_t fast_rv64i;
insn_func_t fast_rv32e;
insn_func_t fast_rv64e;
insn_func_t logged_rv32i;
insn_func_t logged_rv64i;
insn_func_t logged_rv32e;
insn_func_t logged_rv64e;
  
insn_func_t func(int xlen, bool rve, bool logged)
{
if (logged)
	if (rve)
		return xlen == 64 ? logged_rv64e : logged_rv32e;
	else
		return xlen == 64 ? logged_rv64i : logged_rv32i;
	else
	if (rve)
		return xlen == 64 ? fast_rv64e : fast_rv32e;
	else
		return xlen == 64 ? fast_rv64i : fast_rv32i;
}
// ...
};
```
函数指针`typedef reg_t (*insn_func_t)(processor_t*, insn_t, reg_t);`，这个函数可以根据传入的processor，instru和pc，返回一个reg_t

## mmu.h
里面有一个小icache，也是为了方便。
里面还有个tlb
### refill_icache
icache miss后的处理，会返回refill的cacheline
里面

### translate_insn_addr
`inline tlb_entry_t translate_insn_addr(reg_t addr)`可以做vpn到ppn的转化
返回一个tlb_entry_t
```cpp
struct tlb_entry_t {
	char* host_offset;
	reg_t target_offset;
};
```
如果tlb没有，就需要使用fetch_slow_path()fetch一个

## excution.cc
### step
excution.cc的step()会让cpu前进几步
里面分slow path和fast path。
slow里load_insn获得指令，execute_insn_logged执行
fast里access_icache获得指令，execute_insn_fast执行

