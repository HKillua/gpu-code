# Sailor｜来源分析包

AN-20261105-019-v01 · SOSP 2025 正式会议论文 · 2026-11-05 · partial。

GPU 类型、链路与可用机器变化后，训练作业如何选出可放入显存、且吞吐/费用合适的并行方案？ 用实测模型与链路 profile 驱动运行时间/显存估计，再搜索机器组合、数据/流水线/张量并行度和 microbatch，运行中资源变化可重新部署。

**本轮读到的证据。** §5.5 的指定事件中，作者报告规划 0.1 秒、清理 3 秒、广播 1.25 秒、重建 NCCL 组 4.5 秒、重定义模型/优化器 2 秒、重建 dataloader 0.5 秒。它们说明运行时状态操作可能比搜计划更慢；该事件未报告这些阶段的重复置信区间，不能换算成别的模型或机器的固定开销。

**与六张 3090 的联系。** 在能运行的固定小模型上区分参数/优化器、通信组与数据加载器的恢复时间，并确定何时出现第一步有效工作。

[正式发表](https://doi.org/10.1145/3731569.3764839) · [正式会议版正文](https://anakli.inf.ethz.ch/papers/sailor_sosp25.pdf) · [作者工件记录](https://doi.org/10.5281/zenodo.17007125)

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-019-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LN03"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "https://doi.org/10.1145/3731569.3764839",
    "https://anakli.inf.ethz.ch/papers/sailor_sosp25.pdf"
  ],
  "missing_evidence": [
    "已核定点正文；尚未核对完整代码、原始日志、独立重复结果与目标 GPU 环境。",
    "调度时变供电上限与 3090 上的因果关系仍待建立。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-019-v01",
  "source_identity_and_version": {
    "title": "Sailor: Automating Distributed Training over Dynamic, Heterogeneous, and Geo-distributed Clusters",
    "authors": [
      "Foteini Strati",
      "Zhendong Zhang",
      "George Manos",
      "Ixeia Sánchez Périz",
      "Qinghao Hu",
      "Tiancheng Chen",
      "Berk Buzcu",
      "Song Han",
      "Pamela Delgado",
      "Ana Klimovic"
    ],
    "venue": "SOSP",
    "year": 2025,
    "version": "正式会议论文；本包按会议版页/节/图记录",
    "official_url": "https://doi.org/10.1145/3731569.3764839",
    "verification_url": "https://sigops.org/s/conferences/sosp/2025/accepted.html",
    "pdf_url": "https://anakli.inf.ethz.ch/papers/sailor_sosp25.pdf"
  },
  "research_question": "GPU 类型、链路与可用机器变化后，训练作业如何选出可放入显存、且吞吐/费用合适的并行方案？",
  "study_type_and_context": "用实测模型与链路 profile 驱动运行时间/显存估计，再搜索机器组合、数据/流水线/张量并行度和 microbatch，运行中资源变化可重新部署。",
  "design_units_samples_or_materials": "比较同构、异构和跨区训练策略及模型。§5.2 包含小规模真实 A100/V100 测量和较大规模仿真，分别报告。§5.5 用 OPT-350M、16 张 V100 起始并增加 4 张，记录一次重配置过程的各阶段用时。",
  "intervention_input_or_procedure": "先 profile 每类 GPU 的计算和链路成本，用模型估计每个配置的显存及迭代时间。规划器以动态规划和剪枝联合选机器、并行策略与 microbatch；运行时协调清理旧组、传新计划、重建通信与模型状态。",
  "measurements_and_analysis": "评估迭代吞吐、峰值显存估计误差、搜索时间、成本/迭代和 OOM。图 10 比较真实小集群，图 12 是五个云区的仿真。§5.5 单列重新规划、清理、广播、通信组初始化和对象重建。",
  "results_with_uncertainty": "§5.5 的指定事件中，作者报告规划 0.1 秒、清理 3 秒、广播 1.25 秒、重建 NCCL 组 4.5 秒、重定义模型/优化器 2 秒、重建 dataloader 0.5 秒。它们说明运行时状态操作可能比搜计划更慢；该事件未报告这些阶段的重复置信区间，不能换算成别的模型或机器的固定开销。",
  "claim_evidence_implementation_map": [
    {
      "claim": "规划器同时估算速度与放得下的显存",
      "evidence_id": "LN03-E1",
      "boundary": "正文给出输入、方法、模拟器和运行时。",
      "implementation_review": "仅按正文核查，未执行工件。"
    },
    {
      "claim": "实测与大规模仿真两类材料支持不同结论",
      "evidence_id": "LN03-E2",
      "boundary": "不能将五区仿真当作生产云上线。",
      "implementation_review": "仅按正文核查，未执行工件。"
    },
    {
      "claim": "重配置有计划之外的阶段开销",
      "evidence_id": "LN03-E3",
      "boundary": "单事件数据可定位，绝对量外推待验证。",
      "implementation_review": "仅按正文核查，未执行工件。"
    }
  ],
  "limitations_biases_and_ethics": "§6 讨论规划器/模拟器限于 1F1B，及跨网络抖动、丢包和跨设备协议等条件。部分基线只接收固定拓扑，Sailor 同时选资源拓扑；跨系统差异包含更大的动作空间。这里的预算是金额。伦理细节未专门审查。",
  "reproducibility_and_transferability": "已查看作者公开的 SOSP 工件记录，未运行代码或验证硬件。小集群可学习将计划搜索时间与可用训练时间分开记录，实测状态大小、PCIe/网络路径与框架配置。不能将原优化器输出或 V100 阶段耗时当成 3090 结果。",
  "candidate_P": [
    {
      "id": "LN03-P1",
      "text": "当可用设备配置变化，哪些重配置阶段占用了预算恢复后的工作窗口？",
      "evidence_ids": [
        "LN03-E1",
        "LN03-E3"
      ],
      "status": "候选，待 Mode C 核查"
    }
  ],
  "candidate_F": [
    {
      "id": "LN03-F1",
      "text": "在作者的 16→20 张 V100、OPT-350M 重配置中，NCCL 组初始化用时超过计划搜索。",
      "evidence_ids": [
        "LN03-E3"
      ],
      "status": "来源内观察"
    }
  ],
  "evidence_index": [
    {
      "id": "LN03-E1",
      "location": "§4、§5.1，图 5–6",
      "observation": "资源和并行计划、运行时间/显存估计与校准。"
    },
    {
      "id": "LN03-E2",
      "location": "§5.2、图 10 与图 12",
      "observation": "单区硬件验证与大规模跨区仿真的区分。"
    },
    {
      "id": "LN03-E3",
      "location": "§5.5 Reconfiguration overheads；§6 Discussion",
      "observation": "16+4 V100 的重建过程和框架适用边界。"
    }
  ],
  "unresolved_questions": [
    "原论文的各阶段分解在不同大小模型、设备与存储下如何变化？",
    "不同恢复阶段有多少成本来自通信组、模型定义、数据状态或参数传递？"
  ]
}
```
