# Databases on Modern Hardware - How to Stop Underutilization and Love Multicores 读书笔记

- [1 Introduction](#1)
- [PART I Implicit/Vertical Scalability]
  - [2 Exploiting Resources of a Processor Core](#2)
    - Instruction and Data Parallelism
    - Multithreading
    - Horizontal Parallelism
  - [3 Minimizing Memory Stalls](#3)
    - Workload Characterization for Typical Data Management Workloads
    - Roadmap for this Chapter
    - Prefetching
    - Being Cache-conscious while Writing Software
    - Exploiting Common Instructions
    - Conclusions
- [PART II Explicit/Horizontal Scalability]
  - [4 Scaling-up OLTP](#4)
  - [5 Scaling-up OLAP Workloads](#5)
- [PART III Conclusions]
  - [6 Outlook](#6)
  - [7 Summary](#7)


&nbsp;   
<a id="1"></a>
## 1 Introduction

### 1.1 Implicit/Vertical Dimension

<img src="assets/Fig1.1.png" width="540"/>

- **充分压榨单核性能**（同时受限于散热）
- aggressive microarchitectural features
- instruction/data level parallelism
- memory hierarchy (memory stall, data intensive workloads)
- instruction: compiler optimization, prefetching, computation spreading, txn batching
- data: cache-conscious, partitioning, thread scheduling

### 1.2 Explicit/Horizontal Dimension

<img src="assets/Fig1.2.png" width="540"/>

- **充分利用多核扩展性**（同时受限于核间通信）
- socket communication, shared LLC, NUMA, interconnect bandwidth
- TP: 挑战在于 communication，本文提出 unbounded/fixed/cooperative 尽可能降低 communication
- AP: latency, bandwidth, NU shared everything, topology-aware
  - across-core scheduling：减少 across-core access
  - data placement：减少 latency
  - 这两者互相影响

### 1.3 Structure of the Book

- 传统执行模型怎么充分利用硬件
- 最大化利用 data/instr locality
- 在 NU 场景下怎么 scale-up


&nbsp;   
<a id="2"></a>
## 2 Exploiting Resources of a Processor Core

<img src="assets/Fig2.1.png" width="540"/>

### 2.1 Instruction and Data Parallelism

<img src="assets/Fig2.2.png" width="540"/>
<img src="assets/Fig2.3.png" width="540"/>
<img src="assets/Fig2.4.png" width="540"/>

- cpu pipeline: [Processor Microarchitecture an Implementation Perspective 读书笔记](https://github.com/rsy56640/Computer_Architecture_learning/tree/master/Processor%20Microarchitecture%20An%20Implementation%20Perspective%20%E7%AC%94%E8%AE%B0)
- 串行，pipeline，superscalar（同时发射多条指令）

<img src="assets/Fig2.5.png" width="540"/>
<img src="assets/Fig2.6.png" width="540"/>
<img src="assets/Fig2.7.png" width="540"/>
<img src="assets/Fig2.7.1.png" width="540"/>
<img src="assets/Fig2.8.png" width="540"/>

### 2.2 Multithreading

<img src="assets/Fig2.9.png" width="540"/>
<img src="assets/ht_partitioned.png" width="720"/>
<img src="assets/ht_shared.png" width="720"/>
<img src="assets/Fig2.10.png" width="540"/>

- microarchitectural 资源分割（issue queue, ROB, load/store buffer），对于计算密集型 workload 并不适合
- 资源共享：cache, memory bus，可能导致 cacheline thrashing
- 2.10a：不同 task
- 2.10b：相同 task，但是 MT 处理不同部分数据，instr/data *upper-level* cache 友好，同时切 MT 避免 memory stall。缺点是需要重构软件代码。

<img src="assets/ht.png" width="1080"/>

- 使用 HT 的场合：
  - 软件本身可以做 HT-awared 的调度与计算
  - 更明确主动地去利用 cpu 计算资源

<img src="assets/Fig2.11.png" width="540"/>

- master 负责真正的计算；helper 负责 data preloading
- master 通过 work-ahead-set 来通知 helper 接下来需要哪些数据
- 与单纯 prefetching 的区别？
  - 论文中说明：TLB miss 会导致 prefetch 被舍弃，而 helper 是显式触发 load；并不试图降低 total cache miss，而是试图将 cache miss 移出计算线程。
  - 实际上 ia32 optimization 上面说是 page fault

### 2.3 Horizontal Parallelism

<img src="assets/Fig2.12.png" width="540"/>
<img src="assets/Fig2.13.png" width="540"/>
<img src="assets/Fig2.14.png" width="540"/>

- 一个 core 负责同一块数据，不同的计算
> - 笔者注：最好计算比较轻，如果计算涉及到大量内存访问，那可能还不如第一种

#### 2.3.1 Horizontal parallelism in advanced database scenarios

##### Horizontal parallelism in sorting

<img src="assets/Fig2.15.png" width="540"/>
<img src="assets/Fig2.16.png" width="600"/>

- bitonic sort/merge/shuffle
  - 参考 [Fast Sort on CPUs and GPUs: A Case for Bandwidth Oblivious SIMD Sort 论文阅读笔记](https://github.com/rsy56640/paper-reading/tree/master/%E6%95%B0%E6%8D%AE%E5%BA%93/content/Fast%20Sort%20on%20CPUs%20and%20GPUs%20-%20A%20Case%20for%20Bandwidth%20Oblivious%20SIMD%20Sort)
  - `bitonic_merge+`：将Λ或V的数组递增排序
  - `shuffle+`：交换小的放在前面

```c++
// "Λ"/"V" -> "/"
void bitonic_merge+(v[0 : n]) {
  if (size == 1) return;
  shuffle+(v[0 : n]); // "Λ"/"V" -> "∧∨"
  bitonic_merge+(v[0 : n/2]);
  bitonic_merge+(v[n/2 : n]);
}

// "Λ"/"V" -> "∧∨"
void shuffle+(v[0 : n]) {
  if (size == 2) sort then return;
  if ("Λ") {
    swap(v[n/4 : n/2], v[3n/4 : n]);
  } else {
    swap(v[0 : n/4], v[n/2 : 3n/4]);
  }
}
```

<img src="assets/Fig2.17.png" width="720"/>

- N -> M：将数据分成大小为 M 的块，M 可以 reside in cache
- M -> k：每一块分成大小 k 的总共 P=M/k 份数据，k 是 SIMD width，进行 bitonic sort
- k -> M：对大小为 `k * 2^i` 的数据 merge 时，需要 `2^(i+1)` 个线程。input 每次按照**两个有序数组2等分点**切分，总共切分 `2^(i+1) - 1` 次，共 `2^(i+1)` 份，每个线程输出大小为 k 的块，所有块是有序的。
- M -> N：同上
> - 笔者注：直接递归 bitonic sort 和这个比较？

##### Horizontal parallelism in adaptive indexing

<img src="assets/Fig2.18.png" width="540"/>
<img src="assets/Fig2.19.png" width="540"/>
<img src="assets/Fig2.20.png" width="540"/>

- partition cracking 然后 merge，大量 cache miss
- 按 pivot 两边比例分，能够显著减少 merge 阶段数据移动操作
  - 是否需要先扫描统计一遍

#### 2.3.2 Conclusions

- single thread: instr/data level parallelism, SIMD, hyperthreading
- multithread


&nbsp;   
<a id="3"></a>
## 3 Minimizing Memory Stalls

表现为两个方面：

- memory access dependency
- high data/instr footprint

<img src="assets/Fig3.1.png" width="540"/>

- pipeline 设计可以掩盖 L1 latency，更下层的内存访问需要尽可能避免

### 3.1 Workload Characterization for Typical Data Management Workloads

<img src="assets/Fig3.2.png" width="540"/>

<img src="assets/Fig3.3.png" width="540"/>

- Fig3.2 观察：IPC 只有 1，实际上 Xeon X5670 可以达到 4；超过 50% 的时间在 memory stall
- Fig3.3 观察：传统 OLTP 场景，L1 instr miss + LLC data miss
  - TP code 很大，难以 fit into L1 icache
  - 数据密集型访问，缓存容量不足
- **data intensive workloads 通常难以利用现代处理器的激进特性，使得其面临大量 memory stall，并且降低了 IPC**

### 3.2 Roadmap for this Chapter

- memory stalls
  - **L1i miss**
    - 降低 instr footprint：指令流中减少 jmp；给出 large icache 的幻觉
  - **LLC data miss** (compulsory)
    - L1d 只要必要的 cacheline

### 3.3 Prefetching

#### 3.3.1 Techniques that are Common in Modern Hardware

- stream prefetch：触发某些条件，就 prefetch next cacheline
  - 适用于 sequential access，比如 instr (额外有 branch prediction)
  - [In which condition DCU prefetcher start prefetching?](https://stackoverflow.com/questions/53517653/in-which-condition-dcu-prefetcher-start-prefetching)
- stride prefetch：适合明显结构特征的 data access
- [Disclosure of H/W prefetcher control on some Intel processors](https://radiable56.rssing.com/chan-25518398/article18.html)

#### 3.3.2 Temporal Streaming

<img src="assets/Fig3.4.png" width="540"/>

- 基本观察：应用的访问行为会频繁重复，导致重复访问某个数据序列
- 硬件机制，实现代价过大

#### 3.3.3 Software-guided Prefetching

<img src="assets/Fig3.5.png" width="540"/>

- 数据结构上的 prefetch
- prefetch 函数头，之后依赖 next-line prefetcher 和 branch predictor
- 注意 cache thrashing & saturate bandwidth
- 实现上难以调优，通常是做完所有优化后采取的手段，而且需要大量的观测和调试

### 3.4 Being Cache-conscious while Writing Software

#### 3.4.1 Code Optimizations

- 优化软件栈
- 与编译器配合
- 运行时代码生成

#### 3.4.2 Data Layouts

<img src="assets/Fig3.6.png" width="540"/>

- next line prefetcher: NSM vs. DSM

<img src="assets/Fig3.7.png" width="540"/>

- DFS 适合 lookup
  - 每个节点的子树在内存布局上都紧跟在后面
- BFS 适合 scan
  - 每层节点顺序布局

#### 3.4.3 Changing Execution Models

<img src="assets/Fig3.8.png" width="540"/>

- data/instr locality

### 3.5 Exploiting Common Instructions

<img src="assets/Fig3.9.png" width="540"/>

- Fig3.9a/c L1i cache miss
- Fig3.9b batch
  - 牺牲 context switch, data locality, latency
- Fig3.9d pipeline
  - 牺牲 context switch, data locality
- 认为 L1i cache 收益更大

### 3.6 Conclusions

参考 3.2 roadmap


&nbsp;   
<a id="4"></a>
## 4 Scaling-up OLTP

<img src="assets/Fig4.1.png" width="540"/>
<img src="assets/Fig4.2.png" width="540"/>
<img src="assets/Fig4.3.png" width="540"/>
<img src="assets/Fig4.4.png" width="540"/>
<img src="assets/Fig4.5.png" width="540"/>
<img src="assets/Fig4.6.png" width="540"/>
<img src="assets/Fig4.7.png" width="540"/>
<img src="assets/Fig4.8.png" width="540"/>
<img src="assets/Fig4.9.png" width="540"/>



&nbsp;   
<a id="5"></a>
## 5 Scaling-up OLAP Workloads


<img src="assets/Fig5.1.png" width="540"/>
<img src="assets/Fig5.2.png" width="540"/>
<img src="assets/Fig5.3.png" width="540"/>
<img src="assets/Fig5.4.png" width="540"/>
<img src="assets/Fig5.5.png" width="540"/>
<img src="assets/Fig5.6.png" width="540"/>
<img src="assets/Fig5.7.png" width="540"/>
<img src="assets/Fig5.8.png" width="540"/>
<img src="assets/Fig5.9.png" width="540"/>
<img src="assets/Fig5.10.png" width="540"/>
<img src="assets/Fig5.11.png" width="540"/>
<img src="assets/Fig5.12.png" width="540"/>
<img src="assets/Fig5.13.png" width="540"/>
<img src="assets/Fig5.14.png" width="540"/>
<img src="assets/Table5.1.png" width="540"/>



&nbsp;   
<a id="6"></a>
## 6 Outlook


<img src="assets/Fig6.1.png" width="540"/>



&nbsp;   
<a id="7"></a>
## 7 Summary


<img src="assets/Fig7.1.png" width="540"/>


