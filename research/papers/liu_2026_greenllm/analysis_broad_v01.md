# GreenLLM: SLO-Aware Dynamic Frequency Scaling for Energy-Efficient LLM Serving｜广读正文分析

日期：2026-11-05。作者稿§§2–6重点阅读；论文首页正式元数据未解决，未审代码或复现。

## 问题与方法

在线请求负载变化时，如何在节点内针对prefill/decode使用不同的SLO约束调频？

§3：按输入长度分队，prefill依据队列/长度拟合与功率模型求满足TTFT的频率，decode以200ms粗粒度TPS档位加短周期TBT反馈微调；有滞回/限速和边界饱和后的表校正。

## 阅读边界

§4–5指定TTFT/TBT目标并报告通过率和相对能量；引言称node-level，表与结论也称GPU energy，计量边界需澄清。§3.1.1文字cubic与公式二次式不一致；不据此自行补模型。

作者局限：§5.1–5.2承认接近饱和时SLO余量和节能收益下降。

分析者判断：出版字段仍有占位，不能冒称已核对出版社终版。；少量基线不支持优于所有近期专门能效系统；原配置8卡，不直接据此认定六卡可复现。；每阶段节能与变供电上限下的功率履约是不同问题。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-047-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "RPW01"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "RPW01-E1",
    "RPW01-E2",
    "RPW01-E3"
  ],
  "missing_evidence": [
    "正式出版记录与终版差异",
    "node-level/GPU-level能量边界",
    "拟合模型文字和公式差异"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-047-v01",
  "source_identity_and_version": {
    "title": "GreenLLM: SLO-Aware Dynamic Frequency Scaling for Energy-Efficient LLM Serving",
    "authors": [
      "Qunyou Liu",
      "Darong Huang",
      "Marina Zapater",
      "David Atienza"
    ],
    "venue": "DAC 2026（作者机构库登记；出版社记录待补）",
    "year": 2026,
    "official_url": "https://infoscience.epfl.ch/entities/publication/5a812f00-c322-4ff8-a5b4-a890db0bbf93",
    "actual_local_version": "EPFL 机构库公开的 DAC2026_GPU_DVFS_Liu_Huang_CameraReady.pdf，7页，首页 DAC26 但 DOI/ISBN 仍为 X 占位。",
    "local_pdf": "paper_author_dac2026.pdf",
    "reading_coverage": "作者稿§§2–6重点阅读；论文首页正式元数据未解决，未审代码或复现。"
  },
  "research_question": "在线请求负载变化时，如何在节点内针对prefill/decode使用不同的SLO约束调频？",
  "study_type_and_context": "作者机构库会议稿，正式出版身份仍待核验；单DGX-A100节点8卡、PD池分离的服务系统。只作为观察列表，不计20篇正式主池。",
  "design_units_samples_or_materials": "§4/Table2：8×A100 SXM4 40GB，prefill为2 workers×2GPU，decode为1 worker×4GPU；Qwen3-14B与30B MoE；Alibaba不同QPS与降采样Azure流量。",
  "intervention_input_or_procedure": "§3：按输入长度分队，prefill依据队列/长度拟合与功率模型求满足TTFT的频率，decode以200ms粗粒度TPS档位加短周期TBT反馈微调；有滞回/限速和边界饱和后的表校正。",
  "measurements_and_analysis": "§4–5指定TTFT/TBT目标并报告通过率和相对能量；引言称node-level，表与结论也称GPU energy，计量边界需澄清。§3.1.1文字cubic与公式二次式不一致；不据此自行补模型。",
  "results_with_uncertainty": [
    {
      "mark": "DE",
      "finding": "Table4的14B/10QPS下TTFT、TBT通过率分别88.2%/90.9%，高负载收益与履约空间收缩；不能将摘要严格SLO叙述解释为全部设置零违约。",
      "location": "PDF pp.5–6，§5.2/Table4",
      "uncertainty": "小范围负载与两模型，未核验独立重复/区间、计量边界与完整代码。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "SC": "来源提出上述测量/控制方法与设置内结论。",
      "DE": "方法、结果与局限可按evidence_index定位；非本项目独立验证。",
      "AI": "跨论文连接需要重新核验窗口、边界与信息权限。",
      "UN": "代码实现一致性与本机效应。"
    }
  ],
  "limitations_biases_and_ethics": {
    "author_stated": [
      "§5.1–5.2承认接近饱和时SLO余量和节能收益下降。"
    ],
    "analyst_inferred": [
      "出版字段仍有占位，不能冒称已核对出版社终版。",
      "少量基线不支持优于所有近期专门能效系统；原配置8卡，不直接据此认定六卡可复现。",
      "每阶段节能与变供电上限下的功率履约是不同问题。"
    ],
    "ethics": "本轮未独立核查所有数据许可/隐私处理，未使用用户生产数据。"
  },
  "reproducibility_and_transferability": {
    "artifact_status": "阅读公开正文，未运行代码/重新产生结果；材料存在不等于可复现已证实。",
    "scope": "读论文不受六卡限制；落地时重新核验关键机制与测量。"
  },
  "candidate_P": [
    {
      "id": "RPW01-P1",
      "mark": "AI",
      "text": "队列和输出长度剧烈变化时，分阶段调频的有效SLO余量如何随负载变化？",
      "evidence": [
        "RPW01-E1"
      ]
    }
  ],
  "candidate_F": [
    {
      "id": "RPW01-F1",
      "mark": "DE",
      "text": "本文已有phase-aware双环DVFS和滞回；Table4接近饱和时SLO通过率降低。",
      "evidence": [
        "RPW01-E1",
        "RPW01-E3"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "RPW01-E1",
      "location": "§3.1–3.2，PDF pp.3–4：phase-aware建模、双环与滞回",
      "local_pdf": "paper_author_dac2026.pdf"
    },
    {
      "id": "RPW01-E2",
      "location": "§4/Table2–3，PDF pp.4–5：8卡与负载",
      "local_pdf": "paper_author_dac2026.pdf"
    },
    {
      "id": "RPW01-E3",
      "location": "§5.1–5.3/Table4–5，PDF pp.5–6：通过率和负载边界",
      "local_pdf": "paper_author_dac2026.pdf"
    }
  ],
  "unresolved_questions": [
    "正式出版记录与终版差异",
    "node-level/GPU-level能量边界",
    "拟合模型文字和公式差异"
  ]
}
```
