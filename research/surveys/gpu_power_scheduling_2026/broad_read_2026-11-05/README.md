# 2025–2026论文第一轮广读：问题、方法、局限与idea线索

阅读批次始于2026-11-05，整理完成于**2026-11-06，Asia/Shanghai**。上游为已下载近期池及既有分析包；本轮按项目 `analyze-paper → deep-read Mode C` 推进，暂到问题连接层，不选择baseline、算法或最终题目。

**覆盖20项已正式发表的2025–2026研究，并单列1份待核验作者稿。** 每篇读到正文核心方法、关键评估与限制，属于定点广读；未逐页精读全部附录、未审计全部代码/原始数据、未独立复现。正式研究与本地PDF版本分别记录，不能称作20份出版社终版审计。已有21项近期正式研究中，PctoDL的PDF仍缺，故未纳入本轮正文结论。

本轮最有用的认识：文献中的“省电调度”至少包含**能量优化、碳信号调度、供电额度响应、GPU共享与资源回收**四类问题。它们共享一些动作，但不能互换指标。idea应来自某个机制在明确条件下的失效，而不是把这些关键词拼起来。

## 先读哪几篇

按下面顺序建立概念。前两篇贴近用户最初提到的“供电有限、任务紧急度不同”；后四篇逐渐进入训练调度、在线控制、GPU状态和计算/通信机制。**这是学习路线，不是baseline排名。**

| 顺序 | 本地论文 / 笔记 | 发表 | 阅读目的 | 重点位置与带着读的问题 |
|---|---|---|---|---|
| 1 | [TAPAS](../../../papers/stojkovic_2025_tapas/paper.pdf) · [笔记](../../../papers/stojkovic_2025_tapas/analysis_broad_v01.md) | ASPLOS 2025 | 设施电力限制怎样变成计算机系统问题 | §3.3图13–15、§4、§5.4表2；放置、路由、重配置各能做什么？应急Perf到底测了什么？ |
| 2 | [Alibaba ASI / SpotGPU](../../../papers/li_2026_spotgpu/paper.pdf) · [笔记](../../../papers/li_2026_spotgpu/analysis_broad_v01.md) | OSDI 2026 | 先理解紧急与不紧急任务的真实业务语义 | §3.1表1、§4.2；谁允许让出GPU，允许多久？退出和恢复是否同一件事？ |
| 3 | [GREEN](../../../papers/xu_2025_green/paper.pdf) · [笔记](../../../papers/xu_2025_green/analysis_breadth_v01.md) | NSDI 2025 | 训练调度如何利用时间弹性 | §§4–7、§8.1–8.2；碳强度、用电量、完成时间各怎样计账？ |
| 4 | [DynamoLLM](../../../papers/stojkovic_2025_dynamollm/paper.pdf) · [笔记](../../../papers/stojkovic_2025_dynamollm/analysis_v01.md) | HPCA 2025 | 从实例数、并行度、频率理解分层控制 | §III-D、§IV、§V-C；为什么有的动作慢、有的快？预测错后靠什么救急？ |
| 5 | [Nixie](../../../papers/xu_2026_nixie/paper.pdf) · [笔记](../../../papers/xu_2026_nixie/analysis_broad_v01.md) | OSDI 2026 | 进入GPU端：调度离不开显存状态 | §§5–6、§7.2图13；交互响应改善时，后台进度付出什么？切换等待在哪？ |
| 6 | [Kareus](../../../papers/wu_2026_kareus/paper.pdf) · [笔记](../../../papers/wu_2026_kareus/analysis_broad_v01.md) | OSDI 2026 | 进入计算/通信端：频率与执行重叠相互影响 | §2.3–3.2图3、§4、§6.4–6.7；降频后原本最优的通信/计算安排为何可能不再最优？ |

如果先只读三篇，读 **TAPAS → Alibaba ASI/SpotGPU → GREEN**：分别理解物理额度、业务优先级、时间弹性。它们不回答完全相同的问题，正适合建立领域地图。

另两篇可按兴趣穿插：[LithOS](../../../papers/coppock_2025_lithos/paper.pdf) · [笔记](../../../papers/coppock_2025_lithos/analysis_broad_v01.md)（SOSP 2025，§§5.4–5.6、§8.3–8.4）偏GPU执行控制；[ML.ENERGY Benchmark](../../../papers/chung_2025_ml_energy_benchmark/paper.pdf) · [笔记](../../../papers/chung_2025_ml_energy_benchmark/analysis_broad_v01.md)（NeurIPS 2025 D&B，§3.2、Appendix B）偏“节能结果究竟怎样测”。DynamoLLM之后再对读throttLL’eM的§IV-F和§V-E，重点看它承认的短时违约与TTFT边界。

