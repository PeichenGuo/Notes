## Cache

### 10 advanced optimizations of Cache performance

#### Small and Simple First-Level Caches
- reduce hit time and power
- direct map has lowest hit time. 
- **as associativity grows, power and hit time grows.** 
- However, modern cache implement pipeline. Longer hit time do not impact directly on the clock cycle time. 
- 可以把cache按bank分开，从而每次访问只激活一个bank。l3很适合这个方法

#### way prediction
hit time --
2-way 90% accurate
4-way 80% accurate
when prediction success, lower hit time and lower energy consumption 
when fail, extra cycle and same energy consumption
难以pipeline
性能提升不多，但有很大的能耗提升

#### pipelined access and mutibanked caches
bandwidth ++
pipeline能增大带宽，提高频率。但会增大hit lantency。

多个bank可以并行提供数据，实现并行访问。理想情况是多个mem req映射到不同的bank上。所以bank mapping很重要。
以最简单的bank mapping sequencial interleaving为例。这种mapping是把相邻的地址映射到不同的bank上。比如addr mod 4 然后分配到四块bank上。这样相邻的访问就会分散到不同的bank上

#### nonblocking cache
bandwidth ++
非阻塞缓存有较大提升。
实现nonblocking的时候会遇到两个问题。1.如何仲裁ls的先后。什么样的可以bypass。2.如何handle乱序返回的miss resp——引入了MSHR。

#### critical words first and early restart
miss penalty --
处理器需要的内容往往只是一个word，因此可以有两种优化：
critical word first: 先请求需要的，然后立刻返回它，然后再请求剩下的
early restart：正常请求，当请求到需要的内容时立刻返回
cacheline越大 bonus越大