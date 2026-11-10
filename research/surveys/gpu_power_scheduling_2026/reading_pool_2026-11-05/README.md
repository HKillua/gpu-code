# 高水平论文广读池与阅读路线 v01

> 后续用户要求优先2025–2026，并下载全文。当前入口改为[近期论文与本地全文](近期论文与本地全文.md)：正式池已扩至42篇，已取得41篇全文。本文保留上一轮32篇广读和当时阅读顺序，不代表当前优先级。

> 2026-11-06增量：[已下载20篇近期论文正文广读与Mode C](../broad_read_2026-11-05/README.md)已完成；本页下面的初筛/正文计数是上一轮快照，不代表最新阅读状态。

**检索与核验日期：2026-11-05（Asia/Shanghai）。本轮纳入32篇正式发表论文，优先系统、网络、体系结构和性能分析会议/期刊。**

主题：供电约束下的GPU调度，以及支撑它的任务调度、计算通信与外部信息处理。收录范围覆盖2012–2026，主体为2018年后的工作；GreenHadoop仅作历史源头。正式发表是入口条件，方法质量与本项目适用性仍需正文证据。

## 1. 阅读状态与纳入边界

- 新增 **13篇正文定点分析包**：已读问题、关键方法、评估和边界，均为partial；不等于逐页精读、源码审计或独立复现。
- 另复用1篇Phoenix预印本正文/部分工件分析，正式Nature Energy版仅核对书目；尚未逐项审计正式版修订。
- **18篇完成摘要/书目初筛**：保留题名、发表记录、问题和后续要核验的前提，未写成正文已读完。
- 主池不以仅arXiv论文充数。已知PowerSlider仍作为此前矩阵来源保留，不列入本轮正式发表主池。CarbonFlex/PCAPS等版本关系待核验线索见能源组watchlist，不重复累计。
- 同一研究的期刊全文、会议展示摘要与预印本不重复计数。POMACS 2023全文、SIGMETRICS/Performance 2024展示分别记录。
- 本轮无GPU作业、功率控制、代码复现或新方法设计。

## 2. 第一批先读8篇

阅读顺序兼顾主题直接性、概念基础和你们两机六张3090的条件。它是学习顺序，不是引用量排名或最终选题优先级。