## 用一条链理解这个领域

`外部约束/目标 → 业务允许的退化 → 调度动作 → GPU/通信/状态变化 → 可观测代价`

- 外部输入：功率额度是W，能量是J/Wh；碳强度是单位电量排放，电价是货币/电量；空闲卡数是资源容量。波动的碳强度或卡数不是波动的发电出力。
- 业务合同：在线推理有首token时间TTFT、后续token间隔TBT/TPOT和达标比例；训练/批作业有有效进度、完成时间、期限或允许平均降速。训练不天然低优先级，推理也不天然高优先级。
- 可用动作：延后、排队、调频/限功率、改变卡数或并行度、共享、抢占、搬移/保留状态、路由。每个动作都受权限、显存、链路及服务合同限制。
- 系统后果：批量、队列、计算/通信重叠、状态驻留、初始化和恢复会随动作变化。把瓦数降下来只是其中一步。
- 评价：在相同信息与动作权限下，同时看真实功率响应、在线服务、后台有效进度、完整窗口能量与代价。没有整机计量时，结论明确停留在GPU边界。

这条链的后四环主要属于计算机系统、GPU、通信与信息处理；当前无须先做发电设备或电气控制设计。

## 逐篇问题—方法—局限

以下“局限”混合了作者明确边界与本轮分析推断；细分标记、具体页码和待核验项见每篇笔记及四组正文卡。DE仅表示来源材料可定位，不是本项目实测。

### A. 供电、能量与服务控制

| 论文 | 解决的问题 | 核心方法 | 读完必须保留的边界 |
|---|---|---|---|
| [TAPAS · ASPLOS25](../../../papers/stojkovic_2025_tapas/paper.pdf) · [笔记](../../../papers/stojkovic_2025_tapas/analysis_broad_v01.md) | 设施电力/冷却受限且租户可见性不同 | 历史画像、VM放置、约束过滤路由、SaaS配置调整 | 应急表2是5分钟峰值窗的限频性能代理与估计质量；不能称完整事件的真实请求SLO保证。 |
| [Phoenix · Nature Energy26](../../../papers/colangelo_2025_grid_interactive/paper_preprint.pdf) · [笔记](../../../papers/colangelo_2025_grid_interactive/analysis_v01.md) | 真实峰值事件中按约定降载并保持业务可接受 | 事前Flex档位、功率—吞吐画像、按事件窗口编排限功率/暂停等动作 | 本地为2025预印本；合同允许小时级平均吞吐退化，GPU计量非机房进线计量；不能泛化为任意服务无损。 |
| [DynamoLLM · HPCA25](../../../papers/stojkovic_2025_dynamollm/paper.pdf) · [笔记](../../../papers/stojkovic_2025_dynamollm/analysis_v01.md) | 流量/请求长度变化下怎样降低推理能量 | 分池并分层调整实例数、TP和频率；错峰重配、误预测救急 | 主要优化需求起伏下的能量/SLO，没有外部时变总W额度；救急可用容量不能自动沿用到供电收紧。 |
| [throttLL’eM · HPCA25](../../../papers/kakolyris_2025_throttllem/paper_hpca2025.pdf) · [笔记](../../../papers/kakolyris_2025_throttllem/analysis_broad_v01.md) | KV和batch随生成变化，怎样预测性调频 | scoreboard投影、性能模型、服务检查、调频与shadow实例缩放 | 不优化TTFT；突发可短时违约而全轨迹p99仍达标；已有排空、预测误差和批量反馈处理。 |
| [Kareus · OSDI26](../../../papers/wu_2026_kareus/paper.pdf) · [笔记](../../../papers/wu_2026_kareus/analysis_broad_v01.md) | 降频与计算/通信重叠相互影响 | 联合搜索通信SM份额、启动时刻和频率，构建迭代时间—能量前沿 | 已做联合优化；真实16卡与千卡模拟分开。画像成本、频率切换和测量窗口有约束，不是动态外部供电控制。 |
| [VAHRM · TPDS25](../../../papers/yoshida_2025_vahrm/paper_tpds2025.pdf) · [笔记](../../../papers/yoshida_2025_vahrm/analysis_broad_v01.md) | 同型号设备功耗也不同，预算下怎样分配 | 两阶段功率模型、能量/等待加权排序、节点选择与入场检查 | 允许瞬时超预算、节点独占/已知运行时等假设；主要调度收益为仿真，部分功耗分布合成扩展。 |
| [FlipFlop · ICSE26](../../../papers/rajput_2026_flipflop/paper_author_icse2026.pdf) · [笔记](../../../papers/rajput_2026_flipflop/analysis_broad_v01.md) | 如何少测配置就找到节能GPU内核参数 | 硬件校准、PTX静态特征、时间/功率模型，筛选block形状与功率上限 | 隔离内核为主；作者稿若干参数/计量协议需澄清，不将最大收益作为可靠跨论文排名。 |

