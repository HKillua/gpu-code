# Mode C 第四批｜资源释放、状态恢复与画像条件

MC-20261105-004-v01 · 2026-11-05阅读批次，2026-11-06形成 · **partial**。

本包消费7份ANALYSIS_PACKAGE及Sailor本轮正文增量，沿[项目deep-read Mode C原文](../../standards/sources/2026-11-05/skills/deep-read/SKILL.md)建立问题—现象—机制关系。这里只给后续阅读和问题核验入口，不设计方法，也不选baseline或题目。DE表示原材料可定位，并非本项目独立实测。

## 本批发现

第一，计划搜索、丢弃的计算量、资源退出和恢复有效服务是不同量。Sailor指定事件的NCCL重建比规划慢；SpotGPU分别记录checkpoint丢失工作和LP退出；EcoServe逻辑换控制关系也不等于释放物理GPU。跨到供电事件的价值仍待建立。

第二，Tessera给出重叠提速的内存代价，也记录画像与求解成本。VAHRM的功率模型和FlipFlop的内核模型都有特定条件。由此可以问画像何时过期，但没有证据证明改变电力预算必然使配置排名反转。

第三，SPADE和VAHRM提醒我们整体目标改善时部分任务仍可能受损。这些论文已经报告了代价，不能把“考虑公平/尾部”直接当新颖性。

## 来源与定位

| 来源 | 版本与上游 | 本批重点 |
|---|---|---|
| LN03 | [Sailor / AN-20261105-019-v01](../../papers/strati_2025_sailor/analysis_v01.md) | §5.5重配置；BR增量§4.4、§5设置、§5.2.4 |
| LN08 | [Tessera / AN-20261105-043-v01](../../papers/hu_2026_tessera/analysis_broad_v01.md) | §5成本偏差，§6.6内存，§6.7画像开销 |
| LS08 | [Heterogeneity at Hyperscale / AN-20261105-044-v01](../../papers/li_2026_spotgpu/analysis_broad_v01.md) | §4.2退出/丢弃工作/分配率；§3.1优先级 |
| RP03 | [VAHRM / AN-20261105-045-v01](../../papers/yoshida_2025_vahrm/analysis_broad_v01.md) | §III预算，§IV画像，§VI等待/首跑代价 |
| RNS06 | [Efficient LLM Serving on Commodity GPU Clusters with Data-Reduced Cross-Instance Orchestration / AN-20261105-046-v01](../../papers/du_2026_ecoserve/analysis_broad_v01.md) | §3.4逻辑分组与drain，§4.3/4.5 SLO与扩缩 |
| LC03 | [SPADE / AN-20261105-024-v01](../../papers/lechowicz_2026_spade/analysis_breadth_v01.md) | §2.1软信号，§5.3整体/尾部统计 |
| RP04 | [FlipFlop / AN-20261105-034-v01](../../papers/rajput_2026_flipflop/analysis_broad_v01.md) | §5校准，§9与结论的隔离内核边界 |

Sailor新增证据LN03-E04-BR/E05-BR/E06-BR保存在[本轮分组卡](../../surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/distributed_resources.md)与[JSON](../../surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/distributed_resources.json)。旧AN019及其E1/E2不改。真实评估也包括Grace-Hopper与Titan-RTX/RTX-2080/RTX-3090异构机器，并非仅A100/V100；这不构成两机六卡复现证据。

## P × F 概览

| | P01 释放与有效恢复 | P02 画像条件与配置排序 | P03 整体与个体代价 |
|---|---|---|---|
| F01 | supported；LN03-E3,LN03-E04-BR,LS08-E4,LS08-E5,RNS06-E2 | plausible；LN03-E3,RNS06-E5 | plausible；LS08-E4,LS08-E5,LC03-E3 |
| F02 | diagnostic-needed；LN08-E2,LN08-E5,RNS06-E2 | supported；LN08-E2,LN08-E5,LN08-E6,RP04-E04 | plausible；LN08-E5,RNS06-E1,RNS06-E4 |
| F03 | unsupported；LC03-E1,LC03-E3,RP03-E1,RP03-E6 | unsupported；LC03-E2,LC03-E3,RP03-E6 | supported；LC03-E3,RP03-E6 |

完整理由、机制条件、替代解释及原文位置在下方结构包。supported只限所注明的原条件，不代表动态供电场景已获验证。

## 两条连接点

- **C01**：可回收分配量、丢弃工作最少和尽快恢复有效服务，何时会对同一动作作不同评价？先补统一语义与已有覆盖，不把“考虑恢复”当创新。
- **C02**：离线画像的配置排序在哪类条件变化后不再可信，何时重画像无法摊销？尚无排序失效的共同实测，更未提出新控制方法。

二者均为diagnostic-needed。六维评分只是当前材料的粗筛；新颖性尚未建立。当前下游先补来源与近邻；正式诊断前另形成Idea和计划。跨领域方法尚未选定，迁移分支不适用。

## MODE_C_PACKAGE