| 顺序 | 论文与发表 | 这一篇先解决什么困惑 | 本轮状态 |
|---|---|---|---|
| 1 | [POLCA](https://doi.org/10.1145/3620666.3651329) · ACM ASPLOS 2024, Volume 3 | 最贴近“总供电有上限、任务优先级不同”；先看图12与表6。 | 正文定点分析 |
| 2 | [Zeus](https://www.usenix.org/conference/nsdi23/presentation/you) · USENIX NSDI 2023 | 建立到同等训练质量的耗时/能耗口径；先看表1与图6。 | 正文定点分析 |
| 3 | [Tiresias](https://www.usenix.org/conference/nsdi19/presentation/gu) · NSDI 2019 | 理解排队、抢占和短作业等待；先看队列规则与图10–11。 | 正文定点分析 |
| 4 | [GREEN](https://www.usenix.org/conference/nsdi25/presentation/xu-kaiqiang) · NSDI 2025 | 把能源信号接到固定容量GPU集群；正文平台使用RTX3090。 | 正文定点分析 |
| 5 | [DynamoLLM](https://ieeexplore.ieee.org/document/10946802) · IEEE HPCA 2025 | 理解在线推理的能耗、首输出和连续输出间隔；对读图7–8。 | 正文定点分析 |
| 6 | [CASSINI](https://www.usenix.org/conference/nsdi24/presentation/rajasekaran) · NSDI 2024 | 观察计算之外的链路争用；先看图2、4、11。 | 正文定点分析 |
| 7 | [ElasticFlow](https://doi.org/10.1145/3575693.3575721) · ASPLOS 2023 | 理解截止期、分卡与重配；先看图5、6、12。 | 正文定点分析 |
| 8 | [Carbon shifting limits](https://2024.eurosys.org/accepted-papers.html) · EuroSys 2024 | 用约束与简单基线检查优化收益空间；先看§3/Table1和§5。 | 正文定点分析 |

希望先从产业故事进入，可在第1篇前读[Phoenix现场演示](https://www.nature.com/articles/s41560-025-01927-1)，重点理解任务允许退化范围。本文发表在能源期刊，只承担场景证据，后续主线仍聚焦计算机系统。

## 3. 完整32篇地图

下表中的“正文”指本轮重点章节阅读，“初筛”指摘要/书目阅读；“旧稿正文”明确保留版本边界。每篇的前提、六卡联系与分析包在四组中文卡片中。

### GPU 功率、训练能耗与推理能效

| ID | 论文（链接为正式记录或作者机构记录） | 发表 | 读它要回答的问题 | 状态 |
|---|---|---|---|---|
| LP01 | **[Zeus](https://www.usenix.org/conference/nsdi23/presentation/you)** — Zeus: Understanding and Optimizing GPU Energy Consumption of DNN Training | USENIX NSDI 2023 | 反复训练相似模型时，到同一个质量目标要花多久、耗多少电？ | 正文 |
| LP02 | **[POLCA](https://doi.org/10.1145/3620666.3651329)** — Characterizing Power Management Opportunities for LLMs in the Cloud | ACM ASPLOS 2024, Volume 3 | 同一列机柜的额定供电固定，若接入更多推理服务器，怎样在功率逼近上限时优先保护紧急任务？ | 正文 |
| LP03 | **[DynamoLLM](https://ieeexplore.ieee.org/document/10946802)** — DynamoLLM: Designing LLM Inference Clusters for Performance and Energy Efficiency | IEEE HPCA 2025 | 长短请求混合且流量有峰谷时，怎样在满足在线响应目标的同时降低推理能耗？ | 正文 |
| LP04 | **[Perseus](https://doi.org/10.1145/3694715.3695970)** — Reducing Energy Bloat in Large Model Training | ACM SOSP 2024 | 大模型训练中哪些耗电可以省下，同时维持训练进度？ | 初筛 |
| LP05 | **[TAPAS](https://doi.org/10.1145/3676641.3716025)** — TAPAS: Thermal- and Power-Aware Scheduling for LLM Inference in Cloud Platforms | ACM ASPLOS 2025, Volume 2 | 云中 GPU 放在哪里、请求发往哪台机器，会不会触发局部供电或散热限制？ | 初筛 |
| LP06 | **[Kareus](https://www.usenix.org/conference/osdi26/presentation/wu-ruofan)** — Kareus: Joint Reduction of Dynamic and Static Energy in Large Model Training | USENIX OSDI 2026 | 训练时，减少运算本身的耗电与缩短整个设备通电时长能否同时考虑？ | 初筛 |
| LP07 | **[ML.ENERGY Benchmark](https://proceedings.neurips.cc/paper_files/paper/2025/hash/9dc510e3d7b0b3b2a58ffed7a3ad6b0f-Abstract-Datasets_and_Benchmarks_Track.html)** — The ML.ENERGY Benchmark: Toward Automated Inference Energy Measurement and Optimization | NeurIPS 2025, Datasets and Benchmarks Track | 怎样在接近实际服务的条件下，测得可比较的生成式推理能耗？ | 初筛 |
| LP08 | **[PctoDL](https://doi.org/10.1145/3805802)** — PctoDL: Adaptive GPU Throughput Optimization for Deep Learning Inference with Power Constraints | ACM Transactions on Architecture and Code Optimization (TACO), 23(2) 2026 | 多个推理工作负载共享 GPU 的瓦数上限时，怎样获得更高的合计吞吐？ | 初筛 |

[本组中文阅读卡](power_energy_notes.md) · [结构化来源索引](power_energy.json)

### GPU 集群调度基础

| ID | 论文（链接为正式记录或作者机构记录） | 发表 | 读它要回答的问题 | 状态 |
|---|---|---|---|---|
| LS01 | **[Tiresias](https://www.usenix.org/conference/nsdi19/presentation/gu)** — Tiresias: A GPU Cluster Manager for Distributed Deep Learning | NSDI 2019 | 多卡训练要等所需 GPU 凑齐，提交时又常说不准运行多久。怎样减少作业从提交到完成的时间？ | 正文 |
| LS02 | **[Themis](https://www.usenix.org/conference/nsdi20/presentation/mahajan)** — Themis: Fair and Efficient GPU Cluster Scheduling | NSDI 2020 | 共享 GPU 的用户获得相同卡数，为什么完成时间仍可能相差很远？什么才是让用户愿意共享的公平定义？ | 正文 |
| LS03 | **[Pollux](https://www.usenix.org/conference/osdi21/presentation/qiao)** — Pollux: Co-adaptive Cluster Scheduling for Goodput-Optimized Deep Learning | OSDI 2021 | 为训练增加 GPU 或放大批量，得到的每秒样本更多。模型是否也更快达到同一质量目标？ | 正文 |
| LS04 | **[Gandiva](https://www.usenix.org/conference/osdi18/presentation/xiao)** — Gandiva: Introspective Cluster Scheduling for Deep Learning | OSDI 2018 | 用户同时尝试若干超参数时，能否快速看到每个训练的早期反馈，同时提高共享集群利用率？ | 初筛 |
| LS05 | **[Gavel](https://www.usenix.org/conference/osdi20/presentation/narayanan-deepak)** — Heterogeneity-Aware Cluster Scheduling Policies for Deep Learning Workloads | OSDI 2020 | 相同 GPU 时间在不同型号、不同训练作业上对应不同进度，已有集群策略怎样表达这种差别？ | 初筛 |
| LS06 | **[Sia](https://doi.org/10.1145/3600006.3613175)** — Sia: Heterogeneity-aware, goodput-optimized ML-cluster scheduling | SOSP 2023 | 当 GPU 型号和数量都可选，训练作业也会随资源调整，如何估算、搜索和分配？ | 初筛 |
| LS07 | **[PCS](https://www.usenix.org/conference/osdi24/presentation/bin-faisal)** — When will my ML Job finish? Toward providing Completion Time Estimates through Predictability-Centric Scheduling | OSDI 2024 | 调度器既想缩短作业时间，也需要对用户给出可信的预计完成时间。这两项目标如何取舍？ | 初筛 |
| LS08 | **[Alibaba ASI / SpotGPU](https://www.usenix.org/conference/osdi26/presentation/li-suyi)** — Heterogeneity at Hyperscale: Characterization and Scheduling of Large Production AI Clusters at Alibaba (Operational Systems) | OSDI 2026 | 超大生产集群有训练、推理与开发作业并发。GPU 紧缺时，为什么仍有空闲卡，怎样让可被抢占的工作使用它们？ | 初筛 |

[本组中文阅读卡](cluster_scheduling_notes.md) · [结构化来源索引](cluster_scheduling.json)

### 计算、通信与资源重配置

| ID | 论文（链接为正式记录或作者机构记录） | 发表 | 读它要回答的问题 | 状态 |
|---|---|---|---|---|
| LN01 | **[CASSINI](https://www.usenix.org/conference/nsdi24/presentation/rajasekaran)** — CASSINI: Network-Aware Job Scheduling in Machine Learning Clusters | NSDI 2024 | 多个训练作业共用网络链路时，何种放置会让它们的通信互相阻塞？ | 正文 |
| LN02 | **[ElasticFlow](https://doi.org/10.1145/3575693.3575721)** — ElasticFlow: An Elastic Serverless Training Platform for Distributed Deep Learning | ASPLOS 2023 | 训练作业给定截止时间时，如何判断还能接多少作业，并选择随时间变化的 GPU 数量？ | 正文 |
| LN03 | **[Sailor](https://doi.org/10.1145/3731569.3764839)** — Sailor: Automating Distributed Training over Dynamic, Heterogeneous, and Geo-distributed Clusters | SOSP 2025 | GPU 类型、链路与可用机器变化后，训练作业如何选出可放入显存、且吞吐/费用合适的并行方案？ | 正文 |
| LN04 | **[Optimus](https://doi.org/10.1145/3190508.3190517)** — Optimus: An Efficient Dynamic Resource Scheduler for Deep Learning Clusters | EuroSys 2018 | 训练作业进度与收益随资源配置变化，调度器能否在线估计并重新分配资源？ | 初筛 |
| LN05 | **[AntMan](https://www.usenix.org/conference/osdi20/presentation/xiao)** — AntMan: Dynamic Scaling on GPU Clusters for Deep Learning | OSDI 2020 | 单作业内显存和计算需求波动时，如何把空闲资源分给其他作业并处理干扰？ | 初筛 |
| LN06 | **[StellaTrain](https://doi.org/10.1145/3651890.3672228)** — Accelerating Model Training in Multi-cluster Environments with Consumer-grade GPUs | SIGCOMM 2024 | 消费级 GPU 跨集群训练遇到低带宽与不同计算速度时，如何缩短抵达目标精度的时间？ | 初筛 |
| LN07 | **[Crux](https://doi.org/10.1145/3651890.3672239)** — Crux: GPU-Efficient Communication Scheduling for Deep Learning Training | SIGCOMM 2024 | 多项训练任务同时争用链路时，优先传哪条流可以减少 GPU 等待？ | 初筛 |
| LN08 | **[Tessera](https://www.usenix.org/conference/osdi26/presentation/hu-weifang)** — Tessera: A Holistic Pipeline Parallelism Framework for Trillion-Parameter Heterogeneous MoE Training (Operational Systems) | OSDI 2026 | 不同层组合能掩盖的通信时间各异时，怎样划分流水线，避免串行估计看似平衡、实跑后却有等待？ | 初筛 |

[本组中文阅读卡](network_elasticity_notes.md) · [结构化来源索引](network_elasticity.json)

### 能源信号、灵活性与在线决策

| ID | 论文（链接为正式记录或作者机构记录） | 发表 | 读它要回答的问题 | 状态 |
|---|---|---|---|---|
| LC01 | **[Phoenix field demonstration](https://www.nature.com/articles/s41560-025-01927-1)** — AI data centres as grid-interactive assets | Nature Energy 2026 | 电网要求降载时，AI集群能否同时兑现功率响应和事先约定的业务表现？ | 旧稿正文；正式版书目 |
| LC02 | **[GREEN](https://www.usenix.org/conference/nsdi25/presentation/xu-kaiqiang)** — GREEN: Carbon-efficient Resource Scheduling for Machine Learning Clusters | NSDI 2025 | 固定容量下，如何让多个ML任务兼顾完成时间和碳排放？ | 正文 |
| LC03 | **[SPADE](https://www.usenix.org/conference/osdi26/presentation/lechowicz)** — SPADE: Signal-Aware DAG Scheduling and Dynamic Provisioning for Data Processing Clusters | OSDI 2026 | 外部信号改变资源供给时，如何避免延迟关键DAG任务阻塞下游？ | 正文 |
| LC04 | **[CarbonScaler](https://www.sigmetrics.org/opentoc/acm23_vol7_issue3_toc.html)** — CarbonScaler: Leveraging Cloud Workload Elasticity for Optimizing Carbon-Efficiency | POMACS 2023 | 除了延迟/暂停，动态增加或减少资源能否形成更好的碳—时间折中？ | 初筛 |
| LC05 | **[Carbon shifting limits](https://2024.eurosys.org/accepted-papers.html)** — On the Limitations of Carbon-Aware Temporal and Spatial Workload Shifting in the Cloud | EuroSys 2024 | 现实约束下，迁移/延迟工作负载究竟还有多少碳收益空间？ | 正文 |
| LC06 | **[Ecovisor](https://sigplan-www.sigplan.hosting.acm.org/OpenTOC/asplos23-2.html)** — Ecovisor: A Virtual Energy System for Carbon-Efficient Applications | ASPLOS 2023 | 能源系统如何给应用提供可编程的可见性和控制接口？ | 初筛 |
| LC07 | **[Online Pause and Resume](https://www.sigmetrics.org/opentoc/acm23_vol7_issue3_toc.html)** — The Online Pause and Resume Problem: Optimal Algorithms and An Application to Carbon-Aware Load Shifting | POMACS 2023 | 未来信号未知且切换有代价时，如何在截止期前完成工作？ | 正文 |
| LC08 | **[GreenHadoop](https://www.researchwithrutgers.org/en/publications/greenhadoop-leveraging-green-energy-in-data-processing-frameworks/)** — GreenHadoop: Leveraging Green Energy in Data-Processing Frameworks | EuroSys 2012 | 如何让有时限的后台计算匹配太阳能供给？ | 初筛 |

[本组中文阅读卡](energy_signals_notes.md) · [结构化来源索引](energy_signals.json)

## 4. 这轮广读得到的几条判断

以下是本项目的阅读归纳，不是新颖性结论。

1. **约束输入需要先区分。** POLCA的设施总功率固定；Zeus优化任务内部的时间与能量；GREEN处理历史碳信号与固定GPU容量；DynamoLLM的预测主要针对流量和请求长度。名称都带能源，并不代表解决同一个问题。
2. **调度优先级与业务紧急度不是同一量。** Tiresias按已获服务更新优先级；Themis讨论公平完成时间；紧急业务的SLO、截止期和外部功率约束还需单独声明。
3. **重配置成本已经是已有方法的一部分。** Pollux会惩罚频繁重配；GREEN计入checkpoint与重启；Sailor分解重配阶段；OPR显式研究切换成本。今后必须核对我们实际状态是否超出这些已有模型，不能只用“考虑恢复开销”声称创新。
4. **总量改善不自动保护个体。** SPADE原型中整体makespan接近默认时，p95 JCT仍有明显增长；DynamoLLM一个小时回放中中位和尾延迟变化方向不同。读实验要同时看整体、分位数和未完成任务。
5. **通信是与GPU计算共同变化的资源。** CASSINI处理通信碰撞，ElasticFlow与Sailor处理放置/重配。是否与供电约束耦合、耦合多强，仍需目标场景证据。
6. **六卡能帮助检验局部机制。** GREEN使用同代RTX3090很值得参考，但64卡网络、容量竞争与回放收益不能直接挪用。H100、A100和大规模MoE工作提供机制阅读价值，不据此要求我们原样复现。

上述归纳定位：[POLCA](../../../../research/papers/patel_2024_polca/analysis_v01.md)、[GREEN](../../../../research/papers/xu_2025_green/analysis_breadth_v01.md)、[Pollux](../../../../research/papers/qiao_2021_pollux/analysis_v01.md)、[Sailor](../../../../research/papers/strati_2025_sailor/analysis_v01.md)、[OPR](../../../../research/papers/lechowicz_2023_pause_resume/analysis_breadth_v01.md)、[SPADE](../../../../research/papers/lechowicz_2026_spade/analysis_breadth_v01.md)、[DynamoLLM](../../../../research/papers/stojkovic_2025_dynamollm/analysis_v01.md)。

## 5. 每篇讨论留下什么

第一遍只抓六件事：研究对象；真正的约束与优化指标；调度器能看到的信息；能执行的动作和成本；证据来自实机、轨迹回放还是仿真；哪条前提在两机六卡上尚未成立。随后再读方法和关键图，不先推新算法。

正文分析包允许进入后续Mode C复核；初筛卡先补对应正文与定位。下一轮按相近问题选3–8个来源分批比较，保留已知机制与未知项，再增量更新矩阵。暂未进入discover-innovation或跨领域方法适配。

推荐下一篇从 **POLCA** 开始，先按“问题—功率边界—高低优先级—动作—评估证据”讨论。然后用Zeus补清功率、能量与有效训练进度。

## 6. 文件、来源与局限

- [32篇机器可读汇总与包血缘](manifest_v01.json) · [BibTeX书目](references.bib)
- [功率与能耗阅读卡](power_energy_notes.md) · [集群调度阅读卡](cluster_scheduling_notes.md) · [通信与重配置阅读卡](network_elasticity_notes.md) · [能源信号阅读卡](energy_signals_notes.md)
- [检索与纳入说明](search_protocol.md) · [推进台账](../../../../topics/progress.md)

本轮是围绕问题链的定向广读，不是穷尽系统综述；没有建立新颖性、复现成功率或论文质量总排名。部分出版社正文需要权限，作者公开稿与正式版可能存在差异。日期、文献类型、读到哪里和缺项均以各来源卡为准。
