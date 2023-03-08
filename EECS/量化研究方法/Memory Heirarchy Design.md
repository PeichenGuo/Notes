## Cache

### 10 advanced optimizations of Cache performance

#### Small and Simple First-Level Caches
- reduce hit time and power
- direct map has lowest hit time. 
- **as associativity grows, power and hit time grows.** 
- However, modern cache implement pipeline. Longer hit time do not impact directly on the clock cycle time. 
- 可以把cache按bank分开，从而每次访问只激活一个bank。l3很适合这个方法