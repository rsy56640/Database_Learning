# [Readings in Database Systems 5th Edition](http://www.redbook.io/)

&nbsp;   
## [推荐阅读](http://www.redbook.io/all-readings.html)

&nbsp;   
## [Chapter 1: Background](http://www.redbook.io/ch1-background.html)

data model


&nbsp;   
## [Chapter 2: Traditional RDBMS Systems](http://www.redbook.io/ch2-importantdbms.html)

值得学习借鉴的设计

- System R
  - txn manager
  - optimizer (dynamic programming cost-based approach)
- Postgres
  - ADT
- Gamma
  - shared-nothing architecture


&nbsp;   
## [Chapter 3: Techniques Everyone Should Know](http://www.redbook.io/ch3-techniques.html)

- query optimizer
  - cost estimate
- concurrency control
  - 针对不同 workload，参考 [Staring into the Abyss: An Evaluation of Concurrency Control with One Thousand Cores 论文阅读笔记](https://github.com/rsy56640/paper-reading/tree/master/%E6%95%B0%E6%8D%AE%E5%BA%93/Staring%20into%20the%20Abyss%20-%20An%20Evaluation%20of%20Concurrency%20Control%20with%20One%20Thousand%20Cores)
- recovery
- distribution


&nbsp;   
## [Chapter 4: New DBMS Architectures](http://www.redbook.io/ch4-newdbms.html)

一些关于 OLAP column store 的老生常谈的话题

关于 Crash recovery：传统方法是 replicated log，恢复时从上一个 checkpoint 重新执行这行 txn；更快的是在每个 replica 上都运行 txn，而这需要 Deterministic-CC

关于 NOSQL：不需要复杂 schema；半结构化数据


&nbsp;   
## []()





&nbsp;   
## []()