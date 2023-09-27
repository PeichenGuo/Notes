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

# Extension

### debug flag1
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
