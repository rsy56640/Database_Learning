## Transaction Processing on Modern Hardware 读书笔记

- [1 Introduction](#1)
- [2 Transaction Concepts](#2)
- [3 Multi-Version Concurrency Revisited](#3)
- [4 Coordination-Avoidance Concurrency](#4)
- [5 Novel Transactional System Architectures](#5)
- [6 Hardware-Assisted Transactional Utilities](#6)
- [7 Transactions on Heterogeneous Hardware](#7)
- [8 Outlook: The Era of Hardware Specialization and Beyond](#8)


&nbsp;   
<a id="1"></a>
## 1 Introduction

<p/><img src="assets/Fig1.1.png" width="600"/>

<p/><img src="assets/Fig1.2.png" width="600"/>

- hardware trend
  - memory
  - multi-core
- TP 可以完全放进 memory
- 小数据库可以常驻 cache
  - cache-resident
  - branch misprediction
  - vectorization
- instruction 有效利用率，去除冗余
  - memory-resident 可以去除 buffer pool
- 利用 multi-core
  - 针对不同场景如 NUMA 进行设计


&nbsp;   
<a id="2"></a>
## 2 Transaction Concepts


&nbsp;   
<a id="3"></a>
## 3 Multi-Version Concurrency Revisited


&nbsp;   
<a id="4"></a>
## 4 Coordination-Avoidance Concurrency


&nbsp;   
<a id="5"></a>
## 5 Novel Transactional System Architectures


&nbsp;   
<a id="6"></a>
## 6 Hardware-Assisted Transactional Utilities


&nbsp;   
<a id="7"></a>
## 7 Transactions on Heterogeneous Hardware


&nbsp;   
<a id="8"></a>
## 8 Outlook: The Era of Hardware Specialization and Beyond

