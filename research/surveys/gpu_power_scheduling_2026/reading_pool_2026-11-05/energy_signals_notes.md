# 能源信号、灵活性与在线决策｜广读卡片

日期：2026-11-05。新读四篇正文重点章节，复用一个预印本正文包，三篇仅做摘要初筛；不混称八篇精读。正式发表年份与会议展示年份分别记录。

## LC01｜Phoenix field demonstration

**AI data centres as grid-interactive assets** — Nature Energy 2026。[来源](https://www.nature.com/articles/s41560-025-01927-1)

- 阅读范围：既有预印本正文/部分工件已分析；本轮核对正式期刊书目，未逐项对读期刊修订
- 问题：电网要求降载时，AI集群能否同时兑现功率响应和事先约定的业务表现？
- 方法：先声明业务Flex档位，结合任务功率—吞吐曲线编排限功率、暂停等动作。
- 本轮读到：现场试验提供需求响应与业务让步共存的实例。正式版2025-12-05在线、2026卷期；与旧预印本为同一研究，不重复计数。
- 前提/限制：256张A100、选定业务组合与小时级事件；GPU计量不能替代设施电表。
- 主题关系：直接：真实AI集群的功率灵活性。
- 六卡联系：借鉴事件与业务合同的双重验收；六卡只复核局部机制。
- [分析包](../../../../research/papers/colangelo_2025_grid_interactive/analysis_v01.md)

## LC02｜GREEN

**GREEN: Carbon-efficient Resource Scheduling for Machine Learning Clusters** — NSDI 2025。[来源](https://www.usenix.org/conference/nsdi25/presentation/xu-kaiqiang)

- 阅读范围：正文重点阅读：方法、实现、评估及边界；未复现
- 问题：固定容量下，如何让多个ML任务兼顾完成时间和碳排放？
- 方法：能量进度驱动弹性分卡，按碳信号和功率动态调整队列优先级。
- 本轮读到：正文有RTX3090实机，checkpoint与重启计入JCT；同时要分清791全量作业与150个适配基线作业。
- 前提/限制：32节点、每节点2张3090；历史碳信号、无硬截止期任务。
- 主题关系：直接：GPU训练集群能源调度；没有提供时变硬cap保证。
- 六卡联系：设备代际贴近；先读状态开销和固定容量评价，不能沿用64卡收益。
- [分析包](../../../../research/papers/xu_2025_green/analysis_breadth_v01.md)

## LC03｜SPADE

**SPADE: Signal-Aware DAG Scheduling and Dynamic Provisioning for Data Processing Clusters** — OSDI 2026。[来源](https://www.usenix.org/conference/osdi26/presentation/lechowicz)

- 阅读范围：正文重点阅读：方法、实现、评估及边界；未复现
- 问题：外部信号改变资源供给时，如何避免延迟关键DAG任务阻塞下游？
- 方法：联合DAG相对重要性与信号阈值，协调任务选择和资源供给，用SAP作拆分消融。
- 本轮读到：正文将信号软目标与硬执行层分开；总体makespan接近时，个体尾延迟仍可明显变差。
- 前提/限制：Spark CPU DAG；正文原型是51 VM/100 executor，碳由利用率模型计量，信号与任务时间缩放。
- 主题关系：邻接：外部能源信号与调度耦合；不属于GPU硬cap实证。
- 六卡联系：学习依赖、消融和指标边界；暂不做方法迁移。
- [分析包](../../../../research/papers/lechowicz_2026_spade/analysis_breadth_v01.md)

## LC04｜CarbonScaler

**CarbonScaler: Leveraging Cloud Workload Elasticity for Optimizing Carbon-Efficiency** — POMACS 2023。[来源](https://www.sigmetrics.org/opentoc/acm23_vol7_issue3_toc.html)

- 阅读范围：官方摘要初筛；未完成正文分析包
- 问题：除了延迟/暂停，动态增加或减少资源能否形成更好的碳—时间折中？
- 方法：按碳强度变化做弹性扩缩，使用边际资源分配思路与Kubernetes原型。
- 本轮读到：建立资源数量也可随信号变化的入口；2023期刊全文、2024 SIGMETRICS展示，不能算两篇。
- 前提/限制：需要应用支持弹性、资源可获得及扩缩成本；具体最优性条件待读正文。
- 主题关系：邻接：能源信号下的弹性批任务。
- 六卡联系：借鉴资源弹性建模；固定六卡与按需云容量不同。

## LC05｜Carbon shifting limits

**On the Limitations of Carbon-Aware Temporal and Spatial Workload Shifting in the Cloud** — EuroSys 2024。[来源](https://2024.eurosys.org/accepted-papers.html)

- 阅读范围：正文重点阅读：方法、结果及边界；未复现
- 问题：现实约束下，迁移/延迟工作负载究竟还有多少碳收益空间？
- 方法：123地区轨迹，在全知、零迁移成本等上界条件下逐项加入业务与容量限制。
- 本轮读到：强调先核算收益上界与强简单基线；人工预测噪声不等于真实预测误差。
- 前提/限制：2020–22平均碳强度、小时尺度、全知与零切换开销；不是GPU部署。
- 主题关系：邻接：信息价值与优化收益边界。
- 六卡联系：先判断复杂调度是否有改进余量；跨地域部分无法在两机上直接复现。
- [分析包](../../../../research/papers/sukprasert_2024_carbon_limits/analysis_breadth_v01.md)

## LC06｜Ecovisor

**Ecovisor: A Virtual Energy System for Carbon-Efficient Applications** — ASPLOS 2023。[来源](https://sigplan-www.sigplan.hosting.acm.org/OpenTOC/asplos23-2.html)

- 阅读范围：官方摘要初筛；未完成正文分析包
- 问题：能源系统如何给应用提供可编程的可见性和控制接口？
- 方法：虚拟能源系统向应用暴露碳强度、可再生出力和功率/电池控制。
- 本轮读到：从系统接口理解外部能源条件如何进入软件。
- 前提/限制：小规模物理能源原型含电池接口；具体控制保障及工作负载待正文核验。
- 主题关系：邻接：能源与计算之间的接口抽象。
- 六卡联系：可先理解接口边界；本项目没有确认具备电池或设施控制权。

## LC07｜Online Pause and Resume

**The Online Pause and Resume Problem: Optimal Algorithms and An Application to Carbon-Aware Load Shifting** — POMACS 2023。[来源](https://www.sigmetrics.org/opentoc/acm23_vol7_issue3_toc.html)

- 阅读范围：正文重点阅读：模型、方法、数值案例与展望；未独立审证/复现
- 问题：未来信号未知且切换有代价时，如何在截止期前完成工作？
- 方法：运行与暂停状态使用不同阈值，目标显式包含固定切换成本，给出模型内竞争分析。
- 本轮读到：暂停/恢复成本已有正式理论近邻；2023全文与2024会议摘要同一研究。
- 前提/限制：单任务、已知工作量/截止期/信号边界、固定β、每槽单位工作、假想机房。
- 主题关系：邻接且重要：状态切换与未知未来的理论基线。
- 六卡联系：理解什么条件下固定切换成本足够、何时需要真实GPU状态；尚未选择迁移方案。
- [分析包](../../../../research/papers/lechowicz_2023_pause_resume/analysis_breadth_v01.md)

## LC08｜GreenHadoop

**GreenHadoop: Leveraging Green Energy in Data-Processing Frameworks** — EuroSys 2012。[来源](https://www.researchwithrutgers.org/en/publications/greenhadoop-leveraging-green-energy-in-data-processing-frameworks/)

- 阅读范围：作者所属大学的正式书目及摘要初筛；未完成正文分析包
- 问题：如何让有时限的后台计算匹配太阳能供给？
- 方法：预测太阳能，按任务时限安排MapReduce，电网作为备用，并考虑购电价格与峰值。
- 本轮读到：是发电出力进入计算调度的经典源头；不是近期GPU论文。
- 前提/限制：CPU/MapReduce、太阳能与电网备用；不能理解成供电一低任务必须停。
- 主题关系：邻接：发电—计算调度的历史基础。
- 六卡联系：学习供电场景的建模；框架与年代不同，安排在扩展阅读。

## 版本与边界

SPADE 使用 Spark CPU DAG；外部信号感知不等于逐卡硬功率上限。CarbonScaler/OPR 的 POMACS 全文与次年 SIGMETRICS 摘要不重复计数。Phoenix 的正式期刊元数据与预印本正文分析分开记录。PCAPS 短文和 CarbonFlex 预印本暂作版本核查线索。
