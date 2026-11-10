# Mode C 002｜服务救急、画像与评价窗口

MC-20261105-002-v01 · 2026-11-06（阅读批次2026-11-05）· partial。

本批7个来源，保留旧MC001；不将来源内事实写成本机验证，不将连接点写成新算法。六卡只约束后续证据设计，当前阅读不限制原规模。

| 现象 × 问题 | P01 救急余量 | P02 画像排序 | P03 事件评价 |
|---|---|---|---|
| F01 | plausible；LP03-E2,RP01-E04 | diagnostic-needed；RP01-E04 | supported；RP01-E04 |
| F02 | diagnostic-needed；LP06-E01,RP01-E01 | supported；LP06-E01,RP01-E04 | plausible；LP06-E04,RP01-E04 |
| F03 | unsupported；LP05-E04,S02-E1 | diagnostic-needed；LP07-E1,RP02-E1 | supported；LP07-E1,RP02-E1,LP05-E04,S02-E1 |

候选连接点：C01 外部额度改变救急动作可行性；C02 运行条件变化下画像的有效范围；C03 完整事件窗口中的配置排序。C01仅建议继续补证据，不是已选问题。

评分顺序：机制、证据、跨设置、非平凡性、新颖性、可核验性，各0–2。分数用于解释证据缺口，不是发表潜力。

