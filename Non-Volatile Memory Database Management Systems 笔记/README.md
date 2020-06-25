# Non-Volatile Memory Database Management Systems 笔记

- [1 Introduction](#1)
- [2 The Case for a NVM-Oriented DBMS](#2)
- [3 Storage Management](#3)
- [4 Logging and Recovery](#4)
- [5 Buffer Management](#5)
- [6 Indexing](#6)
- [7 Related Work](#7)
- [8 Future Work](#8)
- [9 Instant Restore after a Media Failure](#9)
- [APPENDIX A: Non-Volatile Memory Emulation](#10)
- [APPENDIX B: Benchmarks](#11)
- [APPENDIX C: H-Store DBMS](#12)


&nbsp;   
<a id="1"></a>
## 1 Introduction

<p/><img src="assets/Table1.1.png" width="660"/>

- byte addressability
- high write throughput
- read-write asymmetry

<p/><img src="assets/Table1.2.png" width="660"/>


&nbsp;   
<a id="2"></a>
## 2 The Case for a NVM-Oriented DBMS

### 2.1 Persisting Data on NVM

<p/><img src="assets/Fig2.1.png" width="600"/>

- CLWB(cacheline writeback) 将修改过的 cacheline 写到 WPQ(write pending queue)，*which is power safe*
- SFENCE
  - *"CLWB instruction is ordered only by store-fencing operations. For example, software can use an SFENCE, MFENCE,
XCHG, or LOCK-prefixed instructions to ensure that previous stores are included in the write-back."* - 《IA32 SDM》

<p/><img src="assets/CLWB-order1.png" width="360"/>
<p/><img src="assets/CLWB-order2.png" width="360"/>
<p/><img src="assets/CLWB-order3.png" width="360"/>

> 我理解是 CLWB 是一种 store-like op，保证了数据一定刷到了内存。   
> store 和 clwb 是同一个地址，所以有序。   
> 那么真正写到内存的时机应该在 store 和 clwb 之间，另外要考虑到 clwb 可能发生 reorder。   

### 2.2 NVM-Only Architecture

<p/><img src="assets/Fig2.2.png" width="660"/>

- H-Store
  - logical logging
- MySQL5.5
  - double write

### 2.3 NVM+DRAM Architecture

<p/><img src="assets/Fig2.3.png" width="660"/>

- anti-cache
  - 将纵向的 dbpage fault sync fetch 改为水平化的 async fetch
- disk-oriented

### 2.4 Experimental Evaluation

#### 2.4.3 NVM-Only Architecture

<p/><img src="assets/Fig2.4.png" width="660"/>

- NVM latency 影响 logging
  - H-Store 受 latency 影响较小，因为 lightweight logical logging
- skew
  - 对于 H-Store，skew 越高，性能略好，原因是 DRAM miss + NVM access
  - 对于 mysql，skew 越高，性能越差，原因是 highweight concurrency control 导致的 lock contention

<p/><img src="assets/Fig2.5.png" width="660"/>

#### 2.4.4 NVM+DRAM Architecture

<p/><img src="assets/Fig2.6.png" width="660"/>

- skew
  - 高 skew
      - anti-caching 性能好（原因是 fetch 少？）
      - mysql 性能差，原因是 lock contention
- NVM latency 没有显著影响
- 影响性能的原因在于数据在 DRAM 和 NVM 中来回交换（fetch & evict）

#### 2.4.5 Recovery

<p/><img src="assets/Fig2.7.png" width="660"/>

- 需要新的 logging scheme


&nbsp;   
<a id="3"></a>
## 3 Storage Management


&nbsp;   
<a id="4"></a>
## 4 Logging and Recovery


&nbsp;   
<a id="5"></a>
## 5 Buffer Management


&nbsp;   
<a id="6"></a>
## 6 Indexing


&nbsp;   
<a id="7"></a>
## 7 Related Work


&nbsp;   
<a id="8"></a>
## 8 Future Work


&nbsp;   
<a id="9"></a>
## 9 Conclusion


&nbsp;   
<a id="10"></a>
## APPENDIX A: Non-Volatile Memory Emulation


&nbsp;   
<a id="11"></a>
## APPENDIX B: Benchmarks


&nbsp;   
<a id="12"></a>
## APPENDIX C: H-Store DBMS

