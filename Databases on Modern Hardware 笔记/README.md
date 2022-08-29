# Databases on Modern Hardware - How to Stop Underutilization and Love Multicores 读书笔记

- [1 Introduction](#1)
- [PART I Implicit/Vertical Scalability]
  - [2 Exploiting Resources of a Processor Core](#2)
  - [3 Minimizing Memory Stalls](#3)
- [PART II Explicit/Horizontal Scalability]
  - [4 Scaling-up OLTP](#4)
  - [5 Scaling-up OLAP Workloads](#5)
- [PART III Conclusions]
  - [6 Outlook](#6)
  - [7 Summary](#7)


&nbsp;   
<a id="1"></a>
## 1 Introduction

### 1.1 IMPLICIT/VERTICAL DIMENSION

<img src="assets/Fig1.1.png" width="540"/>

- **充分压榨单核性能**（同时受限于散热）
- aggressive microarchitectural features
- instruction/data level parallelism
- memory hierarchy (memory stall, data intensive workloads)
- instruction: compiler optimization, prefetching, computation spreading, txn batching
- data: cache-conscious, partitioning, thread scheduling

### 1.2 EXPLICIT/HORIZONTAL DIMENSION

<img src="assets/Fig1.2.png" width="540"/>

- **充分利用多核扩展性**（同时受限于核间通信）
- socket communication, shared LLC, NUMA, interconnect bandwidth
- TP: 挑战在于 communication，本文提出 unbounded/fixed/cooperative 尽可能降低 communication
- AP: latency, bandwidth, NU shared everything, topology-aware
  - across-core scheduling：减少 across-core access
  - data placement：减少 latency
  - 这两者互相影响

### 1.3 STRUCTURE OF THE BOOK

- 传统执行模型怎么充分利用硬件
- 最大化利用 data/instr locality
- 在 NU 场景下怎么 scale-up


&nbsp;   
<a id="2"></a>
## 2 Exploiting Resources of a Processor Core


<img src="assets/Fig2.1.png" width="540"/>
<img src="assets/Fig2.2.png" width="540"/>
<img src="assets/Fig2.3.png" width="540"/>
<img src="assets/Fig2.4.png" width="540"/>
<img src="assets/Fig2.5.png" width="540"/>
<img src="assets/Fig2.6.png" width="540"/>
<img src="assets/Fig2.7.png" width="540"/>
<img src="assets/Fig2.7.1.png" width="540"/>
<img src="assets/Fig2.8.png" width="540"/>
<img src="assets/Fig2.9.png" width="540"/>
<img src="assets/Fig2.10.png" width="540"/>
<img src="assets/Fig2.11.png" width="540"/>
<img src="assets/Fig2.12.png" width="540"/>
<img src="assets/Fig2.13.png" width="540"/>
<img src="assets/Fig2.14.png" width="540"/>
<img src="assets/Fig2.15.png" width="540"/>
<img src="assets/Fig2.16.png" width="540"/>
<img src="assets/Fig2.17.png" width="540"/>
<img src="assets/Fig2.18.png" width="540"/>
<img src="assets/Fig2.19.png" width="540"/>
<img src="assets/Fig2.20.png" width="540"/>


&nbsp;   
<a id="3"></a>
## 3 Minimizing Memory Stalls



<img src="assets/Fig3.1.png" width="540"/>
<img src="assets/Fig3.2.png" width="540"/>
<img src="assets/Fig3.3.png" width="540"/>
<img src="assets/Fig3.4.png" width="540"/>
<img src="assets/Fig3.5.png" width="540"/>
<img src="assets/Fig3.6.png" width="540"/>
<img src="assets/Fig3.7.png" width="540"/>
<img src="assets/Fig3.8.png" width="540"/>
<img src="assets/Fig3.9.png" width="540"/>


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


