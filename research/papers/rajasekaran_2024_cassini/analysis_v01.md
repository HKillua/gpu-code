# CASSINI｜来源分析包

AN-20261105-017-v01 · NSDI 2024 正式会议论文 · 2026-11-05 · partial。

多个训练作业共用网络链路时，何种放置会让它们的通信互相阻塞？ 用单作业迭代内的通信周期描述链路需求，选择能错开通信的作业放置和起始时间偏移，并校正相位漂移。

**本轮读到的证据。** 图 11(b) 对照 Themis，加入 CASSINI 后，平均迭代时间加速 1.6×、p99 加速 1.8×。图 17 的三个快照在通信相位漂移超过理想迭代时间的 5% 时触发修正，各自平均少于每分钟两次。选定图报告 CDF 和场景间变动，未核到这些加速比的跨重复置信区间。

**与六张 3090 的联系。** 用两个跨机作业的迭代时间线辨认 NCCL 通信碰撞，需要先核实两机链路确实共享并可观测。

[正式发表](https://www.usenix.org/conference/nsdi24/presentation/rajasekaran) · [正式会议版正文](https://www.usenix.org/system/files/nsdi24-rajasekaran.pdf)

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-017-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LN01"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "https://www.usenix.org/conference/nsdi24/presentation/rajasekaran",
    "https://www.usenix.org/system/files/nsdi24-rajasekaran.pdf"
  ],
  "missing_evidence": [
    "已核定点正文；尚未核对完整代码、原始日志、独立重复结果与目标 GPU 环境。",
    "调度时变供电上限与 3090 上的因果关系仍待建立。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-017-v01",
  "source_identity_and_version": {
    "title": "CASSINI: Network-Aware Job Scheduling in Machine Learning Clusters",
    "authors": [
      "Sudarsanan Rajasekaran",
      "Manya Ghobadi",
      "Aditya Akella"
    ],
    "venue": "NSDI",
    "year": 2024,
    "version": "正式会议论文；本包按会议版页/节/图记录",
    "official_url": "https://www.usenix.org/conference/nsdi24/presentation/rajasekaran",
    "verification_url": "https://www.usenix.org/conference/nsdi24/presentation/rajasekaran",
    "pdf_url": "https://www.usenix.org/system/files/nsdi24-rajasekaran.pdf"
  },
  "research_question": "多个训练作业共用网络链路时，何种放置会让它们的通信互相阻塞？",
  "study_type_and_context": "用单作业迭代内的通信周期描述链路需求，选择能错开通信的作业放置和起始时间偏移，并校正相位漂移。",
  "design_units_samples_or_materials": "主测试床 24 台服务器，每台 1 张 40 GB A100 和 1 张 50 Gbps ConnectX-5，使用 RoCEv2。13 个模型含数据并行与模型并行。考察不同到达方式、作业组合与放置。§5.6 另有每服务器多卡情景。",
  "intervention_input_or_procedure": "取作业独立训练时的计算和通信轨迹，把周期内的需求绕成一个圆，在共享链路上平移候选相位。将作业与链路建立二部图，计算候选放置的兼容度并输出每个作业一致的偏移。运行时偏移越过阈值再校正。",
  "measurements_and_analysis": "使用 Themis、Pollux 及其叠加 CASSINI 的版本等作对照。观察迭代时间分布、p99 和 ECN 标记量，并记录时移修正频率。图 11(b) 的单位为图 11(a) 所含训练迭代。",
  "results_with_uncertainty": "图 11(b) 对照 Themis，加入 CASSINI 后，平均迭代时间加速 1.6×、p99 加速 1.8×。图 17 的三个快照在通信相位漂移超过理想迭代时间的 5% 时触发修正，各自平均少于每分钟两次。选定图报告 CDF 和场景间变动，未核到这些加速比的跨重复置信区间。",
  "claim_evidence_implementation_map": [
    {
      "claim": "周期性通信可用相位错开",
      "evidence_id": "LN01-E1",
      "boundary": "图示/设计直接可核；应在目标网络观察实际相位。",
      "implementation_review": "仅按正文核查，未执行工件。"
    },
    {
      "claim": "同一调度器加入相位/放置模块后改变迭代时间",
      "evidence_id": "LN01-E2",
      "boundary": "仅对应指定到达与模型组合。",
      "implementation_review": "仅按正文核查，未执行工件。"
    },
    {
      "claim": "相位修正开销较低",
      "evidence_id": "LN01-E3",
      "boundary": "三个快照的频率证据。",
      "implementation_review": "仅按正文核查，未执行工件。"
    }
  ],
  "limitations_biases_and_ethics": "§6 假定作业独占 GPU，训练流量与存储等流量分开；链路上有太多作业或迭代形态变化时，互补相位能否稳定存在待考察。论文证据覆盖原测试床。数据隐私与伦理细节本轮未专门审查。",
  "reproducibility_and_transferability": "已核对正式正文的实验条件与图表，源码执行与 trace 对齐未知。小规模可借鉴共享链路上的相位测量；需核实本地网络时钟、NCCL、拓扑、背景流和采样，再判断观测是否可比。",
  "candidate_P": [
    {
      "id": "LN01-P1",
      "text": "作业放置固定时，共享链路上的计算/通信相位关系如何随迭代变化？",
      "evidence_ids": [
        "LN01-E1",
        "LN01-E3"
      ],
      "status": "候选，待 Mode C 核查"
    }
  ],
  "candidate_F": [
    {
      "id": "LN01-F1",
      "text": "在论文的三个快照中，用作者的 5% 偏差阈值，平均每分钟修正相位少于两次。",
      "evidence_ids": [
        "LN01-E3"
      ],
      "status": "来源内观察"
    }
  ],
  "evidence_index": [
    {
      "id": "LN01-E1",
      "location": "§3、图 4–5，§4、图 8–9 与算法 1/2",
      "observation": "单链路错开与跨链路协调。"
    },
    {
      "id": "LN01-E2",
      "location": "§5.1–5.2，图 10 和图 11(b)",
      "observation": "测试床、基线、Poisson 到达下的迭代时间。"
    },
    {
      "id": "LN01-E3",
      "location": "§5.7 图 17、§6 Discussion and Limitations",
      "observation": "修正频率、阈值、独占 GPU 与流隔离假设。"
    }
  ],
  "unresolved_questions": [
    "功率受限时，原单作业 profile 的周期仍然稳定多久？",
    "本地是否有共同瓶颈链路和可重复的相位信号？"
  ]
}
```
