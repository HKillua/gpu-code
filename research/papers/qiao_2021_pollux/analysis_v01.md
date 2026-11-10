# Pollux｜多处理一批样本，能推进多少训练

Aurick Qiao 等，OSDI 2021。本次定位阅读§2.2、§3–4、§5.1–5.3，重点图 3 及表 1–2，阶段为 `analyze-paper`，来源 LS03。下文的实测指作者报告的结果。 [正式书目](https://www.usenix.org/conference/osdi21/presentation/qiao) · [会议全文](https://www.usenix.org/system/files/osdi21-qiao.pdf)

提高每秒处理的样本数不总能让模型更早达到目标质量。Pollux 把吞吐与每个样本带来的统计进度联系起来。选择新配置时，它还扣除换卡、保存和重启造成的时间。 [方法](https://www.usenix.org/system/files/osdi21-qiao.pdf#page=4)

§5.2 表 2 的 64 张 T4 实机合成负载上，默认 Pollux 平均完成时间 0.76 小时，优化过作业配置的 Tiresias 为 1.2 小时，Optimus+Oracle 为 1.5 小时。§5.1 另设重分配惩罚。 作者另测重配时的 checkpoint-restart 延迟为 15–120 秒（正文 Re-allocation penalty 段）。 [评估设置](https://www.usenix.org/system/files/osdi21-qiao.pdf#page=11) · [实验结果](https://www.usenix.org/system/files/osdi21-qiao.pdf#page=12) · [重配耗时](https://www.usenix.org/system/files/osdi21-qiao.pdf#page=10)

可借其度量思路区分每秒样本数、验证目标与重配停顿。六卡的网络和存储会改变吞吐与停顿，现成参数不能直接沿用。给定硬件分配时，它刻画怎样计算有效训练进度。功率预算、W 与 J 的计量以及不同功率设置下的进度，都需另外给出。 本轮未核对代码或做独立测量。

SC 表示作者主张，DE 表示可定位的正文证据，AI 表示本次迁移推断，UN 表示待核验项。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-013-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LS03"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LS03-E1",
    "LS03-E2",
    "LS03-E3",
    "LS03-E4",
    "LS03-E5",
    "LS03-E6"
  ],
  "missing_evidence": [
    "未运行代码或重新获得原日志、梯度统计、随机数据和独立重复结果。",
    "缺少本地 3090 的功率与配置响应、存储带宽和重配时延分布。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-013-v01",
  "source_identity_and_version": {
    "id": "LS03",
    "title": "Pollux: Co-adaptive Cluster Scheduling for Goodput-Optimized Deep Learning",
    "authors": [
      "Aurick Qiao",
      "Sang Keun Choe",
      "Suhas Jayaram Subramanya",
      "Willie Neiswanger",
      "Qirong Ho",
      "Hao Zhang",
      "Gregory R. Ganger",
      "Eric P. Xing"
    ],
    "venue": "OSDI 2021",
    "pages": "1–18",
    "official_url": "https://www.usenix.org/conference/osdi21/presentation/qiao",
    "pdf_url": "https://www.usenix.org/system/files/osdi21-qiao.pdf",
    "material": "正式会议版本，正文图表和节号依 USENIX 公开 PDF，链接页码从封面算起。",
    "coverage": "§2.2、§3–4、§5.1–5.3，重点图 3 及表 1–2。仅查正式论文及书目，本轮未核对代码、数据工件或执行机器。"
  },
  "research_question": "SC｜为训练增加 GPU 或放大批量，得到的每秒样本更多。模型是否也更快达到同一质量目标？",
  "study_type_and_context": "DE｜弹性训练与集群资源分配的共同优化，含云上原型实机对照与基于测量的模拟。各作业以各自任务的验证目标衡量完成。",
  "design_units_samples_or_materials": "DE｜16 台 AWS 节点，各四张 T4，共 64 张。使用 CephFS 保存 checkpoint。从 Microsoft 轨迹抽取 160 个记录，原数据缺模型信息，作者按 GPU 时间分档配给表 1 的训练任务，得到合成工作负载。",
  "intervention_input_or_procedure": "DE｜把样本处理速率与统计效率相乘，估计单位时间的有效训练进度。集群分配 GPU，作业随资源调整批量等训练设置。",
  "measurements_and_analysis": "DE｜训练 goodput 结合每秒处理样本数和相对统计效率。评估报告达到验证目标的平均 JCT、99 分位 JCT 与 makespan。比较时保留已手动调优卡数和批量的基线，并报告不同设置。重配的决策罚分与实际切换时间分别记录。",
  "results_with_uncertainty": [
    {
      "label": "DE",
      "finding": "表 2 默认 Pollux 的平均 JCT 为 0.76 小时，Tiresias+TunedJobs 为 1.2 小时，Optimus+Oracle+TunedJobs 为 1.5 小时。",
      "comparison_and_uncertainty": "同一合成作业组与指定验证目标。作为一次报告的表格结果读取，表 2 没有给出这些均值的置信区间。",
      "evidence": "LS03-E5"
    },
    {
      "label": "DE",
      "finding": "作者测得 checkpoint-restart 导致的重配延迟在 15–120 秒之间，并说明依模型大小、初始化而变。",
      "comparison_and_uncertainty": "这段范围是论文实现的实测，§5.1 决策中 δ=30 秒是估计参数。",
      "evidence": "LS03-E3"
    },
    {
      "label": "DE",
      "finding": "作者报告平均每个作业约每 7 分钟被重分配一次，checkpoint-restart 平均运行时开销为 8%。",
      "comparison_and_uncertainty": "实机整组的累计相对开销，与每次停顿的秒数使用不同分母。",
      "evidence": "LS03-E5"
    }
  ],
  "claim_evidence_implementation_map": [
    "SC｜同时调节集群分配与作业训练参数，可以改善按有效训练进度评价的资源利用。",
    "DE｜§3 联合建模样本吞吐和统计效率，图 3 有实测数据；§4 明示重配罚分依赖作业年龄、已重配次数、估计延迟，§5.1–5.2 分别列参数与运行结果。",
    "AI｜在功率设置变化的六卡机器上，训练进度和存盘停顿都应保留自己的量纲与参照。",
    "UN｜原实验没有本项目的逐机供电轨迹、功率档位性能、实时推理 SLA 或 3090 测量。"
  ],
  "limitations_biases_and_ethics": [
    "需要作业允许改变批量、学习率及资源并支持恢复。goodput 的统计效率由在线估计得出，不同任务、训练阶段与优化器会影响估计。",
    "160 个轨迹记录被映射到作者选择的模型与数据集，因此不能当作完整生产作业重放。",
    "重配折扣沿用历史重配频率的假设，固定估计延迟与各模型实测范围有差别。按有效训练进度建模也不能直接表示在线推理请求的时延。"
  ],
  "reproducibility_and_transferability": {
    "evidence": "论文报告的配置和结果可定位。代码与结果的一致性尚未独立复核，本次不构成复现。",
    "transferable": "可借其度量思路区分每秒样本数、验证目标与重配停顿。六卡的网络和存储会改变吞吐与停顿，现成参数不能直接沿用。",
    "boundary": "间接。给定硬件分配时，它刻画怎样计算有效训练进度。功率预算、W 与 J 的计量以及不同功率设置下的进度，都需另外给出。"
  },
  "candidate_P": [
    {
      "id": "LS03-P1",
      "status": "AI｜候选，待 Mode C 核验",
      "statement": "在资源调整使统计效率和停顿同时变化时，原有吞吐与重配置估计能否保持对训练达到目标所需时间的解释力？",
      "evidence": [
        "LS03-E1",
        "LS03-E2",
        "LS03-E3",
        "LS03-E4",
        "LS03-E5",
        "LS03-E6"
      ]
    }
  ],
  "candidate_F": [
    {
      "id": "LS03-F1",
      "status": "DE｜来源观察，待 Mode C 核验",
      "statement": "测试床上每次重配耗时随模型等因素在 15–120 秒间变化，决策参数取 30 秒，整组运行仍承担了可测的 checkpoint-restart 开销。",
      "evidence": [
        "LS03-E3",
        "LS03-E4",
        "LS03-E5"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "LS03-E1",
      "where": "§2.2、§3，统计效率与 goodput 模型",
      "url": "https://www.usenix.org/system/files/osdi21-qiao.pdf#page=4"
    },
    {
      "id": "LS03-E2",
      "where": "§3 图 3，测得的不同资源和批量下的吞吐",
      "url": "https://www.usenix.org/system/files/osdi21-qiao.pdf#page=8"
    },
    {
      "id": "LS03-E3",
      "where": "§4 的 Re-allocation penalty 段，重配效用折扣与延迟观测",
      "url": "https://www.usenix.org/system/files/osdi21-qiao.pdf#page=10"
    },
    {
      "id": "LS03-E4",
      "where": "§5.1，负载构造、基线调参、重配参数",
      "url": "https://www.usenix.org/system/files/osdi21-qiao.pdf#page=11"
    },
    {
      "id": "LS03-E5",
      "where": "§5.2 表 1–2、System overheads 段，实机结果",
      "url": "https://www.usenix.org/system/files/osdi21-qiao.pdf#page=12"
    },
    {
      "id": "LS03-E6",
      "where": "§5.3，基于测量的模拟",
      "url": "https://www.usenix.org/system/files/osdi21-qiao.pdf#page=13"
    }
  ],
  "unresolved_questions": [
    "不同任务或训练阶段的统计效率变化需要多少观测才能识别？",
    "同一策略在本地存储、两节点通信与其他功率设置下的每次重配代价有多大？"
  ]
}
```
