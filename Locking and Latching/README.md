# Locking & Latching

> 很多文献的说法都让人非常迷糊，一直搞不懂逻辑锁是怎么实现的。。。   
> 写这篇文章的目的是真正理解逻辑锁是如何实现的

主要讨论 lock（txn 层面的逻辑锁），总体分两类

- separate lock manager（常见于2PL）
- lock header，与 tuple 放一起（常见于 MVCC）

## Separate Lock Manager





### Intention Locking on B+-Tree



## Lock Header in MVCC







## Reference

- 《Fundamentals of Database Systems (7th edition)》 - Ch21
- 《Database Management Systems 2nd》 - Ch19
- 《Principles of Transaction Processing 2nd Edition》 - Ch6
- 《Modern B-Tree Techniques》 - Ch4
- 《Transaction Processing Concepts and Techniques》 - Ch7-8