```json
{
  "package_type": "MODE_C_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "MC-20261105-002-v01",
  "matrix_id": "MC-20261105-002-v01",
  "created_at": "2026-11-06",
  "round_started_on": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "AN-20261105-007-v01",
    "AN-20261105-031-v01",
    "AN-20261105-032-v01",
    "AN-20261105-033-v01",
    "AN-20261105-041-v01",
    "AN-20261105-042-v01",
    "AN-20261105-002-v01"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "../../papers/stojkovic_2025_dynamollm/analysis_v01.md",
    "../../papers/stojkovic_2025_tapas/analysis_broad_v01.md",
    "../../papers/wu_2026_kareus/analysis_broad_v01.md",
    "../../papers/kakolyris_2025_throttllem/analysis_broad_v01.md",
    "../../papers/chung_2025_ml_energy_benchmark/analysis_broad_v01.md",
    "../../papers/tschand_2025_mlperf_power/analysis_broad_v01.md",
    "../../papers/colangelo_2025_grid_interactive/analysis_v01.md"
  ],
  "missing_evidence": [
    "没有共同输入/事件/计量下的跨系统复验",
    "源码、原始轨迹与版本差异未全面核验",
    "目标机器能力、服务合同、事件时间尺度未知",
    "最近邻检索并非穷尽，不具备新颖性结论"
  ],
  "next_skill": "discover-innovation",
  "scope_and_inclusion_rules": "本轮20篇近期正式研究中的7篇：正文方法/关键评估/局限定点阅读；Phoenix本地为旧预印本，正式身份不重复计数。不含GreenLLM待核验稿。",
  "source_map": [
    {
      "id": "LP03",
      "analysis_id": "AN-20261105-007-v01",
      "source": "DynamoLLM: Designing LLM Inference Clusters for Performance and Energy Efficiency",
      "version": "本地作者公开HPCA2025会议稿；复用AN007，不覆盖旧包。",
      "analysis_path": "../../papers/stojkovic_2025_dynamollm/analysis_v01.md",
      "coverage": "复用AN007；本轮重新打开PDF pp.6–11，核验§IV方法/应急/多SLO及§V短时、单日和仿真范围。未重复生成包，未新增代码审计。",
      "type": "面向在线 LLM 服务的请求预测和分层控制。用硬件集群短时及单日回放，再用一周轨迹做较大规模仿真。"
    },
    {
      "id": "LP05",
      "analysis_id": "AN-20261105-031-v01",
      "source": "TAPAS: Thermal- and Power-Aware Scheduling for LLM Inference in Cloud Platforms",
      "version": "本地为作者公开的ASPLOS 2025会议排版稿，首页含DOI与正式页码；正式身份复用此前官方核验。",
      "analysis_path": "../../papers/stojkovic_2025_tapas/analysis_broad_v01.md",
      "coverage": "本轮读摘要及PDF pp.5–13：背景机制、§3画像、§4方法、§5关键评估与适用限制；参考文献、全部附录和代码未逐项审计。",
      "type": {
        "design": "生产数据分析、真实服务器回放、回归预测与离散时间仿真相结合。",
        "power_signal_and_objective": "直接考虑行级供电、冷却和服务器温度限制及故障后的额度下降；历史预测对象主要是负载/功率/温度。本文所读评估不能解释为任意时变外部供电序列上的严格功率保证。",
        "service_or_quality_contract": "常态 goodput 是满足 TTFT 与 TBT 的 token/s，§3.3 采用相对空载执行时间的 SLO。应急评估另用频率限幅的性能代理及转向较小模型的估计质量损失；两类服务口径不能互换。"
      }
    },
    {
      "id": "LP06",
      "analysis_id": "AN-20261105-032-v01",
      "source": "Kareus: Joint Reduction of Dynamic and Static Energy in Large Model Training",
      "version": "本地为USENIX官方OSDI 2026 PDF，含封面；正式身份复用官方presentation页核验。",
      "analysis_path": "../../papers/wu_2026_kareus/analysis_broad_v01.md",
      "coverage": "本轮读PDF pp.4–15：§2.3–8中方法、实现、主要真实/模拟评估、消融与计量限制；代码、附录工件说明和原始统计未审。PDF包含封面，页码采用查看器页序。",
      "type": {
        "design": "GPU内核级测量、模型驱动多目标搜索、真实分布式训练迭代实验与大规模仿真。",
        "power_signal_and_objective": "优化训练作业的时间–总能量前沿；dynamic/static指能量组成，不是供电上限时变。搜索中的不确定性用于性能/能量代理模型，并非预测电网功率额度。",
        "service_or_quality_contract": "关注迭代时间、动态/静态/总能量与给定时间或能量预算内的前沿选择；本轮所读结果不是训练达到同等验证质量的全过程比较。"
      }
    },
    {
      "id": "RP01",
      "analysis_id": "AN-20261105-033-v01",
      "source": "throttLL’eM: Predictive GPU Throttling for Energy Efficient LLM Inference Serving",
      "version": "本地为项目站公开的IEEE HPCA 2025排版PDF，首页有完整DOI；正式身份复用IEEE条目核验。",
      "analysis_path": "../../papers/kakolyris_2025_throttllem/analysis_broad_v01.md",
      "coverage": "本轮读PDF pp.6–13：§IV完整方法/局限与§V主要实验、敏感性、失效时间线和补充设备；摘要与结论已核对。代码、全部附录/引用与原始日志未审。",
      "type": {
        "design": "在线推理控制器、状态投影和性能模型，硬件轨迹回放及长度预测误差敏感性实验。",
        "power_signal_and_objective": "需求侧状态随请求改变，功率由频率与实例规模调节；所读方法没有把外部时变总功率额度作为约束输入。不能将预测未来KV/批量称作预测供电。",
        "service_or_quality_contract": "§V-A：平均TBT阈值与按模型高负载画像设定的p99端到端目标；§IV-F明确不优化TTFT。准入检查可将已经无法满足自身期限但不影响他人的请求标lost，继续best-effort处理。"
      }
    },
    {
      "id": "LP07",
      "analysis_id": "AN-20261105-041-v01",
      "source": "ML.ENERGY Benchmark",
      "version": "NeurIPS2025 D&B正式公开PDF",
      "analysis_path": "../../papers/chung_2025_ml_energy_benchmark/analysis_broad_v01.md",
      "coverage": "正文§§2–6，PDF pp2–10；Appendix B/C/E重点pp23–28；未审完整checklist、源码或日志。",
      "type": "生成式AI能耗基准、配置扫描与结果分析；不是时变供电控制器。"
    },
    {
      "id": "RP02",
      "analysis_id": "AN-20261105-042-v01",
      "source": "MLPerf Power",
      "version": "论文已发表于 HPCA 2025；本地为作者公开 arXiv:2410.12032v2（2025-02-06），首页标 HPCA 2025。未逐字比对出版社终版。",
      "analysis_path": "../../papers/tschand_2025_mlperf_power/analysis_broad_v01.md",
      "coverage": "正文§§II–VII，PDF pp3–13：边界、仪器、提交比较、局限；本地arXiv v2带HPCA标识，未逐字比终版。",
      "type": "能效基准方法与行业提交数据的观测分析；本地是arXiv v2带HPCA2025标识，不是独立新调度算法。"
    },
    {
      "id": "S02",
      "analysis_id": "AN-20261105-002-v01",
      "source": "Phoenix现场演示",
      "version": "Philip Colangelo 等，Turning AI Data Centers into Grid-Interactive Assets: Results from a Field Demonstration in Phoenix, Arizona，arXiv:2507.00909v1，2025-07-01。",
      "analysis_path": "../../papers/colangelo_2025_grid_interactive/analysis_v01.md",
      "coverage": "复用AN002；本轮复核预印本§§2–5与服务档位、事件/计量、checkpoint低频假设；沿用既有部分工件证据。",
      "type": "DE｜单个商业云机房的前瞻性工程现场试验。2025-05-01、05-03 分别对齐 APS 与 SRP 峰值，另在实机重演 2020 年 CAISO 应急场景。电网系统总负荷与受控 GPU 集群功率是两套量。E2。"
    }
  ],
  "P": [
    {
      "id": "P01",
      "mark": "AI",
      "text": "外部功率额度下降使最高频率或shadow实例不可用时，既有服务救急动作的可行性怎样改变？",
      "conditions": "把需求侧节能控制用于有外部供电额度的场景；当前是条件变化问题，尚无目标失败实测",
      "strength": "低：动作前提可定位，外部额度下失效未实测",
      "evidence": [
        "LP03-E2",
        "RP01-E01",
        "LP05-E02"
      ]
    },
    {
      "id": "P02",
      "mark": "AI",
      "text": "频率、并发或通信状态改变后，原画像的配置优劣排序何时需要重估？",
      "conditions": "原模型/硬件/输入可比；只讨论画像有效范围，不声称首次联合优化",
      "strength": "中：来源内有交互证据，跨场景未知",
      "evidence": [
        "LP06-E01",
        "RP01-E04"
      ]
    },
    {
      "id": "P03",
      "mark": "AI",
      "text": "稳态或全轨迹结果能否解释有限降载事件及恢复期的真实服务与能量代价？",
      "conditions": "共同任务/质量、计量边界、事件前后窗口与服务定义先对齐",
      "strength": "中：窗口差异可定位，排名变化未知",
      "evidence": [
        "LP07-E1",
        "RP02-E1",
        "LP05-E04",
        "S02-E1"
      ]
    }
  ],
  "F": [
    {
      "id": "F01",
      "mark": "DE",
      "text": "throttLL’eM的突发实验出现短时违约，即使全轨迹p99仍达标；DynamoLLM另设误预测救急与错峰重配置。",
      "conditions": "原流量实验与可用资源条件，不是外部功率骤降实验",
      "strength": "中：直接时间线和协议，未复测",
      "evidence": [
        "RP01-E04",
        "LP03-E2"
      ]
    },
    {
      "id": "F02",
      "mark": "DE",
      "text": "Kareus的优选通信SM分配/启动时刻随频率改变；throttLL’eM记录降频改变批量积累的反馈。",
      "conditions": "训练partition与推理队列两种独立对象，不能合并为同一效应量",
      "strength": "中：两种局部证据，非共同机制复验",
      "evidence": [
        "LP06-E01",
        "RP01-E04"
      ]
    },
    {
      "id": "F03",
      "mark": "DE",
      "text": "这些来源明确采用不同的时间窗、计量组件或服务统计：饱和稳定期、系统执行期、限频代理、小时级Flex吞吐。",
      "conditions": "ML.ENERGY、MLPerf、TAPAS应急表、Phoenix预印本各自范围",
      "strength": "高：定义可定位；不表示某个定义错误",
      "evidence": [
        "LP07-E1",
        "RP02-E1",
        "RP02-E2",
        "LP05-E04",
        "S02-E1",
        "S02-E4"
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
      "chain": "排队/预测误差→通过升频、临时新旧实例并存或转移负载救急→外部额度若禁止这些动作，可行性需重新检查",
      "support": [
        "LP03-E2",
        "RP01-E01",
        "RP01-E04"
      ],
      "conditions": "救急动作所需功率/显存/可路由容量受约束",
      "alternatives": "负载留有足够余量时仍可救急；准入/排队已可能足够；原系统本来不承诺外部cap",
      "status": "局部协议已定位，外部额度联系待检验"
    },
    {
      "id": "M02",
      "related": [
        "P02",
        "F02"
      ],
      "mark": "AI",
      "chain": "频率改变计算时长与资源重叠→通信争用或批量形成改变→原配置排名可能变化",
      "support": [
        "LP06-E01",
        "LP06-E02",
        "RP01-E04"
      ],
      "conditions": "频率/功率动作足以改变瓶颈，画像相对稳定",
      "alternatives": "热状态、输入长度漂移、随机到达也会改变排序；Kareus已经联合优化部分交互",
      "status": "来源内部分支持，不能视为新的普遍规律"
    },
    {
      "id": "M03",
      "related": [
        "P03",
        "F01",
        "F03"
      ],
      "mark": "AI",
      "chain": "窗口排除启动/等待/排空/恢复，或用代理代替真实服务→省略项在有限事件占比可能变大→事件级代价与稳态摘要可能不一致",
      "support": [
        "LP07-E1",
        "RP02-E1",
        "LP05-E04",
        "RP01-E04",
        "S02-E3"
      ],
      "conditions": "遗漏成本非所有策略相同且相对事件窗口足够大；需保持同等输入/质量",
      "alternatives": "长事件与持续高负载可能使省略项可忽略；原benchmark目标本就不同",
      "status": "定义差异已支持，排序改变未证实"
    }
  ],
  "E": [
    {
      "id": "LP03-E1",
      "source_id": "LP03",
      "location": "HPCA 2025 正式记录与论文首页",
      "analysis_path": "../../papers/stojkovic_2025_dynamollm/analysis_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP03-E2",
      "source_id": "LP03",
      "location": "§III-D、§IV-A–E",
      "analysis_path": "../../papers/stojkovic_2025_dynamollm/analysis_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP03-E3",
      "source_id": "LP03",
      "location": "§V-A–C，图7–14",
      "analysis_path": "../../papers/stojkovic_2025_dynamollm/analysis_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP03-E4",
      "source_id": "LP03",
      "location": "§V-D–E，图15–16",
      "analysis_path": "../../papers/stojkovic_2025_dynamollm/analysis_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP05-E01",
      "source_id": "LP05",
      "location": "PDF pp.5–8，§2.2–3.3，图11–15",
      "analysis_path": "../../papers/stojkovic_2025_tapas/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP05-E02",
      "source_id": "LP05",
      "location": "PDF pp.9–11，§4.1–4.6",
      "analysis_path": "../../papers/stojkovic_2025_tapas/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP05-E03",
      "source_id": "LP05",
      "location": "PDF pp.11–12，§5.1–5.3，图19–23",
      "analysis_path": "../../papers/stojkovic_2025_tapas/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP05-E04",
      "source_id": "LP05",
      "location": "PDF pp.12–13，§5.4，表2",
      "analysis_path": "../../papers/stojkovic_2025_tapas/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP06-E01",
      "source_id": "LP06",
      "location": "PDF pp.4–6，§2.3–3.2，图3、表1–2",
      "analysis_path": "../../papers/wu_2026_kareus/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP06-E02",
      "source_id": "LP06",
      "location": "PDF pp.7–10，§4–5",
      "analysis_path": "../../papers/wu_2026_kareus/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP06-E03",
      "source_id": "LP06",
      "location": "PDF pp.10–13，§6.1–6.3，表3–7",
      "analysis_path": "../../papers/wu_2026_kareus/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP06-E04",
      "source_id": "LP06",
      "location": "PDF pp.14–15，§6.4–6.7，表8、图12",
      "analysis_path": "../../papers/wu_2026_kareus/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "RP01-E01",
      "source_id": "RP01",
      "location": "PDF pp.6–8，§IV-A–E",
      "analysis_path": "../../papers/kakolyris_2025_throttllem/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "RP01-E02",
      "source_id": "RP01",
      "location": "PDF pp.8–9，§IV-F",
      "analysis_path": "../../papers/kakolyris_2025_throttllem/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "RP01-E03",
      "source_id": "RP01",
      "location": "PDF pp.9–11，§V-A–D，图7–10",
      "analysis_path": "../../papers/kakolyris_2025_throttllem/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "RP01-E04",
      "source_id": "RP01",
      "location": "PDF pp.11–13，§V-E–F，图11–13",
      "analysis_path": "../../papers/kakolyris_2025_throttllem/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP07-E1",
      "source_id": "LP07",
      "location": "§§2.1–2.4、§3.1–3.3，PDF pp.2–5：边界、稳定期及配置前沿",
      "analysis_path": "../../papers/chung_2025_ml_energy_benchmark/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP07-E2",
      "source_id": "LP07",
      "location": "§4.1–4.3，PDF pp.6–10，Fig3–7：设备、结果与解释",
      "analysis_path": "../../papers/chung_2025_ml_energy_benchmark/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP07-E3",
      "source_id": "LP07",
      "location": "Appendix B.1–B.4，PDF pp.23–25，Fig8–9、Table4–5：抢占/通信/PD/chunk",
      "analysis_path": "../../papers/chung_2025_ml_energy_benchmark/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "LP07-E4",
      "source_id": "LP07",
      "location": "Appendix E，PDF p.28：作者局限",
      "analysis_path": "../../papers/chung_2025_ml_energy_benchmark/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "RP02-E1",
      "source_id": "RP02",
      "location": "§III-C、§IV-A，PDF pp.5–6：误区、窗口和边界",
      "analysis_path": "../../papers/tschand_2025_mlperf_power/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "RP02-E2",
      "source_id": "RP02",
      "location": "§IV-B–D，PDF pp.6–8：分析仪、遥测、估计及报告",
      "analysis_path": "../../papers/tschand_2025_mlperf_power/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "RP02-E3",
      "source_id": "RP02",
      "location": "§V-B/C/F，PDF pp.9–12：规模、长度换算和非随机提交结果",
      "analysis_path": "../../papers/tschand_2025_mlperf_power/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "RP02-E4",
      "source_id": "RP02",
      "location": "§III、§V-C、§VI，PDF pp.4/10/12–13：覆盖与未来方向",
      "analysis_path": "../../papers/tschand_2025_mlperf_power/analysis_broad_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "S02-E1",
      "source_id": "S02",
      "location": "arXiv v1，§§2.1、4.1，表 1，作业组合与 Flex 合约",
      "analysis_path": "../../papers/colangelo_2025_grid_interactive/analysis_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "S02-E2",
      "source_id": "S02",
      "location": "arXiv v1，§§2.2–2.3，图 2–5，事件、基线与性能",
      "analysis_path": "../../papers/colangelo_2025_grid_interactive/analysis_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "S02-E3",
      "source_id": "S02",
      "location": "arXiv v1，§§3.3–3.5，图 6，动作代价、假设与场地边界",
      "analysis_path": "../../papers/colangelo_2025_grid_interactive/analysis_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "S02-E4",
      "source_id": "S02",
      "location": "arXiv v1，§§4.2–4.4、§5，策略、测量与实现",
      "analysis_path": "../../papers/colangelo_2025_grid_interactive/analysis_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "S02-E5",
      "source_id": "S02",
      "location": "作者工件 README 的 Data、AI Orchestration Usage、Emerald AI Conductor Pseudocode",
      "analysis_path": "../../papers/colangelo_2025_grid_interactive/analysis_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "S02-E6",
      "source_id": "S02",
      "location": "data/SRP_exp_performance.csv 的 pt_mpt_13b_sm 记录及 Flexibility 列，图 2 图源",
      "analysis_path": "../../papers/colangelo_2025_grid_interactive/analysis_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    },
    {
      "id": "S02-E7",
      "source_id": "S02",
      "location": "控制端的 checkpoint 与 dvfs_set_power_cap；补丁中的 EmeraldCallback.batch_end、save_interval_fn 和 control_power.py",
      "analysis_path": "../../papers/colangelo_2025_grid_interactive/analysis_v01.md",
      "scope": "原来源设置内的可定位材料；未独立复现"
    }
  ],
  "P_by_F_matrix": [
    {
      "F_id": "F01",
      "P_id": "P01",
      "status": "plausible",
      "reason": "救急动作与启动滞后有来源证据；外部功率使动作不可用尚未测量。",
      "evidence_ids": [
        "LP03-E2",
        "RP01-E04"
      ]
    },
    {
      "F_id": "F01",
      "P_id": "P02",
      "status": "diagnostic-needed",
      "reason": "突发损失可能来自启动时间而非画像排名改变，需区分。",
      "evidence_ids": [
        "RP01-E04"
      ]
    },
    {
      "F_id": "F01",
      "P_id": "P03",
      "status": "supported",
      "reason": "来源内直接显示全轨迹统计与短时违约可并存；不自动证明新的事件级算法必要。",
      "evidence_ids": [
        "RP01-E04"
      ]
    },
    {
      "F_id": "F02",
      "P_id": "P01",
      "status": "diagnostic-needed",
      "reason": "频率影响竞争不代表最高频或shadow动作必不可用，缺统一预算证据。",
      "evidence_ids": [
        "LP06-E01",
        "RP01-E01"
      ]
    },
    {
      "F_id": "F02",
      "P_id": "P02",
      "status": "supported",
      "reason": "来源内已有频率—重叠配置交互与批量反馈；共同动态供电设置未验证。",
      "evidence_ids": [
        "LP06-E01",
        "RP01-E04"
      ]
    },
    {
      "F_id": "F02",
      "P_id": "P03",
      "status": "plausible",
      "reason": "状态反馈可能跨入恢复窗口，但静态/动态能量概念本身不证明恢复损失。",
      "evidence_ids": [
        "LP06-E04",
        "RP01-E04"
      ]
    },
    {
      "F_id": "F03",
      "P_id": "P01",
      "status": "unsupported",
      "reason": "合同和窗口不同不能单独证明救急动作在功率约束下失效。",
      "evidence_ids": [
        "LP05-E04",
        "S02-E1"
      ]
    },
    {
      "F_id": "F03",
      "P_id": "P02",
      "status": "diagnostic-needed",
      "reason": "计量差异可能改变观测排名而非真实执行排序，需区分。",
      "evidence_ids": [
        "LP07-E1",
        "RP02-E1"
      ]
    },
    {
      "F_id": "F03",
      "P_id": "P03",
      "status": "supported",
      "reason": "定义差异直接限制比较解释；不能把小时平均/限频代理等同真实逐请求SLO。",
      "evidence_ids": [
        "LP07-E1",
        "RP02-E1",
        "LP05-E04",
        "S02-E1"
      ]
    }
  ],
  "C": [
    {
      "id": "C01",
      "P_ids": [
        "P01"
      ],
      "F_ids": [
        "F01"
      ],
      "M_ids": [
        "M01"
      ],
      "status": "diagnostic-needed",
      "focus": "供电额度收紧时，节能控制器依赖的服务救急余量是否仍可用？",
      "evidence_ids": [
        "LP03-E2",
        "RP01-E01",
        "RP01-E04",
        "LP05-E02"
      ],
      "scores": {
        "mechanism": 1,
        "evidence": 2,
        "cross_setting": 1,
        "nontriviality": 2,
        "novelty": 1,
        "verifiability": 1
      },
      "score_reason": "局部动作有证据；改变的是可行动作集合而非拼模块；邻近cap系统未穷尽故新颖性1，硬件能力未知故核验1。",
      "already_addressed": "DynamoLLM/throttLL’eM已有预测、shadow、错峰与救急；TAPAS已有设施应急。需与PowerSlider/POLCA及其他cap工作进一步比较。",
      "unknowns": [
        "目标功率信号及窗口",
        "原救急机制在同一约束下能否成功",
        "最近邻已覆盖范围"
      ],
      "first_low_cost_check": "先逐项对读原算法的动作前提，列明满频/新实例所需额外容量；暂不运行。"
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
      "status": "plausible",
      "focus": "离线配置前沿面对运行条件变化时，哪些排名保持、哪些改变？",
      "evidence_ids": [
        "LP06-E01",
        "LP06-E04",
        "RP01-E04"
      ],
      "scores": {
        "mechanism": 1,
        "evidence": 2,
        "cross_setting": 1,
        "nontriviality": 1,
        "novelty": 0,
        "verifiability": 1
      },
      "score_reason": "交互已有直接研究；边界核验可有价值，但联合优化/画像失效本身非新颖。",
      "already_addressed": "Kareus已联合频率和计算/通信调度，throttLL’eM已处理批量反馈；不是再做一次联合调度。",
      "unknowns": [
        "特定变化的必要性",
        "幅度和时间尺度",
        "在线重用策略的相关工作"
      ],
      "first_low_cost_check": "从已读图表标出改变频率时最优配置切换的条件，检查哪些变化已在原方法输入内。"
    },
    {
      "id": "C03",
      "P_ids": [
        "P03"
      ],
      "F_ids": [
        "F01",
        "F03"
      ],
      "M_ids": [
        "M03"
      ],
      "status": "diagnostic-needed",
      "focus": "同等有效工作下，完整事件与恢复窗口是否改变能效/服务排序？",
      "evidence_ids": [
        "LP07-E1",
        "LP07-E3",
        "RP02-E1",
        "RP01-E04",
        "LP05-E04",
        "S02-E3"
      ],
      "scores": {
        "mechanism": 1,
        "evidence": 2,
        "cross_setting": 1,
        "nontriviality": 1,
        "novelty": 0,
        "verifiability": 1
      },
      "score_reason": "窗口差异有证据，排名逆转无证据；测量常识不构成新意，必须定位具体失效与最近邻差异。",
      "already_addressed": "ML.ENERGY附录已有抢占/通信能量，MLPerf已有系统测量；GREEN/OPR等已考虑状态/转换成本。",
      "unknowns": [
        "事件级排名是否真的改变",
        "遗漏项是否有策略相关差异",
        "完整窗口和同质量定义"
      ],
      "first_low_cost_check": "对读测量协议，先核对启动/低负载/排空/恢复是否已纳入，寻找现有时间线及日志可访问性。"
    }
  ],
  "recommended_C": {
    "connection_id": "C01",
    "purpose": "仅作为下一轮补证据入口，不是选题/基线决定"
  },
  "first_low_cost_check": "先对读动作权限、计量窗口与服务统计，补最近邻和源工件可用性；真正实测前另生成诊断Idea/计划。",
  "evidence_audit": {
    "within_source": "关键图表/方法已定位，DE表示原论文材料，不表示独立复测",
    "cross_setting": "训练重叠与推理队列只有互补证据，不是同机制共同实验；供电联系仍AI",
    "independence": "DynamoLLM/TAPAS作者群部分重叠，ML.ENERGY/Kareus作者群部分重叠，不能算独立重复验证",
    "causal_path": "F的局部事实可定位；M到外部功率约束与C的箭头尚待检验",
    "boundaries": "模型、输入、硬件、热状态、任务时长、动作权限与质量均需对齐；统计功效未知"
  },
  "cross_domain_transfer": "不适用：当前未选目标A与源方法B；邻接论文不是迁移完成",
  "handoff_ready": false,
  "actual_next_stage": "analyze-paper/deep-read Mode C补证据及用户导读；暂不进入正式Idea或执行"
}
```
