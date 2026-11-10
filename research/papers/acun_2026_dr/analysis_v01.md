# S01｜AI 数据中心需求响应：来源分析 v01

阶段：analyze-paper；分析日期：2026-11-05。ID：AN-20261105-001-v01。

这篇论文适合用来理解“外部功率要求 → 运行策略 → 任务性能”的基本关系。它采用实机测得的任务曲线构建仿真，再在作者定义的服务条件下寻找可承诺的调节量。阅读时最重要的两点是区分实测与仿真，以及先看服务约定再解读灵活性。

论文中的长训练作业不被抢占，因此主要通过降低功率继续运行；短批处理任务周转更频繁，可以改变活跃节点数。这是特定策略的行为，并非训练天生不能暂停、推理天生可以暂停。

对本项目，可直接借鉴的是功率与服务曲线的测量思路；3090、交互请求和恢复开销的结论需要自己的证据。下列 P/F 仅作为 Mode C 输入，尚未构成新的 Idea 或本机观察。

原文：[本地 PDF](paper.pdf) · [作者发布版本](https://www.bu.edu/peaclab/files/2026/03/FlexDC_Sim_ACM_E_Energy26.pdf)。

## ANALYSIS_PACKAGE

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-001-v01",
  "analysis_id": "AN-20261105-001-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "S01"
  ],
  "source_identity_and_version": {
    "source_id": "S01",
    "title": "Investigating Power Consumption Flexibility of AI Data Centers for Demand Response Participation",
    "authors": [
      "Fatih Acun",
      "Can Hankendi",
      "Ethan Levine",
      "Hudson Reynolds",
      "Joshua Bardwick",
      "Ayse K. Coskun"
    ],
    "publication": "ACM e-Energy 2026, pp.344–348",
    "doi": "10.1145/3744255.3798112",
    "local_original": "paper.pdf",
    "original_sha256": "c7ea44cc17c6e90c5b704bbf968d1a9b5fbf41fa978e0fb3b49bbd07f9b8576a",
    "read_material": "本地 study/raw.txt 的全部五页；在线核对作者机构发布的同题 PDF。图表数值仅按正文报告，不额外目测推算。",
    "online_source": "https://www.bu.edu/peaclab/files/2026/03/FlexDC_Sim_ACM_E_Energy26.pdf"
  },
  "research_question": "在论文指定的 AI 工作负载和 QoS 定义下，数据中心可为 EDR/RSR 提供多少功率灵活性？",
  "study_type_and_context": "真实硬件离线 profiling + 合成工作负载 + 需求响应仿真与优化；不是千节点现场试验。",
  "design_units_samples_or_materials": {
    "hardware": "Chameleon Cloud，节点四张 NVIDIA P100。",
    "jobs": "ResNet-50、GPT-2、Llama-7B、BLOOM-560M 的训练片段与批处理推理。",
    "traces": "五组合成轨迹，泊松到达；负载和 QoS 阈值见表2。",
    "simulated_cluster": "1000 个服务器；此规模为仿真。",
    "repetitions_and_uncertainty": "本文未提取到可用于跨随机种子置信区间的完整报告；不写成已验证稳健性。"
  },
  "intervention_input_or_procedure": "离线测功率上限与运行时长；仿真运行策略依次调活跃节点数与运行任务功率上限，模拟退火选择功率投标。训练任务在该策略中不支持抢占。",
  "measurements_and_analysis": {
    "power": "RSR 跟踪 P_target，EDR 相对基线削减；两者与单纯上界问题不同。",
    "qos": "Q=(T_so-T_min)/T_min；考察每类任务相对排队+执行时间退化分位。",
    "cost": "能量经济成本和响应补偿/惩罚；不是只看完成同样工作耗多少 Wh。"
  },
  "results_with_uncertainty": [
    {
      "mark": "SC",
      "finding": "论文报告 RSR 灵活性为平均功率的18%–55%，EDR 削减最高约20%。",
      "conditions": "特定 profiles、合成轨迹、QoS 与调频参数；不是通用 GPU 可调比例。",
      "uncertainty": "不同轨迹下的范围，不等于统计置信区间。",
      "location": "§4–5，pp.347，Figs.2–6。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "mark": "DE",
      "claim": "硬件实测与大规模仿真分层",
      "location": "§3.3 表1–2；§4 首段",
      "implementation_audit": "阅读协议和正文，未读取/运行作者模拟器代码。"
    },
    {
      "mark": "DE",
      "claim": "训练不支持抢占；控制手段随作业周转速度变化",
      "location": "§3.2、§4 Power Tracking / Fig.6",
      "implementation_audit": "是作者策略与仿真内的观察，非任意训练系统限制。"
    },
    {
      "mark": "AI",
      "claim": "阈值 Q=3–5 允许相应分位的总驻留时间为理想执行的4–6倍，因此不能直接替代在线 token SLO。",
      "location": "§3.1 Eq.(8)、§3.3 表2",
      "implementation_audit": "由指标代数推导，不能推广为在线服务性能保证。"
    }
  ],
  "limitations_biases_and_ethics": [
    "P100 与六张 RTX3090 的功率—性能关系需重测。",
    "批处理推理、泊松到达与相对 slowdown 不能表示全部交互业务。",
    "未建模抢占策略；本文不能评价反复 checkpoint/释放重载。",
    "未独立复现、未审计全部实现与随机性；其他资料中未见的细节保留未知。"
  ],
  "reproducibility_and_transferability": {
    "assessment": "partial：原论文和本地提取材料可用；复现等级尚未经运行审计。",
    "artifact": "https://github.com/peaclab/flexdc-sim",
    "reusable_principle": "先测目标硬件的服务曲线，再评估给定电力目标下业务约定是否仍可满足。",
    "must_revalidate": "3090 曲线、混合业务合同、事件时间尺度、切换与恢复代价。"
  },
  "candidate_P": [
    {
      "id": "S01-Pa",
      "mark": "AI",
      "text": "使用批处理相对 slowdown 得到的灵活性，如何映射到在线时延约束？",
      "evidence_ids": [
        "S01-E02"
      ]
    },
    {
      "id": "S01-Pb",
      "mark": "AI",
      "text": "当控制动作包含抢占与恢复时，原有可调区间是否改变？",
      "evidence_ids": [
        "S01-E03"
      ],
      "status": "目标场景尚无实测。"
    }
  ],
  "candidate_F": [
    {
      "id": "S01-Fa",
      "mark": "DE",
      "text": "作者 profiles/工作负载中的性能随功率档和任务而变化。",
      "evidence_ids": [
        "S01-E01"
      ]
    },
    {
      "id": "S01-Fb",
      "mark": "DE",
      "text": "在作者策略中，长训练任务主要依赖功率封顶，短批处理更能靠任务周转改变活跃节点数。",
      "evidence_ids": [
        "S01-E03"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "S01-E01",
      "location": "paper.pdf §3.3, p.346, Tables1–2；study/raw.txt PAGE3",
      "supports": "真实 profile 与工作负载条件。"
    },
    {
      "id": "S01-E02",
      "location": "paper.pdf §3.1 Eq.(8), p.345；Table2, p.346",
      "supports": "QoS定义与阈值。"
    },
    {
      "id": "S01-E03",
      "location": "paper.pdf §3.2, p.346；§4 Power Tracking / Fig.6, p.347",
      "supports": "运行策略、不抢占与控制手段差异。"
    },
    {
      "id": "S01-E04",
      "location": "paper.pdf §4–5, p.347",
      "supports": "作者报告的灵活性范围及适用条件。"
    }
  ],
  "unresolved_questions": [
    "作者代码的版本、profiles 与正文是否逐项一致？",
    "真实在线服务合同下的灵活性与6×3090测量均未知。",
    "实际事件间隔、供电预算来源和节点网络条件尚未确定。"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "paper.pdf §§2–5, pp.345–347",
    "study/raw.txt PAGE1–5"
  ],
  "missing_evidence": [
    "未审计作者代码或复现实验。",
    "缺本项目 GPU/服务器 profile、服务合同、功率事件和网络测量。",
    "跨设置效果及新颖性尚未验证。"
  ],
  "next_skill": "deep-read Mode C"
}
```