### B. GPU内部共享与请求状态

| 论文 | 解决的问题 | 核心方法 | 读完必须保留的边界 |
|---|---|---|---|
| [Nixie · OSDI26](../../../papers/xu_2026_nixie/paper.pdf) · [笔记](../../../papers/xu_2026_nixie/analysis_broad_v01.md) | 模型接近占满显存时，交互应用如何时间复用 | 拦截提交、等待kernel完成、分层内存迁移、交互优先与状态预取 | 主要单RTX5090/单用户；频繁交互案例后台吞吐下降，响应改善并非免费；未验证硬功率响应。 |
| [LithOS · SOSP25](../../../papers/coppock_2025_lithos/paper.pdf) · [笔记](../../../papers/coppock_2025_lithos/analysis_broad_v01.md) | GPU内的资源份额、长kernel阻塞与能量浪费 | TPC调度、kernel atomization、在线right-sizing与设备DVFS | 已联合共享和DVFS；主要单A100、模型共驻留，低层接口/特殊kernel限制重要；GPU能量非整机能量。 |
| [BLESS · EuroSys25](../../../papers/zhang_2025_bless/paper.pdf) · [笔记](../../../papers/zhang_2025_bless/analysis_broad_v01.md) | 固定空间份额/粗时间片留下空隙 | 离线画像、kernel squad、相对进度反馈和空隙回收 | 无bubble时收益收缩；须等待squad执行边界；空间配额不保证所有时刻零延迟代价。 |
| [FastServe · NSDI26](../../../papers/wu_2026_fastserve/paper.pdf) · [笔记](../../../papers/wu_2026_fastserve/analysis_broad_v01.md) | 未知输出长度造成请求队头阻塞与KV压力 | iteration级MLFQ、晋升、防饥饿、主动KV换出与预取 | 同系预印本2023已公开；KV优化在高显存压力下更有效。隐藏关键路径时间不等于没有PCIe/能量代价。 |

### C. 容量承诺、分布式训练与信号调度

| 论文 | 解决的问题 | 核心方法 | 读完必须保留的边界 |
|---|---|---|---|
| [Alibaba ASI / SpotGPU · OSDI26](../../../papers/li_2026_spotgpu/paper.pdf) · [笔记](../../../papers/li_2026_spotgpu/analysis_broad_v01.md) | 空闲GPU因碎片、预留和网络位置而不可用 | 资源整理、用户Standby窗口、LP抢占和checkpoint损失感知 | 分配率不是有效利用率；退出时延不是HP首请求恢复。优先级来自服务定义，未处理外部W额度。 |
| [GFS · ASPLOS26](../../../papers/duan_2026_gfs/paper.pdf) · [笔记](../../../papers/duan_2026_gfs/analysis_broad_v01.md) | 按组织需求售卖可承诺一段时间的spot容量 | 需求分布预测、保障窗口配额、驱逐历史与checkpoint损失调度 | 本地arXiv v1式9/11待正式版/实现澄清；保障时长增加伴随排队代价，生产前后比较有混杂。 |
| [Sailor · SOSP25](../../../papers/strati_2025_sailor/paper.pdf) · [笔记](../../../papers/strati_2025_sailor/analysis_v01.md) | 资源/设备/跨区链路变化后怎样重选并行方案 | 计算/通信/显存画像、计划搜索、运行期重配置 | kill-free仍从最近checkpoint恢复；真实测试含数据中心与消费卡，不等于两机六卡兼容。预算示例是货币，重建比求计划更耗时。 |
| [Tessera · OSDI26](../../../papers/hu_2026_tessera/paper.pdf) · [笔记](../../../papers/hu_2026_tessera/analysis_broad_v01.md) | 异构MoE流水线受重叠成本和动态bubble限制 | 重叠后画像、静态分区、动态填bubble、有界可移动任务池 | 移动工作延长状态存活并增内存；MFU增益不是节能。profile成本不能用MILP求解时间代替。 |
| [EcoServe · OSDI26](../../../papers/du_2026_ecoserve/paper.pdf) · [笔记](../../../papers/du_2026_ecoserve/analysis_broad_v01.md) | 弱互联下PD干扰与KV传输两难 | 实例内时间分离PD、保留KV、利用decode余量、逻辑重分组与drain | 逻辑迁移不物理搬模型/KV；严格SLO、高带宽或小模型改变适用性，扩实例未证明符合供电上限。 |
| [GREEN · NSDI25](../../../papers/xu_2025_green/paper.pdf) · [笔记](../../../papers/xu_2025_green/analysis_breadth_v01.md) | 固定容量里利用训练时间弹性减少碳排放 | 进度/能量弹性与碳感知双层队列，checkpoint/重启计入JCT | 碳信号并非供电额度；历史轨迹及不同基线作业子集须分开，已有恢复开销。 |
| [SPADE · OSDI26](../../../papers/lechowicz_2026_spade/paper.pdf) · [笔记](../../../papers/lechowicz_2026_spade/analysis_breadth_v01.md) | 信号变化时如何不让DAG关键依赖停住 | 任务相对重要性、信号阈值和动态资源供给 | CPU DAG邻接工作；软信号与硬执行分开。利用率模型和压缩时间不能当GPU硬功率实验；整体与尾部代价可相反。 |

