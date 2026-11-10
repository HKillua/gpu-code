# POLCA · ACM ASPLOS 2024, Volume 3

Characterizing Power Management Opportunities for LLMs in the Cloud

LP02 · AN-20261105-006-v01 · 2026-11-05 · analyze-paper · partial

一列机柜可能装得下更多 GPU 服务器，但多台同时冲到高功率时会超过供电额度。POLCA 研究何时先给低优先级服务器限频，以及在什么条件下也必须限制高优先级。 [正式记录](https://doi.org/10.1145/3620666.3651329)

看图12后接着看表6：先弄清读数来自哪里，再看两类工作分别允许损失多少延迟。§6.4 解释为何要从生产总功率生成模拟请求。 [论文正文](https://www.microsoft.com/en-us/research/wp-content/uploads/2024/03/GPU_Power_ASPLOS_24.pdf)

**阅读边界。** 同一项研究有两种证据：机房功率的生产观测，以及增加服务器后延迟的仿真结果。外推时必须分别使用。

## ANALYSIS_PACKAGE

SC 为作者主张，DE 为正文可定位的设计或观察，AI 为本组推断，UN 为未核验项。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-006-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LP02"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LP02-E1 ASPLOS 2024 正式记录及作者机构出版页",
    "LP02-E2 §3–5、§6.2–6.3，图12、表5–6",
    "LP02-E3 §6.4–6.6，图13、16、17及表6"
  ],
  "missing_evidence": [
    "UN：原始生产 PDU 轨迹、原请求序列以及仿真器发布状态未核验。",
    "UN：两机六卡的计量层级、执行时延和可达阈值都需设备证据。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-006-v01",
  "source_identity_and_version": {
    "title": "Characterizing Power Management Opportunities for LLMs in the Cloud",
    "authors": [
      "Pratyush Patel",
      "Esha Choukse",
      "Chaojie Zhang",
      "Íñigo Goiri",
      "Brijesh Warrier",
      "Nithish Mahalingam",
      "Ricardo Bianchini"
    ],
    "year": 2024,
    "venue": "ACM ASPLOS 2024, Volume 3",
    "pages": "207–222",
    "doi": "10.1145/3620666.3651329",
    "official_url": "https://doi.org/10.1145/3620666.3651329",
    "pdf_url": "https://www.microsoft.com/en-us/research/wp-content/uploads/2024/03/GPU_Power_ASPLOS_24.pdf",
    "publication_status": "正式发表"
  },
  "research_question": "同一列机柜的额定供电固定，若接入更多推理服务器，怎样在功率逼近上限时优先保护紧急任务？",
  "study_type_and_context": "生产功率特性研究与策略模拟结合；行级配电单元（PDU）为控制边界，策略关注推理集群增加可接入的服务器。",
  "design_units_samples_or_materials": {
    "DE": "模型测量刻画提示输入与逐 token 生成的功率；§6.4 用六周生产总功率和 BLOOM-176B 的不同推理配置构造请求到达和长度。",
    "evidence": "LP02-E2，LP02-E3"
  },
  "intervention_input_or_procedure": {
    "SC": "机柜控制器通过带外通路向服务器下发限频。历史最大功率上冲决定安全裕量；按优先级分两级介入，用较低的恢复阈值减少往复触发。",
    "evidence": "LP02-E2"
  },
  "measurements_and_analysis": {
    "DE": "表6用相对基线的 P50、P99 延迟影响定义高低优先级的不同容忍度。§6.5 扫阈值，§6.6 比较双阈值、单阈值及不预先限频等策略。",
    "evidence": "LP02-E2，LP02-E3"
  },
  "results_with_uncertainty": [
    {
      "label": "DE",
      "setting": "图13，表6；高负载、模型已载入，模拟向原集群增添服务器。低优先级允许的 P99 延迟退化比高优先级宽。",
      "observation": "在选定的双阈值和模拟混合负载下，两类延迟均在约定范围内。更早触发会使低优先级延迟代价上升。",
      "uncertainty": "结果依赖合成请求、优先级组成、历史功率上冲和执行控制所需时长；生产功率轨迹是输入，策略收益来自仿真。",
      "evidence": "LP02-E3"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "claim": "SC：读机柜列 PDU 的总功率。低阈值先限制低优先级任务的频率，更高阈值再升级控制；恢复阈值留有间隔。",
      "support": [
        "LP02-E2",
        "LP02-E3"
      ],
      "implementation_status": "核对论文报告的方法及关键评估段落；本轮未运行作者代码或独立复测。"
    }
  ],
  "limitations_biases_and_ethics": {
    "AI": "生产聚合功率拟合得准，仍可能对应多种请求序列或排队行为。同一合约下的超额配置与外部瓦数曲线是两种设施约束。",
    "ethics": "原生产请求的内容与个体数据不在本次核验材料中。",
    "evidence": "LP02-E3"
  },
  "reproducibility_and_transferability": {
    "DE": "设备测量和仿真过程见 §3–6；跨平台时应区分 PDU 和 GPU 遥测。",
    "AI-六卡": "六卡可借鉴优先级分组和延迟指标。如果只有 GPU 遥测，记录清楚测量边界；文中 PDU 观察整列服务器。",
    "UN": "原生产序列与可执行模拟器的公开获取及版本尚未核实。"
  },
  "candidate_P": [
    {
      "label": "AI",
      "text": "在限制的总瓦数逼近时，怎样让低优先级承担更多限频代价，又让它自己的延迟仍然合约达标？",
      "evidence": [
        "LP02-E2",
        "LP02-E3"
      ]
    }
  ],
  "candidate_F": [
    {
      "label": "DE",
      "text": "阈值更保守时，仿真中低优先级请求更早受限，其延迟会令部分策略违反自身服务目标。",
      "evidence": [
        "LP02-E3"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "LP02-E1",
      "locator": "ASPLOS 2024 正式记录及作者机构出版页",
      "detail": "确认书目信息与模拟属性。",
      "url": "https://www.microsoft.com/en-us/research/publication/characterizing-power-management-opportunities-for-llms-in-the-cloud/"
    },
    {
      "id": "LP02-E2",
      "locator": "§3–5、§6.2–6.3，图12、表5–6",
      "detail": "实测、带外控制链、两档介入和优先级合同。",
      "url": "https://www.microsoft.com/en-us/research/wp-content/uploads/2024/03/GPU_Power_ASPLOS_24.pdf"
    },
    {
      "id": "LP02-E3",
      "locator": "§6.4–6.6，图13、16、17及表6",
      "detail": "合成请求来源、阈值敏感性、策略模拟和延迟。",
      "url": "https://www.microsoft.com/en-us/research/wp-content/uploads/2024/03/GPU_Power_ASPLOS_24.pdf"
    }
  ],
  "unresolved_questions": [
    "UN：原始生产 PDU 轨迹、原请求序列以及仿真器发布状态未核验。",
    "UN：两机六卡的计量层级、执行时延和可达阈值都需设备证据。"
  ]
}
```
