# [The Design and Implementation of Modern Column-Oriented Database Systems](https://dl.acm.org/doi/10.5555/2602024) 笔记

> 本书并不深，主要讨论 Design Space

- [1 Introduction](#1)
- [2 History, trends, and performance tradeoffs](#2)
- [3 Column-store Architectures](#3)
- [4 Column-store internals and advanced techniques](#4)
- [5 Discussion, Conclusions, and Future Directions](#5)


&nbsp;   
<a id="1"></a>
## 1 Introduction

- [The design and implementation of modern column-oriented database systems - the morning paper](https://blog.acolyer.org/2018/09/26/the-design-and-implementation-of-modern-column-oriented-database-systems/)

<p/><img src="assets/Fig1.1.png" width="600"/>

- offset 寻址 record, fixed-width（考虑 compression）
- block processing, cpu utilization
- late materialization, memory bandwidth
- compression
- 直接在 compressed data 上做计算
- join
- sorted column
- I/O pattern: write optimized memory buffer -> batched  flush compressed column

<p/><img src="assets/Fig1.2.png" width="600"/>


&nbsp;   
<a id="2"></a>
## 2 History, trends, and performance tradeoffs

### 2.1 History

<p/><img src="assets/Fig2.1.png" width="600"/>

### 2.2 Technology and Application Trends

- disk 演进（capacity, transfer, seek time）导致 memory access pattern 变化
- cpu frequency 快于 memory access
- 实践
  - MonetDB
  - PAX: NSM page is organized as columns
  - Fractured Mirrors: both NSM and DSM
  - SybaseIQ
  - C-Store / VectorWise

### 2.3 Fundamental Performance Tradeo!s

<p/><img src="assets/Fig2.2.png" width="600"/>

DSM storage model against scan workload

- projectivity
- SSD：缓解 random read
- low selectivity：reconstruct 少读了很多，NSM 还是要读每个 tuple


&nbsp;   
<a id="3"></a>
## 3 Column-store Architectures

### 3.1 C-Store

<p/><img src="assets/Fig3.1.png" width="600"/>

- 以 column 为单位组织文件，compress，sort
- ROS: read optimized store
- WOS: write optimized store
- **projection**：在某一组 column 上进行 sort 的一个 column group
  - 意味着 column storage 会有冗余
  - 按 column 一级一级 sort
  - 不同形式的 projection 处理不同形式的 query
- compression method
  - sorted
  - data type
  - distinct value 的数量
- sparse index
  - 类似 btr，引入 internal node，加速二分
  - 也可以对 tuple id 进行 sparse index，当 compressed 或 variable-size attr
- update = delete mark + insert
- late materialization
- join
- batch processing
- MPP

### 3.2 MonetDB and VectorWise

#### MonetDB

<p/><img src="assets/Fig3.2.png" width="300"/>

- 列存计算
- 更加看重 cache miss，而不是 IO cost
- index
- optimization
- 尝试消除 interpretation 开销

#### VectorWise

- vectorized execution
- block of a column at a time
- I/O
- TP: Positial Delta Tree
- compression

### 3.3 Other Implementations

- Column Storage Only：仍使用 NSM execution engine，易于从原有架构演进
- Native Column Store Design：将一套 column store engine 集成到原有架构
- IBM BLU/BLINK
- Microsoft SQL Servere Column Indexes


&nbsp;   
<a id="4"></a>
## 4 Column-store internals and advanced techniques

### 4.1 Vectorized Processing

- interpretation overhead
- cache locality
- compiler optimization
- block algorithm
- parallel memory access: hide latency
- profiling: amortized overhead
- adaptive execution: runtime statistics
  - based on high/low selectivity
  - different vectorized impl
- query layout

### 4.2 Compression

- column compression
  - same pattern
  - compact, simple impl
  - sorted
- cpu utilization
  - reduce storage space
  - cpu/memory bandwith is faster: perfer compressed data
- fixed-width array & SIMD
  - prefer light-weight compression schemes
      - compress data into fixed-width dense array
      - SIMD processing on compressed data
- frequency partition：目标是提升 compression ratio
  - frequent value 放到一个 page，对该 page 进行 dictionary compreesion
- compression algorithm

#### 4.2.1 Run-length Encoding

- 对连续的相同数据编码
- sorted consecutively
- reasonable-sized run of the same value
- 结果是 variable-length column，考虑 tuple reconstruction

#### 4.2.2 Bit-Vector Encoding

- 最好有限数据值
- 为每一个 value 准备 bitmap
- bitmap indices

#### 4.2.3 Dictionary

- 有限数据值
- 将值序列映射为数字序列，数字序列还可以继续 compress
- per block dictionary
- 对 string 的计算转变成对 int 的计算

#### 4.2.4 Frame Of Reference (FOR)

- value locality，数据局部相关
- 取一个公共部分，然后记录 delta
  - 比如 leveldb 记录 key 的共享部分，然后分别记录 key 的非共享部分

#### 4.2.5 The Patching Technique

- 由于数据分布原因，只对最频繁的数据做 dictionary/FOR
- 引入 exception value 来表示 no compression

### 4.3 Operating Directly on Compressed Data

- 例如：RLE, SUM
- dictionary compression 使用 order preserving encoding
- query operator 需要感知 compressed data
  - compression block 基础设施和一系列规范接口，针对不同 compression schemes

### 4.4 Late Materialization

<p/><img src="assets/Fig4.1.png" width="600"/>

计算过程中使用 position list 表达 selectivity

如果使用 projection，有 filter 命中了 leading sorted column，并且其他要访问的 column 也都包含在 projection 内，那么访问对 cache 友好。

#### late materialization ([Materialization Strategies in a Column-Oriented DBMS](http://paperhub.s3.amazonaws.com/b96c89e3bc63ebb074728bb776b72e23.pdf))

- filter & aggregation 非必要开销（具体指什么？？还得看上面论文）
- 允许 process on compressed data
- cache, memory bandwidth
- vectorized execution
- 有可能更慢：比如 filter 访问了大部分 column

#### multi-column blocks

<p/><img src="assets/Fig4.2.png" width="480"/>

multi-column blocks：部分行，部分列

<p/><img src="assets/Fig4.3.png" width="600"/>

### 4.5 Joins

- materialization strategy
- sorted position list: access locality (block order)

### 4.6 Group-by, Aggregation and Arithmetic Operations

- group by
- aggregation
  - tight for-loops
  - memory bandwidth
- arithmetic operation

### 4.7 Inserts/updates/deletes

- column store, projection, replica 导致 IO 次数放大
- compression 无法增量 update

方案：in-memory write store

- column format
- row format
- Positional Delta Tree (VectorWise PDT)

### 4.8 Indexing, Adaptive Indexing and Database Cracking

- indexing
  - projection
  - zonemap：间隔的，轻量的 metadata
- database cracking & adaptive indexing
  - 逐渐分解成 pieces，逐步建立 index

<p/><img src="assets/Fig4.4.png" width="480"/>

根据 query 来 sort 部分，多次以后就建立起粗粒度的 sorted index

<p/><img src="assets/Fig4.5.png" width="480"/>

- presorted 需要提前长时间准备
- cracking 可以动态快速达到较优的水平

### 4.9 Summary and Design Principles Taxonomy

<p/><img src="assets/Fig4.6.png" width="720"/>


&nbsp;   
<a id="5"></a>
## 5 Discussion, Conclusions, and Future Directions

### 5.1 Comparing MonetDB/VectorWise/C-Store

- block execution
  - full materialization
  - late materialization: compressed
- load & update
  - deletion bitmap + in-memory write store
  - PDT
- compression
  - execution
- index 

### 5.2 Simulating Column/Row Stores

### 5.3 Conclusions

- adaptive storage layout