### D. 测量与比较本身

| 论文 | 解决的问题 | 核心方法 | 读完必须保留的边界 |
|---|---|---|---|
| [ML.ENERGY · NeurIPS25 D&B](../../../papers/chung_2025_ml_energy_benchmark/paper.pdf) · [笔记](../../../papers/chung_2025_ml_energy_benchmark/analysis_broad_v01.md) | 生成服务能耗怎样测、怎样按延迟选配置 | 稳定饱和期GPU能量、配置扫描与Pareto前沿；附录研究抢占/TP/PD | 默认不覆盖完整启动/排空/恢复；已经测过抢占和通信能量，不能把二者当空白。 |
| [MLPerf Power · HPCA25](../../../papers/tschand_2025_mlperf_power/paper_arxiv_v2_hpca2025.pdf) · [笔记](../../../papers/tschand_2025_mlperf_power/analysis_broad_v01.md) | 跨平台如何统一性能/质量/计量边界 | 执行期对齐、不同规模仪器/遥测、系统能效提交协议 | 不完全包含设施冷却/存储；平均窗口不能证明瞬时cap；行业提交不是单项优化的随机因果试验。 |

观察项：[GreenLLM作者稿](../../../papers/liu_2026_greenllm/paper_author_dac2026.pdf) / [分析](../../../papers/liu_2026_greenllm/analysis_broad_v01.md)。机构库标DAC2026，但正式出版及版本待补；分阶段双环DVFS/滞回已经提出，高负载SLO达标率下降。只用于提醒最近邻覆盖，不计正式20篇，也不作为主矩阵的确定出版证据。

## 这一轮形成的五个idea线索

这里的“idea线索”是**有文献依据、可被推翻的问题连接**，不是已确认创新或完整IDEA_PACKAGE。各来源的局限不自动构成本项目值得做的题目。

| 线索 | 为什么值得继续追问 | 已有工作已做到哪里 | 需要找到什么证据；何时放下 |
|---|---|---|---|
| **L1：供电收紧时，原本用于救急的资源也消失了怎么办？** | DynamoLLM/throttLL’eM靠升频、新旧实例并存、重路由应对误差；这些动作可能恰在预算下降时不可行 | 预测、shadow、排空、错峰均已有；TAPAS、PowerSlider/POLCA等已研究cap/应急 | 先核算法可行性与动作权限，在相同外部约束下有无真实失败。已有机制若可直接满足，则不能声称新问题。对应MC002:C01。 |
| **L2：稳态最省的配置，完整降载—恢复一轮也最省吗？** | 稳定区间未必包含状态写出、排空、重建和追赶积压；短窗中这些成本可能改变排序 | GREEN、Sailor、OPR等已有状态/转换代价；ML.ENERGY附录已有抢占能量 | 需要同等有效工作/质量、完整窗口中的策略相关差异。若开销可忽略或不改变判断，停止将其当核心机制。对应MC002:C03、MC004:C01。 |
| **L3：功率/频率变了，原画像中的“最优并行/重叠方式”还可靠吗？** | Kareus显示频率与计算/通信安排相互影响；Tessera显示理论成本与实际重叠有偏差，且有内存代价 | Kareus已做联合优化，LithOS已联合资源份额/DVFS，Tessera已有重叠画像 | 要找原方法输入/缓存未覆盖的明确变化及配置排序改变；泛泛的“联合优化”没有新意。对应MC002:C02、MC004:C02。 |
| **L4：账面让出资源，多久才真的让紧急任务获得有效服务？** | 停提交、等kernel、迁移状态、释放卡、首个有效结果和稳定服务是不同时间点 | Nixie/FastServe预取、BLESS/LithOS执行粒度、SpotGPU退出、Sailor重建均有相关机制 | 需要同一合同下的时间线与有效进度证据；不能只测调度器做决定的耗时。若既有机制充分涵盖且无新边界，保留为复现知识。对应MC003:C01、MC004:C01。 |
| **L5：供给或容量的“未来信息”，究竟哪部分值得预测？** | 保障时间更长可能变成更久排队；已知结束时刻、幅度与置信度的价值可能不同 | GFS已有需求分布/保障窗口，GREEN有历史碳信号，SPADE使用lookahead边界；不能说它们不预测 | 先核信号实际可得时刻、预测目标和信息权限。无真实预测版本/发布时间时，不把历史全知回放当在线预测收益。对应MC003:C02；功率联系尚待建立。 |

