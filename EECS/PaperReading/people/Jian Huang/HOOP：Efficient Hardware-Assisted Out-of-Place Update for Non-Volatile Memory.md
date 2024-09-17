---
Author: Miao Cai, Jian Huang
Year: 2020
Journel/Conference: MICRO
Summary: 结合LSNVM和shadow paging
Rate: 3
Question: None
Eureka: None
---
# Tags
#Non-volatile-memory, #NVM,  #out-of-place-update, #logging, #memory-persistency
# Authors

# Abstract

# Background
## Atomic Durability for NVM
atomic 对于一组数据的操作，要么发生要么没发生；方便错误时回滚
durability指的是data最终要存放在persistent storage里
cacheline的evict策略会影响atomic durability。但由于这个问题在现代计算机中不是很重要，所以没有被结局
## Crash-consistency Techniques
三种操作：
- logging。写old data前写一个log，会产生更多的write traffic，还可能引入更长的关键路径
- shadow paging。每次写入页的复制，浪费很多空间；最新的细粒度COR虽然解决了页复制开销，但会造成更多TLB开销
- log-structured NVM,LSNVM。将写以log形式存储，不需要写两次，log即data；但这样导致read时会有logN的复杂度。
![[Pasted image 20240917180640.png]]
HOOP类似LSNVM+shadow paging。写入一个lSNVM类型的新地方，然后异步地写入old data。
# Motivation
见bg。

# Solution
![[Pasted image 20240917181627.png]]
![[Pasted image 20240917181915.png]]
![[Pasted image 20240917182113.png]]
在store时，当data load到l1 cache后，对应cache line的persistent bit置一，然后cache controller会向下发修改过的data（绕过LLC）

#skip

# Evaluation


# Unsolved Question


# Related Works
## Later Works

## Previous Works

## Similar Works
