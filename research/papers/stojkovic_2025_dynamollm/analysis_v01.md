# DynamoLLM · IEEE HPCA 2025

DynamoLLM: Designing LLM Inference Clusters for Performance and Energy Efficiency

LP03 · AN-20261105-007-v01 · 2026-11-05 · analyze-paper · partial

短问题和长回答会以不同方式占用 GPU。DynamoLLM 先按请求特征分组，再让响应快、响应慢的控制动作分别调节频率、并行度和实例数量。 [正式记录](https://ieeexplore.ieee.org/document/10946802)

图7和图8应当一起读。一小时会话回放里，相对单池基线，系统耗电下降，最慢一部分请求也改善，但典型请求的等待时间上升。 [论文正文](https://iacoma.cs.uiuc.edu/iacoma-papers/hpca25_2.pdf)

**阅读边界。** 控制器预判的是流量和输出长度。§V-D 的单日回放运行在真实硬件上，§V-E 的周尺度结果来自仿真。

## ANALYSIS_PACKAGE

SC 为作者主张，DE 为正文可定位的设计或观察，AI 为本组推断，UN 为未核验项。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-007-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LP03"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LP03-E1 HPCA 2025 正式记录与论文首页",
    "LP03-E2 §III-D、§IV-A–E",
    "LP03-E3 §V-A–C，图7–14",
    "LP03-E4 §V-D–E，图15–16"
  ],
  "missing_evidence": [
    "UN：完整代码与长时私有轨迹的获取、复现误差及重复运行统计未核验。",
    "UN：两台 3090 服务器能承载的模型、并行度和分组数量需以本机证据确定。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-007-v01",
  "source_identity_and_version": {
    "title": "DynamoLLM: Designing LLM Inference Clusters for Performance and Energy Efficiency",
    "authors": [
      "Jovan Stojkovic",
      "Chaojie Zhang",
      "Íñigo Goiri",
      "Josep Torrellas",
      "Esha Choukse"
    ],
    "year": 2025,
    "venue": "IEEE HPCA 2025",
    "pages": "1348–1362",
    "doi": "10.1109/HPCA61900.2025.00102",
    "official_url": "https://ieeexplore.ieee.org/document/10946802",
    "pdf_url": "https://iacoma.cs.uiuc.edu/iacoma-papers/hpca25_2.pdf",
    "publication_status": "正式发表"
  },
  "research_question": "长短请求混合且流量有峰谷时，怎样在满足在线响应目标的同时降低推理能耗？",
  "study_type_and_context": "面向在线 LLM 服务的请求预测和分层控制。用硬件集群短时及单日回放，再用一周轨迹做较大规模仿真。",
  "design_units_samples_or_materials": {
    "DE": "§V-A 报告 8×H100 服务器，对外展示 Llama2-70B。会话和编程服务轨迹时长不同；§V-B 的一小时会话实验用 12 台服务器为基线承载峰值。",
    "evidence": "LP03-E3，LP03-E4"
  },
  "intervention_input_or_procedure": {
    "SC": "用输入和预测输出长度分池；按不同时间尺度调节点数、单实例张量并行度及频率。误判时可在队列中提优先级、升频或重分派尚未执行的请求。",
    "evidence": "LP03-E2"
  },
  "measurements_and_analysis": {
    "DE": "图7记录总能耗，图8拆分首 token 等待（TTFT）及 token 间隔（TBT）的分位数。图12改变输出长度预测的错分率，图14比较池数。",
    "evidence": "LP03-E3"
  },
  "results_with_uncertainty": [
    {
      "label": "DE",
      "setting": "§V-B 图7–8，一小时公开 Conversation 轨迹回放，Llama2-70B，12 台 H100 服务器作为 SinglePool 基线的峰值容量；参照为 TP8 和最高频率单池。",
      "observation": "综合策略降低能耗，P99 首 token 和 token 间隔变短，P50 两项均变长。",
      "uncertainty": "图中是指定流量和配置的分位数。基线和完整系统同时改变分池与多种动作，单项贡献需参考文中分别缩放单个控制项的基线。",
      "evidence": "LP03-E3"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "claim": "SC：按输入长度和预测输出长度分池；分别调节实例数、张量并行度及 GPU 频率，估算配置变更的时间开销。",
      "support": [
        "LP03-E2",
        "LP03-E3"
      ],
      "implementation_status": "核对论文报告的方法及关键评估段落；本轮未运行作者代码或独立复测。"
    }
  ],
  "limitations_biases_and_ethics": {
    "AI": "可调资源范围、请求长度分布与分池规模决定能否省电。周尺度结论由模拟器给出，不能视作周级硬件验证。",
    "ethics": "小时级公开轨迹与更长的服务轨迹可获得性不同。未独立检查其中隐私处理。",
    "evidence": "LP03-E3，LP03-E4"
  },
  "reproducibility_and_transferability": {
    "DE": "正文给出控制器、画像和分层实验；§V-A 明示小时级轨迹开源。",
    "AI-六卡": "首 token 等待时间和后续 token 间隔可分别观察。原型使用每机 8 张 H100，两机共六张 3090 的张量并行和实例池条件不同。",
    "UN": "此轮未定位能独立还原全部控制器和长轨迹的工件版本。"
  },
  "candidate_P": [
    {
      "label": "AI",
      "text": "请求分型和多个可调部件如何共同影响在线推理的能耗与尾延迟？",
      "evidence": [
        "LP03-E2",
        "LP03-E3"
      ]
    }
  ],
  "candidate_F": [
    {
      "label": "DE",
      "text": "在 §V-B 的同一小时回放中，完整系统相对 SinglePool 的中位延迟与尾部延迟变化方向相反。",
      "evidence": [
        "LP03-E3"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "LP03-E1",
      "locator": "HPCA 2025 正式记录与论文首页",
      "detail": "题名、作者、正式版本。",
      "url": "https://ieeexplore.ieee.org/document/10946802"
    },
    {
      "id": "LP03-E2",
      "locator": "§III-D、§IV-A–E",
      "detail": "各类重配开销、分层控制、请求预测与误判处理。",
      "url": "https://iacoma.cs.uiuc.edu/iacoma-papers/hpca25_2.pdf"
    },
    {
      "id": "LP03-E3",
      "locator": "§V-A–C，图7–14",
      "detail": "硬件、基线、一小时运行和长度预测误差敏感性。",
      "url": "https://iacoma.cs.uiuc.edu/iacoma-papers/hpca25_2.pdf"
    },
    {
      "id": "LP03-E4",
      "locator": "§V-D–E，图15–16",
      "detail": "单日硬件实验与一周大规模模拟分开。",
      "url": "https://iacoma.cs.uiuc.edu/iacoma-papers/hpca25_2.pdf"
    }
  ],
  "unresolved_questions": [
    "UN：完整代码与长时私有轨迹的获取、复现误差及重复运行统计未核验。",
    "UN：两台 3090 服务器能承载的模型、并行度和分组数量需以本机证据确定。"
  ]
}
```