这五条覆盖调度可行性、评价窗口、计算/通信、状态管理和预测信息价值。**目前没有哪条获得“已有方法失效且我们能解决”的完整证据**；优先做阅读与最近邻核验，不因连接点听起来新就先搭系统。

## 已排除的几种过早判断

- “考虑恢复成本”本身是创新：不成立，GREEN、Sailor、GFS等已有处理，计的成本也各不相同。
- “共享加DVFS”没人做：不成立，LithOS/Kareus已有不同粒度的联合处理；PctoDL正文仍缺，不能略过这个最近邻。
- “预测得更准就一定调度更好”：现有材料不足。预测目标、窗口承诺、动作延迟和误差代价都要核对。
- “单卡/小模型跑通就能代表机房收益”：不成立。两机六卡约束最终实现与核心证据；当前广读允许学习大规模和其他设备工作。硬件控制接口、网络与计量能力仍未知。
- “一篇论文整体指标好，就没有代价”：不成立。throttLL’eM的短时违约、SPADE的尾JCT、Nixie的后台进度、Tessera的内存，都提示要同时看受损对象。

## 证据与工作流交接

四组详细阅读卡及结构记录：

- [功率与服务](power_serving.md) / [JSON](power_serving.json)：5篇，新增4包、复用DynamoLLM。
- [共享与抢占](sharing_preemption.md) / [JSON](sharing_preemption.json)：5篇，新增5包。
- [分布式资源](distributed_resources.md) / [JSON](distributed_resources.json)：5篇，新增4包、复用Sailor；本轮增量不覆盖旧ID含义。
- [计量与信号](measurement_signals.md) / [JSON](measurement_signals.json)：5篇正式+1篇观察，新增2+1包，复用GREEN/SPADE/Phoenix。

合计20项主池：**15份新增partial来源分析包、5份复核复用**；观察稿另新增1份partial包。复用ID为AN002/007/019/023/024；新主池ID为AN031–039、041–046，观察为AN047。页码为本地PDF查看器序号，具体范围见包。

本轮分批建立3个正式Mode C包，覆盖7+6+7个不同来源，保留旧MC001：

| 包 | 来源/目标 | 当前状态 |
|---|---|---|
| [MC-20261105-002-v01](../../../matrices/power_constrained_gpu_scheduling/mode_c_002_power_v01.md) | 服务救急、配置画像与测量窗口；7来源 | partial，3P/3F/3M、9格、3C |
| [MC-20261105-003-v01](../../../matrices/power_constrained_gpu_scheduling/mode_c_003_sharing_v01.md) | 共享执行边界、状态与保障窗口；6来源 | partial，3P/3F/3M、9格、2C |
| [MC-20261105-004-v01](../../../matrices/power_constrained_gpu_scheduling/mode_c_004_resources_v01.md) | 资源退出/恢复、重叠成本与副作用；7来源 | partial，3P/3F/3M、9格、2C |

跨领域迁移本轮**不适用**：SPADE等提供邻接知识，但尚未选源方法B、未做A′适配或归因验证，不能把阅读算成迁移完成。正式Idea、服务器核验与实验均未开展。

下一轮可以从优先清单逐篇讨论“现象是什么—作者如何解释—证据支持到哪里—还有什么竞争解释”，再补对应最近邻；选定单个连接点并具备诊断依据后，才进入discover-innovation，涉及迁移则先走TRANSFER_PACKAGE。不会在这一轮提前定baseline或选题。
