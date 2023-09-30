# 常用命令
run:
```
build/X86/gem5.opt configs/tutorial/part1/simple.py
```

debug flags
```
    build/X86/gem5.opt --debug-flags=DRAM configs/learning_gem5/part1/simple.py | head -n 50
```
dram是打memory交互

```
    build/X86/gem5.opt --debug-flags=Exec configs/learning_gem5/part1/simple.py | head -n 50
```
exec是打instruction

# CPP源码

### debug flag
1.在scons里添加：
```
DebugFlag('HelloExample')
```
2.include
```cpp
#include "base/trace.hh" 
#include "debug/HelloExample.hh"
```
3.DPRINTF
```
DPRINTF(HelloExample, "Created the hello object\n");
```

## SimOBJ
==**never use std::cout**==, use debug instead



_Functional mode_ is better described as _debugging mode_.会直接返回最新的data，忽略memory具体架构。（估计就是个数组）

### 初始化过程
<ol>
<li>SimObject::init()
<li>SimObject::regStats()
<li><ul>
<li>SimObject::initState() if starting afresh.
<li>SimObject::loadState() if restoring from a checkpoint.
</ul>
<li>SimObject::resetStats()
<li>SimObject::startup()
<li>Drainable::drainResume() if resuming from a checkpoint.
</ol>

### API 分析
#### create()
python 会自动调用simobj的create()去生成一个对象
有三种create()的方式
##### Foo(const FooParams &)
这个是最常见的，python会自动构建一个create
建议使用这种方式

##### Foo \*FooParams::create() const;
如果非要自己定义的话，可以用这种方式。这会override默认的create
有时候需要向下类型转换，把SimObjectParam变成DerivSimObjectParams，gem5提供了一个宏`PARAMS`：
```cpp
using Params = DerivSimObjectParams;
const Params &
params() const
{
return reinterpret_cast<const Params&>(_params);
}
```
#### init()
init() is called after all C++ SimObjects have been created and all ports are connected.
需要链接好再初始化的内容需要写到这里

#### loadState() & initState()
loadState() is called on each SimObject when restoring from a checkpoint.
initState() is called on each SimObject when *not* restoring from a checkpoint

#### Probe
probe产生trace，trace是event log。

#### getPort()
```cpp
virtual Port &getPort(const std::string &if_name, PortID idx=InvalidPortID);
```

#### startup()
最终的init环节。
适合schedule initial event(s)
```cpp
virtual void startup();
```

#### memWriteback() & memInvalidate()
Write back dirty buffers to memory using functional writes.
适合给带cache的system准备checkpoint
```cpp
virtual void memWriteback() {}
virtual void memInvalidate() {};
```

#### serialize
Create a checkpoint by serializing all SimObjects in the system.
```cpp
void serialize(CheckpointOut &cp) const override {};
void unserialize(CheckpointIn &cp) override {};
static void serializeAll(const std::string &cpt_dir);
```
## Port
port有三种mode：timing, atomic, and functional。timing是最常用的，可以得到正确的结果。
port分master和slave
![[Pasted image 20230930151729.png]]
master通过`sendTimingReq`发送一个`PacketPtr`。同理`sendTimingResp`。两个send函数的返回值都是bool，直接给对方发过去，表示收到并处理了send请求。
```cpp
bool sendTimingReq(PacketPtr pkt);

bool
sendTimingResp(PacketPtr pkt)
{
	try {
		return TimingResponseProtocol::sendResp(_requestPort, pkt);
	} catch (UnboundPortException) {
		reportUnbound();
	}
}
```

当master或者slave busy的时候，busy的一方需要再不busy的时候发送retry
![[Pasted image 20230930153634.png]]
![[Pasted image 20230930154222.png]]
