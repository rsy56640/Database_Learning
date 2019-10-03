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
## [Chapter 5: Large-Scale Dataflow Engines](http://www.redbook.io/ch5-dataflow.html)


可扩展的数据处理需求


&nbsp;   
## [Chapter 6: Weak Isolation and Distribution](http://www.redbook.io/ch6-isolation.html)

弱并发控制下的性能提升，以及如何设计易于编程的语义模型


&nbsp;   
## [Chapter 7: Query Optimization](http://www.redbook.io/ch7-queryoptimization.html)

Eddies：运行时动态改变 operator 之间的数据流


&nbsp;   
## [Chapter 8: Interactive Analytics](http://www.redbook.io/ch8-interactive.html)

set-containment lattice

加速执行query的方法：并不访问所有数据

- precomputation
- sampling

tradeoff: accuracy <-> performance


&nbsp;   
## [Chapter 9: Languages](http://www.redbook.io/ch9-languages.html)


&nbsp;   
## [Chapter 10: Web Data](http://www.redbook.io/ch10-webdata.html)


&nbsp;   
## [Chapter 11: A Biased Take on a Moving Target: Complex Analytics](http://www.redbook.io/ch11-complexanalytics.html)


&nbsp;   
## [Chapter 12: A Biased Take on a Moving Target: Data Integration](http://www.redbook.io/ch12-dataintegration.html)