```json
{
  "package_type": "MODE_C_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "MC-20261105-004-v01",
  "matrix_id": "MC-20261105-004-v01",
  "created_at": "2026-11-06",
  "batch_date": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "AN-20261105-019-v01",
    "AN-20261105-043-v01",
    "AN-20261105-044-v01",
    "AN-20261105-045-v01",
    "AN-20261105-046-v01",
    "AN-20261105-024-v01",
    "AN-20261105-034-v01"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "../../papers/strati_2025_sailor/analysis_v01.md",
    "../../papers/hu_2026_tessera/analysis_broad_v01.md",
    "../../papers/li_2026_spotgpu/analysis_broad_v01.md",
    "../../papers/yoshida_2025_vahrm/analysis_broad_v01.md",
    "../../papers/du_2026_ecoserve/analysis_broad_v01.md",
    "../../papers/lechowicz_2026_spade/analysis_breadth_v01.md",
    "../../papers/rajput_2026_flipflop/analysis_broad_v01.md",
    "../../surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/distributed_resources.json"
  ],
  "missing_evidence": [
    "没有统一动态供电事件或两机六卡的本机运行。",
    "没有审计源码、原始日志、完整统计区间与独立重复。",
    "七个来源的任务、硬件、时间尺度和目标不同，尚无跨设置复验。",
    "FlipFlop本地作者稿存在协议与计量待澄清处；不采用其最大收益。",
    "SPADE为CPU DAG软信号调度；VAHRM允许瞬时超预算；Sailor预算是货币，SpotGPU容量是配额，不能转写为硬功率履约。",
    "完整最近邻/新颖性及跨领域方法条件未建立。"
  ],
  "next_skill": "discover-innovation",
  "actual_next_stage": "先补来源、版本和最近邻证据并讨论C，不进入正式Idea或实验。",
  "scope_and_inclusion_rules": "2025–2026已下载来源中的7篇动态资源、训练时序、画像和信号调度研究。作为本轮20篇广读的第三批，阅读不受六卡规模限制。本包仅整合P/F/M/E与连接点，不设计方法、不选baseline/题目。",
  "source_map": [
    {
      "id": "LN03",
      "analysis_id": "AN-20261105-019-v01",
      "analysis_path": "../../papers/strati_2025_sailor/analysis_v01.md",
      "source": "Sailor: Automating Distributed Training over Dynamic, Heterogeneous, and Geo-distributed Clusters",
      "version": "正式会议论文；本包按会议版页/节/图记录",
      "type": "正式论文的本地会议/作者版本，详见原包",
      "key_evidence": [
        "LN03-E3",
        "LN03-E04-BR",
        "LN03-E05-BR",
        "LN03-E06-BR"
      ],
      "coverage": "本轮方法/关键评估/局限定点广读；未审源码或原始日志。新增BR证据在../../surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/distributed_resources.json；旧AN019不改。",
      "evidence_status": "partial"
    },
    {
      "id": "LN08",
      "analysis_id": "AN-20261105-043-v01",
      "analysis_path": "../../papers/hu_2026_tessera/analysis_broad_v01.md",
      "source": "Tessera: A Holistic Pipeline Parallelism Framework for Trillion-Parameter Heterogeneous MoE Training (Operational Systems)",
      "version": "USENIX OSDI 2026 正式会议 PDF，Operational Systems Paper；PDF 含封面，正文印刷页287–304。",
      "type": "正式论文的本地会议/作者版本，详见原包",
      "key_evidence": [
        "LN08-E1",
        "LN08-E2",
        "LN08-E5",
        "LN08-E6"
      ],
      "coverage": "本轮方法/关键评估/局限定点广读；未审源码或原始日志。",
      "evidence_status": "partial"
    },
    {
      "id": "LS08",
      "analysis_id": "AN-20261105-044-v01",
      "analysis_path": "../../papers/li_2026_spotgpu/analysis_broad_v01.md",
      "source": "Heterogeneity at Hyperscale: Characterization and Scheduling of Large Production AI Clusters at Alibaba (Operational Systems)",
      "version": "USENIX OSDI 2026 正式会议 PDF，Operational Systems Paper；PDF含封面，正文印刷页2187–2203。",
      "type": "正式论文的本地会议/作者版本，详见原包",
      "key_evidence": [
        "LS08-E1",
        "LS08-E3",
        "LS08-E4",
        "LS08-E5",
        "LS08-E6"
      ],
      "coverage": "本轮方法/关键评估/局限定点广读；未审源码或原始日志。",
      "evidence_status": "partial"
    },
    {
      "id": "RP03",
      "analysis_id": "AN-20261105-045-v01",
      "analysis_path": "../../papers/yoshida_2025_vahrm/analysis_broad_v01.md",
      "source": "VAHRM: Variation-Aware Resource Management in Heterogeneous Supercomputing Systems",
      "version": "作者大学网站保存的IEEE TPDS正式排版版；36(8)，1713–1727，2025年8月；DOI 10.1109/TPDS.2025.3577252。",
      "type": "正式论文的本地会议/作者版本，详见原包",
      "key_evidence": [
        "RP03-E1",
        "RP03-E2",
        "RP03-E3",
        "RP03-E6"
      ],
      "coverage": "本轮方法/关键评估/局限定点广读；未审源码或原始日志。",
      "evidence_status": "partial"
    },
    {
      "id": "RNS06",
      "analysis_id": "AN-20261105-046-v01",
      "analysis_path": "../../papers/du_2026_ecoserve/analysis_broad_v01.md",
      "source": "Efficient LLM Serving on Commodity GPU Clusters with Data-Reduced Cross-Instance Orchestration",
      "version": "USENIX OSDI 2026 正式会议 PDF；含封面，正文印刷页1787–1802，非2025年较早arXiv稿。",
      "type": "正式论文的本地会议/作者版本，详见原包",
      "key_evidence": [
        "RNS06-E1",
        "RNS06-E2",
        "RNS06-E4",
        "RNS06-E5",
        "RNS06-E6"
      ],
      "coverage": "本轮方法/关键评估/局限定点广读；未审源码或原始日志。",
      "evidence_status": "partial"
    },
    {
      "id": "LC03",
      "analysis_id": "AN-20261105-024-v01",
      "analysis_path": "../../papers/lechowicz_2026_spade/analysis_breadth_v01.md",
      "source": "SPADE: Signal-Aware DAG Scheduling and Dynamic Provisioning for Data Processing Clusters",
      "version": "版本与读页见原分析包",
      "type": "正式论文的本地会议/作者版本，详见原包",
      "key_evidence": [
        "LC03-E1",
        "LC03-E2",
        "LC03-E3"
      ],
      "coverage": "本轮方法/关键评估/局限定点广读；未审源码或原始日志。",
      "evidence_status": "partial"
    },
    {
      "id": "RP04",
      "analysis_id": "AN-20261105-034-v01",
      "analysis_path": "../../papers/rajput_2026_flipflop/analysis_broad_v01.md",
      "source": "FlipFlop: A Static Analysis-based Energy Optimization Framework for GPU Kernels",
      "version": "作者主页13页稿；正式会议身份已核，首页Alex Brandt对应会议作者Alexander Brandt；保留Conference’17占位页脚，不视为出版社排版终版。",
      "type": "正式论文的本地会议/作者版本，详见原包",
      "key_evidence": [
        "RP04-E02",
        "RP04-E04"
      ],
      "coverage": "本轮方法/关键评估/局限定点广读；未审源码或原始日志。",
      "evidence_status": "partial"
    }
  ],
  "P": [
    {
      "id": "P01",
      "text": "资源发生变化时，按丢弃GPU-time或可回收分配量选动作，能否同时反映物理资源释放与首个有效工作恢复？",
      "mark": "AI",
      "conditions": "SpotGPU退出、Sailor训练重配、EcoServe逻辑分组是不同对象；统一动态供电事件尚无证据。",
      "strength": "中：来源内可定位，跨设置未知",
      "evidence_ids": [
        "LN03-E3",
        "LN03-E04-BR",
        "LS08-E4",
        "LS08-E5",
        "RNS06-E2"
      ]
    },
    {
      "id": "P02",
      "text": "按既定硬件/并发条件形成的时间、功率或重叠画像，在哪些条件改变后仍能维持配置排序？",
      "mark": "AI",
      "conditions": "Tessera算子/通信重叠、VAHRM同型号器件偏差、FlipFlop隔离kernel校准；时变供电预算仅是待核验条件。",
      "strength": "中：来源内可定位，跨设置未知",
      "evidence_ids": [
        "LN08-E2",
        "LN08-E5",
        "RP03-E2",
        "RP03-E6",
        "RP04-E02",
        "RP04-E04"
      ]
    },
    {
      "id": "P03",
      "text": "优化整体碳/能量或资源使用时，哪些个体任务承担额外等待，能否仅用整体完成时间判断服务代价？",
      "mark": "AI",
      "conditions": "SPADE CPU DAG和VAHRM HPC任务；服务指标、对象和约束不同，不能合并收益。",
      "strength": "中：来源内可定位，跨设置未知",
      "evidence_ids": [
        "LC03-E3",
        "RP03-E6",
        "LS08-E1"
      ]
    }
  ],
  "F": [
    {
      "id": "F01",
      "text": "指定重配事件中Sailor的NCCL重建4.5秒超过规划0.1秒；SpotGPU另报告LP退出均值13秒、P95为48秒。",
      "mark": "DE",
      "conditions": "前者OPT-350M、16→20 V100单事件；后者生产LP清理阶段，非HP端到端恢复时间。",
      "strength": "中：来源内可定位，跨设置未知",
      "evidence_ids": [
        "LN03-E3",
        "LS08-E4"
      ]
    },
    {
      "id": "F02",
      "text": "Tessera的指定DBO消融将迭代6.98降至6.67秒，同时峰值内存占比67.4%增至70.0%；离线画像时间也不能由MILP求解时间代替。",
      "mark": "DE",
      "conditions": "256GPU、Qwen3-Next-M、4K序列；profile开销另为参考组上的测量。不是能量/供电收益。",
      "strength": "中：来源内可定位，跨设置未知",
      "evidence_ids": [
        "LN08-E2",
        "LN08-E5",
        "LN08-E6"
      ]
    },
    {
      "id": "F03",
      "text": "SPADE原型整体makespan为默认1.013倍时，p95 JCT仍增加45.4%；VAHRM另有部分大作业等待显著恶化。",
      "mark": "DE",
      "conditions": "前者CPU Spark/Kubernetes、历史信号与时间压缩；后者主要调度仿真，不能视为同一实验。",
      "strength": "中：来源内可定位，跨设置未知",
      "evidence_ids": [
        "LC03-E3",
        "RP03-E3",
        "RP03-E6"
      ]
    }
  ],
  "M": [
    {
      "id": "M01",
      "related": [
        "P01",
        "F01"
      ],
      "mark": "AI",
      "chain": "调度计划可生成或victim被选中 → 仍须退出清理、状态重建/排空等操作 → 资源释放与首个有效工作时点分离；是否在某个供电事件里主导代价仍未知。",
      "evidence_ids": [
        "LN03-E3",
        "LN03-E04-BR",
        "LS08-E3",
        "LS08-E4",
        "LS08-E5",
        "RNS06-E2",
        "RNS06-E5"
      ],
      "conditions": "实际动作确实涉及这些阶段；必须定义恢复进度基准和服务窗口。不同系统阶段时延不可相加。",
      "alternatives": "驻留暂停或逻辑分组可避免物理状态搬运；长窗口可摊销；checkpoint年龄对应的是丢弃工作而非全路径成本。",
      "status": "原设置部分支持；跨系统/动态供电联系待诊断"
    },
    {
      "id": "M02",
      "related": [
        "P02",
        "F02"
      ],
      "mark": "AI",
      "chain": "并发重叠改变SM/访存竞争且延长状态存活 → 原孤立/固定配置成本与可行内存改变 → 配置可行域或排序可能变化。",
      "evidence_ids": [
        "LN08-E1",
        "LN08-E2",
        "LN08-E5",
        "RP03-E2",
        "RP03-E6",
        "RP04-E02",
        "RP04-E04",
        "RNS06-E1",
        "RNS06-E4",
        "RNS06-E6"
      ],
      "conditions": "必须区分任务长度/模型、网络、设备、频率/功率、内存余量和计量窗口；画像与模型误差不能混为同一变化。",
      "alternatives": "Tessera已经使用重叠后profile；VAHRM已有单设备残差建模与首跑处理；FlipFlop范围本来就限定隔离kernel。条件改变也可能不改变排序。",
      "status": "来源内代价和适用条件有证据；外加功率变化导致排序反转未建立"
    },
    {
      "id": "M03",
      "related": [
        "P03",
        "F03"
      ],
      "mark": "AI",
      "chain": "总体目标允许重新分配服务/等待 → 一部分任务损失可能被整体完成时间或均值掩盖 → 去留判断需同时保留尾部、分组服务与总目标。",
      "evidence_ids": [
        "LC03-E2",
        "LC03-E3",
        "RP03-E1",
        "RP03-E6",
        "LS08-E1",
        "LS08-E6"
      ],
      "conditions": "比较对象拥有相同信息/动作；固定测量边界、服务承诺、作业分母；不能将作者报告的相关变化当作单组件因果。",
      "alternatives": "负载分布、动作空间、时间压缩和功率估计可影响统计；有些合同本来只约束整体窗口；原论文已报告相应代价。",
      "status": "整体/个体统计分离在来源内已支持；跨工作负载机制待核验"
    }
  ],
  "E": [
    {
      "id": "LN03-E3",
      "source_id": "LN03",
      "analysis_id": "AN-20261105-019-v01",
      "analysis_path": "../../papers/strati_2025_sailor/analysis_v01.md",
      "mark": "DE",
      "location": "§5.5 Reconfiguration overheads；§6 Discussion",
      "content": "16+4 V100 的重建过程和框架适用边界。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LN08-E1",
      "source_id": "LN08",
      "analysis_id": "AN-20261105-043-v01",
      "analysis_path": "../../papers/hu_2026_tessera/analysis_broad_v01.md",
      "mark": "SC",
      "location": "§2–3，PDF pp4–8（印刷pp289–293）",
      "content": "串行平衡不足；静态候选划分、重叠成本画像和MILP；DBO使用局部路由信号和受依赖/截止位置/内存约束的可移动任务池。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LN08-E2",
      "source_id": "LN08",
      "analysis_id": "AN-20261105-043-v01",
      "analysis_path": "../../papers/hu_2026_tessera/analysis_broad_v01.md",
      "mark": "DE",
      "location": "§5，PDF p10（印刷p295）",
      "content": "作者报告纯理论成本平均低估约5%，EP通信会占用SM并拖慢重叠计算；过度延后任务曾导致OOM。移动平均预测抖动属于未来系统验证。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LN08-E5",
      "source_id": "LN08",
      "analysis_id": "AN-20261105-043-v01",
      "analysis_path": "../../papers/hu_2026_tessera/analysis_broad_v01.md",
      "mark": "DE",
      "location": "§6.6，表3，PDF p14（印刷p299）",
      "content": "256GPU、Qwen3-Next-M、4K序列，overlap-aware静态分区上启用DBO：迭代6.98→6.67秒（作者报告4.4%下降），峰值内存67.4%→70.0%（+2.6个百分点）；always-keep为7.06秒。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LN08-E6",
      "source_id": "LN08",
      "analysis_id": "AN-20261105-043-v01",
      "analysis_path": "../../papers/hu_2026_tessera/analysis_broad_v01.md",
      "mark": "DE",
      "location": "§6.7，PDF pp14–15（印刷pp299–300）",
      "content": "8,192GPU目标配置的离线profile在64GPU参考组上执行；最长3,050秒，MILP耗时0.34–4.22秒，不能只以求解时间代表整个规划成本。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LS08-E1",
      "source_id": "LS08",
      "analysis_id": "AN-20261105-044-v01",
      "analysis_path": "../../papers/li_2026_spotgpu/analysis_broad_v01.md",
      "mark": "DE",
      "location": "§2、§3.1，表1，PDF pp4–5（印刷pp2189–2190）；§6，PDF pp13–14",
      "content": "六个月生产trace覆盖155,410GPU；训练94.1%为HP、离线推理98.6%为LP。高/低优先级由用户与服务保证定义，不能按“训练=可随意暂停”分类。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LS08-E3",
      "source_id": "LS08",
      "analysis_id": "AN-20261105-044-v01",
      "analysis_path": "../../papers/li_2026_spotgpu/analysis_broad_v01.md",
      "mark": "SC",
      "location": "§4.2，PDF pp10–11（印刷pp2195–2196）",
      "content": "Standby由用户通过API选择时间窗并获得费用激励；容器保温，主进程替换sleep且切断请求流量。恢复跳过重新调度/拉镜像，但仍须驱逐LP并恢复HP服务命令。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LS08-E4",
      "source_id": "LS08",
      "analysis_id": "AN-20261105-044-v01",
      "analysis_path": "../../papers/li_2026_spotgpu/analysis_broad_v01.md",
      "mark": "DE",
      "location": "§4.2 Standby HP tasks，PDF p11（印刷p2196）",
      "content": "LP优雅退出上限60秒；部署平均退出13秒，P95为48秒；超时SIGKILL少于5%。这是资源驱逐阶段的时延，不是HP恢复到首个有效请求的端到端时延。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LS08-E5",
      "source_id": "LS08",
      "analysis_id": "AN-20261105-044-v01",
      "analysis_path": "../../papers/li_2026_spotgpu/analysis_broad_v01.md",
      "mark": "SC",
      "location": "§4.2 Preemptive task scheduling，PDF p11（印刷p2196）",
      "content": "已有抢占机制定义Ct=Gt×(τnow−τck)，最小化距最近成功checkpoint后丢弃的GPU-time；优先不抢占，LP新任务只能不抢占放置。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LS08-E6",
      "source_id": "LS08",
      "analysis_id": "AN-20261105-044-v01",
      "analysis_path": "../../papers/li_2026_spotgpu/analysis_broad_v01.md",
      "mark": "DE",
      "location": "§4.2 Benefits of SpotGPU，PDF p12（印刷p2197）",
      "content": "GPU平均分配率68%→93%；关闭共置/驱逐历史并使用随机victim的对照下，LP完成时间减少24%，作者称HP性能未受影响。测量窗口和逐次统计在该段未展开。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "RP03-E1",
      "source_id": "RP03",
      "analysis_id": "AN-20261105-045-v01",
      "analysis_path": "../../papers/yoshida_2025_vahrm/analysis_broad_v01.md",
      "mark": "SC",
      "location": "§III-A–C，PDF pp3–4（印刷pp1715–1716）",
      "content": "节点独占、CPU/accelerator二类任务；加速器任务CPU按idle计；仅系统预算、无节点power cap，允许瞬时超过预算。预计执行时间事先可知，权重β需运行测试确定。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "RP03-E2",
      "source_id": "RP03",
      "analysis_id": "AN-20261105-045-v01",
      "analysis_path": "../../papers/yoshida_2025_vahrm/analysis_broad_v01.md",
      "mark": "SC",
      "location": "§IV，PDF pp5–6（印刷pp1717–1718）",
      "content": "TSMVA用同类基线模型加资源特定残差模型，六类PMC特征，RF+RF为主要实例；首跑缺画像按TDP保守入场。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "RP03-E3",
      "source_id": "RP03",
      "analysis_id": "AN-20261105-045-v01",
      "analysis_path": "../../papers/yoshida_2025_vahrm/analysis_broad_v01.md",
      "mark": "DE",
      "location": "§V-A–E，PDF pp6–8（印刷pp1718–1720）",
      "content": "主要评估为1秒步长调度模拟；九组合负载和10,000条日志；2,160GPU中256GPU实测，其余1,904GPU用正态随机数补齐，CPU分布从另一系统外推。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "RP03-E6",
      "source_id": "RP03",
      "analysis_id": "AN-20261105-045-v01",
      "analysis_path": "../../papers/yoshida_2025_vahrm/analysis_broad_v01.md",
      "mark": "DE",
      "location": "§III-C，图3，PDF p5；§VI-C2–6，PDF pp10–12（印刷pp1722–1724）",
      "content": "排序会产生个体等待代价；部分大作业等待可达backfill对照18倍。首跑作业收集PMC的附加执行时间在敏感性实验中假设为5%，首跑比例>40%时节能约1%；原日志不标注重跑，约9%首跑来自启发式同作业判定。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "RNS06-E1",
      "source_id": "RNS06",
      "analysis_id": "AN-20261105-046-v01",
      "analysis_path": "../../papers/du_2026_ecoserve/analysis_broad_v01.md",
      "mark": "SC",
      "location": "§3.1–3.3，图6、算法1，PDF pp5–8（印刷pp1790–1793）",
      "content": "PaDG把prefill与decode在实例内按时间分开并循环激活；算法检查预计prefill时间、当前decode请求的平均saved TPOT和显存；本文TTFT显式包含阶段切换等待。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "RNS06-E2",
      "source_id": "RNS06",
      "analysis_id": "AN-20261105-046-v01",
      "analysis_path": "../../papers/du_2026_ecoserve/analysis_broad_v01.md",
      "mark": "SC",
      "location": "§3.4，PDF pp8–9（印刷pp1793–1794）",
      "content": "扩缩以运行时平均TTFT/slack触发；缩容先drain；Flexible Instance Migration转移InstanceHandler/消息队列入口等元数据，改变macro scheduler的控制权，不物理迁移模型或KV。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "RNS06-E4",
      "source_id": "RNS06",
      "analysis_id": "AN-20261105-046-v01",
      "analysis_path": "../../papers/du_2026_ecoserve/analysis_broad_v01.md",
      "mark": "DE",
      "location": "§4.3，PDF p11（印刷p1796）",
      "content": "CodeLlama-34B/A800/ShareGPT案例中TTFT/TPOT从5秒/100毫秒收紧至1秒/50毫秒时，99%达标比例下吞吐42→18请求/秒（下降57.1%）。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "RNS06-E5",
      "source_id": "RNS06",
      "analysis_id": "AN-20261105-046-v01",
      "analysis_path": "../../papers/du_2026_ecoserve/analysis_broad_v01.md",
      "mark": "DE",
      "location": "§4.5，PDF p12（印刷p1797）",
      "content": "CodeLlama-34B、TP4、ShareGPT、L20动态负载从20增加至50RPS，32→64GPU并重新分macro groups；本地存储重新初始化一个实例约3分钟。逻辑迁移避免已有实例重新初始化。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "RNS06-E6",
      "source_id": "RNS06",
      "analysis_id": "AN-20261105-046-v01",
      "analysis_path": "../../papers/du_2026_ecoserve/analysis_broad_v01.md",
      "mark": "DE",
      "location": "§4.2、§5，PDF pp10、14（印刷pp1795、1799）",
      "content": "作者说明高带宽H100条件下收益缩小，特定CodeLlama案例Mooncake更好；小模型可适合不分离，极严格SLO/大模型可能需要完整分离及高速网络；部分基线达不到SLO的组合不展示。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LC03-E1",
      "source_id": "LC03",
      "analysis_id": "AN-20261105-024-v01",
      "analysis_path": "../../papers/lechowicz_2026_spade/analysis_breadth_v01.md",
      "mark": "SC/DE，见原包具体条目",
      "location": "§2.1，PDF p.4；软目标与硬执行层",
      "content": "具体方法、结果与边界见所链接原分析包。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LC03-E2",
      "source_id": "LC03",
      "analysis_id": "AN-20261105-024-v01",
      "analysis_path": "../../papers/lechowicz_2026_spade/analysis_breadth_v01.md",
      "mark": "SC/DE，见原包具体条目",
      "location": "§3/Alg.1、§4，PDF pp.6–8；相对重要性与SAP",
      "content": "具体方法、结果与边界见所链接原分析包。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LC03-E3",
      "source_id": "LC03",
      "analysis_id": "AN-20261105-024-v01",
      "analysis_path": "../../papers/lechowicz_2026_spade/analysis_breadth_v01.md",
      "mark": "SC/DE，见原包具体条目",
      "location": "§5.1、§5.3/Table2/Fig6，PDF pp.9–11；51VM/100executor、计量、重复、尾部",
      "content": "具体方法、结果与边界见所链接原分析包。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "RP04-E02",
      "source_id": "RP04",
      "analysis_id": "AN-20261105-034-v01",
      "analysis_path": "../../papers/rajput_2026_flipflop/analysis_broad_v01.md",
      "mark": "DE",
      "location": "PDF pp.6–9，§5，算法1–3、图4–5",
      "content": "硬件微基准校准、PTX特征、时间/功率建模与实测反馈；“静态分析”不等于无需任何硬件测量。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "RP04-E04",
      "source_id": "RP04",
      "analysis_id": "AN-20261105-034-v01",
      "analysis_path": "../../papers/rajput_2026_flipflop/analysis_broad_v01.md",
      "mark": "DE",
      "location": "PDF pp.11–12，§9及结论",
      "content": "作者讨论模型/硬件外部有效性，承认以隔离单内核为重点，将多流强争用和按阶段动态DVFS留作后续。",
      "boundary": "可定位的来源材料；未独立复现或审计原始日志。"
    },
    {
      "id": "LN03-E04-BR",
      "source_id": "LN03",
      "analysis_id": "AN-20261105-019-v01 + 本轮正文增量",
      "analysis_path": "../../papers/strati_2025_sailor/analysis_v01.md",
      "increment_path": "../../surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/distributed_resources.json",
      "mark": "DE（本地正文事实）；控制行为是SC，未审代码",
      "location": "§4.4–5.1，PDF p9（印刷p212）",
      "content": "明确kill-free重配置仍需重建通信与模型，training restarts from latest available checkpoint；不是任意中断无损。",
      "boundary": "2026-11-06正文增量复核；独立于原AN019的LN03-E1，不覆盖旧定位。"
    },
    {
      "id": "LN03-E05-BR",
      "source_id": "LN03",
      "analysis_id": "AN-20261105-019-v01 + 本轮正文增量",
      "analysis_path": "../../papers/strati_2025_sailor/analysis_v01.md",
      "increment_path": "../../surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/distributed_resources.json",
      "mark": "DE（本地正文事实）；控制行为是SC，未审代码",
      "location": "§5.2.4，PDF p13（印刷p216）；原AN019 LN03-E2另保留§5.2图10/12的实测/仿真定位",
      "content": "硬件测量与较大规模仿真分开；约束示例为0.2 iterations/s或1.2 USD/iteration，预算单位是货币而不是W。",
      "boundary": "2026-11-06正文增量复核；货币预算信息新增编号，不改变原LN03-E2范围。"
    },
    {
      "id": "LN03-E06-BR",
      "source_id": "LN03",
      "analysis_id": "AN-20261105-019-v01 + 本轮正文增量",
      "analysis_path": "../../papers/strati_2025_sailor/analysis_v01.md",
      "increment_path": "../../surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/distributed_resources.json",
      "mark": "DE（本地正文事实）；控制行为是SC，未审代码",
      "location": "§5 Evaluation—System configurations、§5.1，PDF p9（印刷p212）",
      "content": "真实评估包括GCP A100/V100、最多32台每台4张Grace-Hopper的同构集群，以及2×8 Titan-RTX、3×8 RTX-2080、2×8 RTX-3090的异构主机；Fig6为异构迭代时间预测。不能将真实设备范围概括为只有A100/V100，也不能据此认定两机六卡可复现。",
      "boundary": "2026-11-06补读本地会议PDF9；新增事实定位，旧AN019保持原样。"
    }
  ],
  "P_by_F_matrix": [
    {
      "F_id": "F01",
      "P_id": "P01",
      "status": "supported",
      "reason": "同类状态阶段和victim代价定义直接说明计划完成、退出完成与有效工作不同；仅支持分类和原条件，不支持统一供电响应保证。",
      "evidence_ids": [
        "LN03-E3",
        "LN03-E04-BR",
        "LS08-E4",
        "LS08-E5",
        "RNS06-E2"
      ],
      "mechanism_ids": [
        "M01"
      ]
    },
    {
      "F_id": "F01",
      "P_id": "P02",
      "status": "plausible",
      "reason": "重建成本可进入有限窗口配置价值，但这些事件没有对各配置排序或画像漂移做统一对照。",
      "evidence_ids": [
        "LN03-E3",
        "RNS06-E5"
      ],
      "mechanism_ids": [
        "M01",
        "M02"
      ]
    },
    {
      "F_id": "F01",
      "P_id": "P03",
      "status": "plausible",
      "reason": "退出与重建可能让少数任务承担更长等待，但当前没有退出阶段与个体JCT的联合分布。",
      "evidence_ids": [
        "LS08-E4",
        "LS08-E5",
        "LC03-E3"
      ],
      "mechanism_ids": [
        "M01",
        "M03"
      ]
    },
    {
      "F_id": "F02",
      "P_id": "P01",
      "status": "diagnostic-needed",
      "reason": "更长状态存活可约束资源回收，但Tessera的内存变化并未测物理释放时间或外部功率动作。",
      "evidence_ids": [
        "LN08-E2",
        "LN08-E5",
        "RNS06-E2"
      ],
      "mechanism_ids": [
        "M01",
        "M02"
      ]
    },
    {
      "F_id": "F02",
      "P_id": "P02",
      "status": "supported",
      "reason": "来源内重叠成本与内存代价可定位，支持画像依赖执行条件；预算改变是否使排序失效仍为AI，不能升级为已测。",
      "evidence_ids": [
        "LN08-E2",
        "LN08-E5",
        "LN08-E6",
        "RP04-E04"
      ],
      "mechanism_ids": [
        "M02"
      ]
    },
    {
      "F_id": "F02",
      "P_id": "P03",
      "status": "plausible",
      "reason": "内存和依赖约束说明局部提速有代价，但没有证明该代价转为多任务尾部延迟。",
      "evidence_ids": [
        "LN08-E5",
        "RNS06-E1",
        "RNS06-E4"
      ],
      "mechanism_ids": [
        "M02",
        "M03"
      ]
    },
    {
      "F_id": "F03",
      "P_id": "P01",
      "status": "unsupported",
      "reason": "全局/尾部JCT差异不是物理GPU释放、W响应或恢复阶段测量，不能由此反推资源可回收时点。",
      "evidence_ids": [
        "LC03-E1",
        "LC03-E3",
        "RP03-E1",
        "RP03-E6"
      ],
      "mechanism_ids": []
    },
    {
      "F_id": "F03",
      "P_id": "P02",
      "status": "unsupported",
      "reason": "个体等待恶化可以由目标排序解释，本身不证明性能/功率画像过期或配置排名变化。",
      "evidence_ids": [
        "LC03-E2",
        "LC03-E3",
        "RP03-E6"
      ],
      "mechanism_ids": []
    },
    {
      "F_id": "F03",
      "P_id": "P03",
      "status": "supported",
      "reason": "来源直接分别报告整体和个体等待/尾部，足以否定仅由整体近似不变推出所有服务近似不变。",
      "evidence_ids": [
        "LC03-E3",
        "RP03-E6"
      ],
      "mechanism_ids": [
        "M03"
      ]
    }
  ],
  "C": [
    {
      "id": "C01",
      "P_ids": [
        "P01",
        "P03"
      ],
      "F_ids": [
        "F01",
        "F03"
      ],
      "M_ids": [
        "M01",
        "M03"
      ],
      "focus": "资源可回收、工作丢弃最少、有效服务尽快恢复三种判断，何时对同一动作给出不同评价？",
      "status": "diagnostic-needed",
      "evidence_ids": [
        "LN03-E3",
        "LS08-E4",
        "LS08-E5",
        "RNS06-E2",
        "LC03-E3"
      ],
      "scores": {
        "mechanism": 1,
        "evidence": 2,
        "cross_setting": 1,
        "nontriviality": 1,
        "novelty": 0,
        "verifiability": 1
      },
      "score_reasons": {
        "mechanism": "阶段区分已有来源支持，但统一事件中的因果链缺失。",
        "evidence": "原正文阶段时延、代价公式和整体/尾部统计可定位。",
        "cross_setting": "训练、spot和推理具有不同状态，需建立共同动作语义。",
        "nontriviality": "有度量/动作评价冲突线索，尚不能判定构成新问题。",
        "novelty": "恢复、checkpoint损失、排空和状态处理均已有工作覆盖；本轮没有新颖性证明。",
        "verifiability": "先补文献和规格可低成本开展；真实诊断需独立Idea与硬件核验。"
      },
      "known_coverage": "Sailor已有重配置与checkpoint恢复，SpotGPU已有checkpoint-aware victim和退出窗口，EcoServe已有drain及逻辑重组；不能把“考虑恢复”当创新。",
      "unknowns": [
        "同一事件中三种代价是否真的改变动作选择",
        "服务合同与真实事件时长/频率",
        "动态功率近邻已覆盖到何处",
        "六卡下机制是否出现以及计量边界是否足够"
      ],
      "first_low_cost_check": "按现有正文列出每种动作的开始/结束条件、是否物理释放、丢弃进度与首次有效工作定义；不足项回到来源，不运行实验。"
    },
    {
      "id": "C02",
      "P_ids": [
        "P02"
      ],
      "F_ids": [
        "F02"
      ],
      "M_ids": [
        "M02"
      ],
      "focus": "从离线时间/能量画像得到的配置排名，在哪类运行条件变化下不再可信，何时重画像代价会超过可用收益？",
      "status": "diagnostic-needed",
      "evidence_ids": [
        "LN08-E2",
        "LN08-E5",
        "LN08-E6",
        "RP03-E2",
        "RP03-E6",
        "RP04-E02",
        "RP04-E04",
        "RNS06-E6"
      ],
      "scores": {
        "mechanism": 1,
        "evidence": 2,
        "cross_setting": 1,
        "nontriviality": 1,
        "novelty": 1,
        "verifiability": 1
      },
      "score_reasons": {
        "mechanism": "竞争/状态代价有中间证据，外加功率或拓扑改变后的排序失效仍未知。",
        "evidence": "原设定中内存、副作用和画像开销可定位；没有共同干预。",
        "cross_setting": "各方法画像对象不同，需要先区分可迁移的条件。",
        "nontriviality": "关注条件失配和画像摊销，而非把两个现成调度模块拼接。",
        "novelty": "相近的联合画像/在线反馈已存在，需补动态预算、在线性能模型和鲁棒控制近邻；仅记待比较。",
        "verifiability": "可先审正文/工件条件，再决定是否值得建立诊断。"
      },
      "known_coverage": "Tessera已做post-overlap画像、DBO与内存约束；VAHRM已有设备差异/首跑处理；FlipFlop已有硬件校准和反馈。不能把“加预测/加功率模型”当新意。",
      "unknowns": [
        "哪一项条件变化足以改变排名而非只改变绝对值",
        "计量误差、画像误差和真实状态漂移如何区分",
        "画像成本和适用时长的真实分布",
        "FlipFlop作者稿与终版/工件的协议差异"
      ],
      "first_low_cost_check": "分别核对画像输入、训练/验证条件、校准成本、缓存复用与反馈规则；先查最近邻是否已有同样失配及处理，不提出新模型。"
    }
  ],
  "recommended_C": {
    "connection_id": "C01",
    "purpose": "优先解释动作/状态/指标差异的阅读入口，非选题决定；C02并列保留。"
  },
  "first_low_cost_check": "沿C01补动作语义与计量表；沿C02核对画像条件及开销。任何真实诊断另经discover-innovation形成完整计划。",
  "evidence_audit": {
    "within_source": "以原包定位、表/图/协议和本轮Sailor PDF9/13增量为依据，未将摘要单独升级为DE。",
    "cross_setting": "不同系统仅提供互补证据，没有共同工作负载、设备或电力事件的复验。",
    "evidence_path": "F中的原设置观察可定位；M为显式AI；通向动态供电响应与配置排序的箭头仍待检验。",
    "independence": "七项不同研究提供状态、内存、画像和尾部统计的互补视角，未当作七个对同一假说的独立重复；同源方法与评估不重复计证据。",
    "boundary_checks": "任务/样本、秒级退出与迭代级调度、硬件与拓扑、模型误差、主存/显存、功率计量边界、仿真/实测、样本和统计功效均分开记录。"
  },
  "cross_domain_transfer": {
    "status": "不适用",
    "reason": "本轮只比较邻接问题与证据，没有选定目标A或源方法B；若后续迁移SPADE等方法须先走transfer-ml-methods。",
    "transfer_id": "none"
  },
  "handoff_ready": false,
  "six_gpu_boundary": "六卡仅限制后续实施与核心证据设计；本轮不据硬件规模排除阅读。尚未确认3090控制接口、网络、功率与整机计量，不能宣称这些系统已兼容或原规模收益可复现。"
}
```
