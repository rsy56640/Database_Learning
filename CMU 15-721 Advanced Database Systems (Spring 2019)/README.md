# [CMU 15-721 Advanced Database Systems (Spring 2019)](https://www.youtube.com/playlist?list=PLSE8ODhjZXja7K1hjZ01UTVDnGQdx5v5U)

课程配套的 notes 很全，配套论文阅读：[Schedule - CMU 15-721 Advanced Database Systems (Spring 2019)](https://15721.courses.cs.cmu.edu/spring2019/schedule.html)

- [01 In-Memory Databases](#01)
- [02 Transaction Models & In-Memory Concurrency Control](#02)
- []()
- []()
- []()


&nbsp;   
<a id="01"></a>
## 01 In-Memory Databases

没有 I/O 影响之后的瓶颈：

- concurrency control
  - Disk oriented DB 额外维护 lock/latch table
  - In memory DB 把 lock 和 tuple 放一起
- cache line miss
- pointer chasing (indirect layer)
- predicate evaluation：where clause（compilation）
- data copy
- logging and recovery

### Data Organization

<img src="./assets/01_in_memory_data_organization.png" width="400"/>

DB 自己管理 eviction/flush and cache policy


&nbsp;   
<a id="02"></a>
## 02 Transaction Models & In-Memory Concurrency Control

<img src="./assets/02_isolation_level_hierarchy.png" width="400"/>


&nbsp;   
<a id="03"></a>
##


&nbsp;   
<a id="04"></a>
##


&nbsp;   
<a id="05"></a>
##


&nbsp;   
<a id="06"></a>
##


&nbsp;   
<a id="07"></a>
##


&nbsp;   
<a id="08"></a>
##


&nbsp;   
<a id="09"></a>
##
