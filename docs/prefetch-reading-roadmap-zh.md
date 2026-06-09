# 数据预取论文阅读路线图

这个文件夹里现在有两类资料：

- 你原来的两篇博士论文和课程 PDF
- 我补下来的预取相关论文，按年份和会议命名

## 建议先读的顺序

如果老师主要让你学“数据预取”，我建议不要从博士论文整本开始啃，而是按下面这条线读。

### 0. 入门概念

先看：

- `13-prefetch.pdf`

目标：

- 搞懂 stride prefetcher、stream prefetcher、coverage、accuracy、timeliness、bandwidth overhead 这些基本词

不用深读，先建立词汇表就好。

### 1. 软件预取和间接访存

先读：

- `2017-CGO-Software-Prefetching-for-Indirect-Memory-Accesses.pdf`
- `2019-TOCS-Software-Prefetching-Microarchitectural-Perspective.pdf`
- `UCAM-CL-TR-923-prefetching.pdf` 的第 3 章

这条线回答：

**如果访问地址看起来不规则，编译器能不能提前算出未来地址，然后插软件预取？**

重点看：

- indirect memory access 是什么
- 为什么 `a[i] -> b[a[i]]` 这种模式适合预取
- 软件预取的 lookahead distance 怎么选
- 为什么软件预取有 instruction overhead
- 为什么复杂依赖链会让软件预取变差

### 2. 图计算和数据结构知识

再读：

- `2016-ICS-Graph-Prefetching-Using-Data-Structure-Knowledge.pdf`
- `UCAM-CL-TR-923-prefetching.pdf` 的第 4 章

这条线回答：

**硬件如果知道程序正在遍历 CSR 图，能不能比软件预取做得更好？**

重点看：

- CSR graph 的 vertex array / edge array / visited array
- BFS 的访存模式
- 为什么单纯 stride prefetcher 抓不住
- configurable graph prefetcher 怎么被程序配置
- 它如何观察 queue / vertex / edge / visited 之间的关系

### 3. 可编程硬件预取器

然后读：

- `2018-ASPLOS-Event-Triggered-Programmable-Prefetcher.pdf`
- `UCAM-CL-TR-923-prefetching.pdf` 的第 5、6 章

这条线回答：

**固定功能预取器覆盖不了所有模式，那能不能做一个小型可编程预取系统？**

重点看：

- event-triggered model
- PPU 是什么
- 为什么用很多小核，而不是一个大 helper core
- observation queue / prefetch request queue / address filter
- 编译器如何帮忙生成 event kernels

### 4. 软硬件协同、profile-guided 预取

接着读：

- `cornellgrad_prefetch-thesis.pdf` 的第 3 章
- `2022-EuroSys-APT-GET-Profile-Guided-Timely-Software-Prefetching.pdf`
- `2025-ISCA-Profile-Guided-Temporal-Prefetching-Prophet.pdf`

这条线回答：

**软件/编译器/profiling 能不能告诉硬件：哪些地方该预取，哪些地方别浪费带宽？**

重点看：

- 数据中心为什么带宽紧张
- aggressive prefetcher 为什么可能让性能退化
- hardware throttling 为什么太 reactive
- PC / call stack / profile 信息如何判断 prefetch usefulness
- profile-guided hints 是怎样影响硬件预取决策的

### 5. Runahead 和间接访存依赖链

最后读：

- `2021-ISCA-Vector-Runahead.pdf`
- `2023-MICRO-Decoupled-Vector-Runahead.pdf`
- `2024-MICRO-Scalar-Vector-Runahead.pdf`
- `2025-IEEE-Micro-Scalar-Vector-Runahead-Top-Picks.pdf`

这条线回答：

**如果问题不是“发一条预取”这么简单，而是一串依赖 load，能不能用 runahead 提前走这条链？**

重点看：

- indirect memory chain
- memory-level parallelism
- runahead 和 prefetch 的关系
- vector runahead 为什么适合间接访存
- decoupled vector runahead 为什么要把地址生成和主执行分开

## 每篇论文一句话定位

- `2016-ICS-Graph-Prefetching-Using-Data-Structure-Knowledge.pdf`  
  用图数据结构知识指导硬件预取，主要面向 CSR 图和 BFS。

- `2017-CGO-Software-Prefetching-for-Indirect-Memory-Accesses.pdf`  
  自动生成软件预取，专门处理间接数组访问。

- `2018-ASPLOS-Event-Triggered-Programmable-Prefetcher.pdf`  
  用事件触发的小型可编程单元做通用不规则预取。

- `2019-TOCS-Software-Prefetching-Microarchitectural-Perspective.pdf`  
  2017 CGO 软件预取工作的扩展版，从微体系结构角度分析为什么有效、什么时候失效。

- `2020-TACO-Informed-Prefetching-for-Indirect-Memory-Accesses-ATP.pdf`  
  让软件提供信息，帮助硬件做 indirect prefetch。

- `2021-ISCA-Vector-Runahead.pdf`  
  用 vector runahead 提前执行间接访存链，挖掘 memory-level parallelism。

- `2022-EuroSys-APT-GET-Profile-Guided-Timely-Software-Prefetching.pdf`  
  用 profiling 选择更及时的软件预取位置。

- `2023-MICRO-Decoupled-Vector-Runahead.pdf`  
  把 vector runahead 做成更解耦的机制，进一步提升间接访存性能。

- `2024-ISCA-Triangel-Temporal-Prefetcher.pdf`  
  关注 temporal prefetching，目标是高性能、准确、及时。

- `2024-MICRO-Scalar-Vector-Runahead.pdf`  
  把 vector runahead 思想用于 in-order core 上的间接访存链。

- `2025-IEEE-Micro-Scalar-Vector-Runahead-Top-Picks.pdf`  
  Scalar Vector Runahead 的短版/Top Picks 版，适合快速了解。

- `2025-ISCA-Profile-Guided-Temporal-Prefetching-Prophet.pdf`  
  用 profile-guided 方法做 temporal prefetching，和 Cornell 论文里的 ProP 思想很接近。

## 对比时抓住这几个维度

读预取论文时，不要只看 speedup。建议每篇都问这几个问题：

1. 它预取的是什么访问模式？
2. 它靠什么信息预测未来地址？
3. 信息来自硬件观察、编译器分析、程序员 hint，还是 profiling？
4. 它的准确率如何？
5. 它的 coverage 如何？
6. 它会不会增加太多内存带宽？
7. 它解决 timeliness 问题了吗？
8. 它需要改 ISA、改编译器、改硬件，还是只改软件？
9. 它适合单核、并行程序，还是数据中心多租户？
10. 它处理的是简单 stride、间接数组、图遍历、链表，还是 temporal pattern？

## 推荐第一轮阅读策略

第一轮不要逐字读。每篇只读：

1. Abstract
2. Introduction
3. Motivation example
4. Design overview
5. Evaluation summary
6. Conclusion

等你能用自己的话说出“这篇解决什么问题、和前一篇差在哪”之后，再回去读细节。

## 最小阅读路线

如果你现在只想快速建立方向感，先读这 5 篇：

1. `13-prefetch.pdf`
2. `2017-CGO-Software-Prefetching-for-Indirect-Memory-Accesses.pdf`
3. `2018-ASPLOS-Event-Triggered-Programmable-Prefetcher.pdf`
4. `2021-ISCA-Vector-Runahead.pdf`
5. `2025-ISCA-Profile-Guided-Temporal-Prefetching-Prophet.pdf`

这 5 篇连起来，基本覆盖了：

- 基础硬件预取
- 软件预取
- 可编程硬件预取
- runahead
- profile-guided temporal prefetching
