****# [CMU 15-721 Advanced Database Systems (Spring 2019)](https://www.youtube.com/playlist?list=PLSE8ODhjZXja7K1hjZ01UTVDnGQdx5v5U)

课程配套的 notes 很全，配套论文阅读：[Schedule - CMU 15-721 Advanced Database Systems (Spring 2019)](https://15721.courses.cs.cmu.edu/spring2019/schedule.html)

- [01 In-Memory Databases](#01)
- [02 Transaction Models & In-Memory Concurrency Control](#02)
- [03 Multi-Version Concurrency Control Design Decisions](#03)
- [04 Multi-Version Concurrency Control Protocols](#04)
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
## 03 Multi-Version Concurrency Control Design Decisions

SI：read the ***consistent*** snapshot that has been commited before te txn starts

**大量操作基于 CAS**

<img src="./assets/03_MVTO.png" width="500"/>

- `TXN-ID` 表示写锁，哪个 txn 正在占用
- 读操作不阻塞，只 CAS upadte `READ-TS`

<img src="./assets/03_MV2PL.png" width="500"/>

- `TXN-ID` 表示写锁，`READ-CNT` 表示读锁。这两个都是 32bit，共同组成一个 64bit，一起做 CAS

### Version Storage

<img src="./assets/03_append_storage.png" width="500"/>

- 如果 head 是 oldest，那么每次写都会遍历 version chain，head 索引是固定的
- 如果 head 是 newest，写操作不遍历 version chain，但是会 update 所有 head 的索引

<img src="./assets/03_time_travel_storage.png" width="500"/>

- main table 是 newest，写前把旧的 copy 到 time travel table

<img src="./assets/03_delta_storage.png" width="500"/>

- 只记录被修改的 column，省空间
- reconstruct 需要回溯

### GC

#### Tuple level GC

<img src="./assets/03_tuple_gc.png" width="500"/>

<img src="./assets/03_tuple_gc_2.png" width="500"/>

#### Transaction level GC

记录 read/write set

### Index Management

<img src="./assets/03_index.png" width="500"/>

### Design Decision

<img src="./assets/03_mvcc_design_decision.png" width="500"/>


&nbsp;   
<a id="04"></a>
## 04 Multi-Version Concurrency Control Protocols

<img src="./assets/04_hekaton_mvcc.png" width="500"/>

- txn 有两个 ts：begin-ts 和 commit-ts
- `txn@ts` 表示 txn 还未提交，但是 begin-ts 是 ts
- 先写入新的 tuple (begin-ts, infinity)
- 然后把上一个指针指向这个 tuple（CAS，这里失败说明 first-writer-wins）
- ！！！这时候这个 tuple 还是不可见！！！
- 将上一个 end-ts 写成 begin-ts
- 获取 commit-ts
- 更新上一个 end-ts 和这个 begin-ts

<img src="./assets/04_txn_lifecycle.png" width="500"/>

为了支持 repeatable read 甚至 serializability，维护一些 metadata：

<img src="./assets/04_txn_metadata.png" width="400"/>

### commit

- Optimistic
  - 检查 version read 仍然可见
  - 检查所有 scan 是否有 phantom
- Pessimistic
  - 读写锁
  - 不需要 validation
  - detect deadlock


有一个挺不错的优化（针对 delta storage）：Precision Locking

- 检查 validation phase 的 read sets 是否 phantom，即对于那些在 start-ts 之后 commit 的 txn 的 write sets
- 只保留 read predicate

<img src="./assets/04_precision_lock.png" width="500"/>

还有很多优化和 trade-off，太细了，不写了。


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
