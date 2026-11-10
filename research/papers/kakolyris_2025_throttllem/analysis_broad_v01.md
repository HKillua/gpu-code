# throttLL’eM：问题、方法与证据边界

RP01 · AN-20261105-033-v01 · analyze-paper · partial

本轮始于2026-11-05，完成于2026-11-06（Asia/Shanghai）；保留本轮ID。SC=作者主张，DE=论文可定位的设计/观察，AI=分析推断，UN=待核验。DE不代表本项目复测。页码均为本地PDF查看器页序。

[本地PDF](paper_hpca2025.pdf) · [正式记录](https://ieeexplore.ieee.org/document/10946751/)

**问题。** LLM请求输出长度不同，批量与KV缓存随token生成而变化；怎样利用未来执行状态的预测降低GPU能量，同时控制响应时间？

**核心方法（SC/DE）。** 维护正在执行请求的scoreboard，按预测输出长度展开未来各迭代的批量与KV缓存；性能模型预测每次迭代速率，检查准入与SLO，搜索可用最低频率，并按负载调整TP实例。切换用shadow instance和旧实例排空。

**电力与服务口径。** 需求侧状态随请求改变，功率由频率与实例规模调节；所读方法没有把外部时变总功率额度作为约束输入。不能将预测未来KV/批量称作预测供电。 §V-A：平均TBT阈值与按模型高负载画像设定的p99端到端目标；§IV-F明确不优化TTFT。准入检查可将已经无法满足自身期限但不影响他人的请求标lost，继续best-effort处理。

**版本与已读范围。** 本地为项目站公开的IEEE HPCA 2025排版PDF，首页有完整DOI；正式身份复用IEEE条目核验。 本轮读PDF pp.6–13：§IV完整方法/局限与§V主要实验、敏感性、失效时间线和补充设备；摘要与结论已核对。代码、全部附录/引用与原始日志未审。

**关键证据。**

- RP01-E01，PDF pp.6–8，§IV-A–E：scoreboard投影、GBDT性能模型、KV容量/服务检查、lost请求、统一调频和shadow实例缩放；投影当前批次时假定没有未来新请求。
- RP01-E02，PDF pp.8–9，§IV-F：明确不优化TTFT；输出长度误差的保守处理；TP依赖节点内快链路，跨节点PP需额外编排。
- RP01-E03，PDF pp.9–11，§V-A–D，图7–10：实验硬件/模型、平均TBT与p99端到端阈值，oracle和合成长度误差；固定TP和动态TP的比较条件分开。
- RP01-E04，PDF pp.11–13，§V-E–F，图11–13：突发负载后启动延迟引起短时违约，即便全轨迹p99符合；边界负载下反复切换实例，另分析调频引起批量积累的反馈。

**作者自述限制。**

- §IV-F：不针对TTFT优化，频率调节与prefill的时间尺度存在冲突。
- §V-E：突发后的临时违约与实例启动时间有关；除非缩短启动时间，否则不能完全避免。
- §IV-F：TP扩展依赖高速节点内通信，跨节点需要额外PP编排。

**分析者判断与未知。**

- AI：救急升最高频和创建shadow实例都要求额外资源/功率可用；在外部供电额度突然下降时动作可行性不能沿用。
- AI：平均TBT、全轨迹端到端p99、短窗口违约和lost请求比例是不同合同，不能合并成“所有请求均满足SLO”。
- AI：长度oracle/合成高斯误差不等同于真实预测误差分布；投影未包含未来新请求。
- UN：未审代码、所有错误分布及完整轨迹；未独立复现。
- UN：真实输出长度预测器、lost请求总体比例与重试行为的端到端影响待核验。

**已有处理，不能直接当创新。** KV/批量未来状态预测与缓存容量检查；输出长度不确定性的保守处理；频率救急与lost请求best-effort；shadow实例预热、旧实例排空和冷却期；突发启动滞后与阈值附近反复切换的观察；频率改变批量的闭环反馈。

**候选问题，仅供Mode C继续核验。**

- AI：当全局分位数达标但短窗口发生违约时，哪种服务统计能保留事件造成的损失和恢复尾巴？原文已有这一现象，候选问题必须进一步收窄。
- AI：当外部功率额度收紧使max-frequency或shadow实例不可行时，原准入/服务可行性判断在哪一步失效？目前只是从动作前提导出的候选问题。

**阅读价值。** 最值得和TAPAS对读：清楚看到预测、准入、重配置与SLO统计怎样互相牵制。 借鉴记录事件时间线、区分平均/尾部/单请求指标及真实预测与oracle；不同GPU数不影响先理解这些机制。

## ANALYSIS_PACKAGE

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-033-v01",
  "created_at": "2026-11-06",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "RP01"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "RP01-E01 PDF pp.6–8，§IV-A–E",
    "RP01-E02 PDF pp.8–9，§IV-F",
    "RP01-E03 PDF pp.9–11，§V-A–D，图7–10",
    "RP01-E04 PDF pp.11–13，§V-E–F，图11–13"
  ],
  "missing_evidence": [
    "未审代码、所有错误分布及完整轨迹；未独立复现。",
    "真实输出长度预测器、lost请求总体比例与重试行为的端到端影响待核验。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-033-v01",
  "source_identity_and_version": {
    "title": "throttLL’eM: Predictive GPU Throttling for Energy Efficient LLM Inference Serving",
    "authors": [
      "Andreas Kosmas Kakolyris",
      "Dimosthenis Masouros",
      "Petros Vavaroutsos",
      "Sotirios Xydis",
      "Dimitrios Soudris"
    ],
    "year": 2025,
    "venue": "IEEE HPCA 2025",
    "doi": "10.1109/HPCA61900.2025.00103",
    "official_url": "https://ieeexplore.ieee.org/document/10946751/",
    "pdf_url": "https://www.privateer-project.eu/wp-content/uploads/2026/03/throttLLeM_Predictive_GPU_Throttling_for_Energy_Efficient_LLM_Inference_Serving.pdf",
    "publication_status": "正式发表",
    "version": "本地为项目站公开的IEEE HPCA 2025排版PDF，首页有完整DOI；正式身份复用IEEE条目核验。",
    "local_pdf": "research/papers/kakolyris_2025_throttllem/paper_hpca2025.pdf"
  },
  "research_question": "LLM请求输出长度不同，批量与KV缓存随token生成而变化；怎样利用未来执行状态的预测降低GPU能量，同时控制响应时间？",
  "study_type_and_context": {
    "design": "在线推理控制器、状态投影和性能模型，硬件轨迹回放及长度预测误差敏感性实验。",
    "power_signal_and_objective": "需求侧状态随请求改变，功率由频率与实例规模调节；所读方法没有把外部时变总功率额度作为约束输入。不能将预测未来KV/批量称作预测供电。",
    "service_or_quality_contract": "§V-A：平均TBT阈值与按模型高负载画像设定的p99端到端目标；§IV-F明确不优化TTFT。准入检查可将已经无法满足自身期限但不影响他人的请求标lost，继续best-effort处理。"
  },
  "design_units_samples_or_materials": {
    "DE": "§V-A：AWS单节点8张A100 40GB，Triton/TensorRT-LLM；Llama3-8B、Llama2-13B、Llama3-70B以及不同TP。§V-F另在8张A10G上验证Mixtral。生成请求匹配Azure输入/输出长度，负载有缩放。",
    "reading_coverage": "本轮读PDF pp.6–13：§IV完整方法/局限与§V主要实验、敏感性、失效时间线和补充设备；摘要与结论已核对。代码、全部附录/引用与原始日志未审。"
  },
  "intervention_input_or_procedure": {
    "SC": "维护正在执行请求的scoreboard，按预测输出长度展开未来各迭代的批量与KV缓存；性能模型预测每次迭代速率，检查准入与SLO，搜索可用最低频率，并按负载调整TP实例。切换用shadow instance和旧实例排空。",
    "already_addressed": [
      "KV/批量未来状态预测与缓存容量检查",
      "输出长度不确定性的保守处理",
      "频率救急与lost请求best-effort",
      "shadow实例预热、旧实例排空和冷却期",
      "突发启动滞后与阈值附近反复切换的观察",
      "频率改变批量的闭环反馈"
    ],
    "implementation_audit": "本轮读论文流程，未审代码或独立执行。"
  },
  "measurements_and_analysis": {
    "DE": "分别记录能量、功率、平均TBT、端到端p99和部分TTFT。长度预测评估采用oracle或人为注入的高斯误差；不能当作已经部署并独立评估了真实长度预测器。§V-E使用时间线揭示完整回放分位数遗漏的短时违约。"
  },
  "results_with_uncertainty": [
    {
      "label": "DE",
      "setting": "§V-E图11，Llama2-13B动态TP轨迹",
      "observation": "全轨迹p99可以达标，而突发增长后等待新实例启动的窗口出现违约；作者同时观察到阈值附近连续实例切换。",
      "uncertainty": "是文中回放现象，不能由此量化实际供电事件的服务损失；本轮无独立原始日志。",
      "evidence": "RP01-E04"
    },
    {
      "label": "DE",
      "setting": "§V-D–F，误差、延迟和能量分析",
      "observation": "调低频率也会让请求积累成更大批次，改变后续迭代数；能量变化不只是同一执行过程的功率缩放。",
      "uncertainty": "取决于请求长度、KV容量和到达过程；不保证所有负载得到相同效果。",
      "evidence": "RP01-E03，RP01-E04"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "claim": "SC：维护正在执行请求的scoreboard，按预测输出长度展开未来各迭代的批量与KV缓存；性能模型预测每次迭代速率，检查准入与SLO，搜索可用最低频率，并按负载调整TP实例。切换用shadow instance和旧实例排空。",
      "direct_evidence": [
        "RP01-E01",
        "RP01-E02",
        "RP01-E03",
        "RP01-E04"
      ],
      "implementation": "UN：未审代码；所读方法和评估支持限定范围内的论文主张。",
      "boundary": "需求侧状态随请求改变，功率由频率与实例规模调节；所读方法没有把外部时变总功率额度作为约束输入。不能将预测未来KV/批量称作预测供电。"
    }
  ],
  "limitations_biases_and_ethics": {
    "source_stated": [
      "§IV-F：不针对TTFT优化，频率调节与prefill的时间尺度存在冲突。",
      "§V-E：突发后的临时违约与实例启动时间有关；除非缩短启动时间，否则不能完全避免。",
      "§IV-F：TP扩展依赖高速节点内通信，跨节点需要额外PP编排。"
    ],
    "analyst_inference": [
      "救急升最高频和创建shadow实例都要求额外资源/功率可用；在外部供电额度突然下降时动作可行性不能沿用。",
      "平均TBT、全轨迹端到端p99、短窗口违约和lost请求比例是不同合同，不能合并成“所有请求均满足SLO”。",
      "长度oracle/合成高斯误差不等同于真实预测误差分布；投影未包含未来新请求。"
    ],
    "unknown": [
      "未审代码、所有错误分布及完整轨迹；未独立复现。",
      "真实输出长度预测器、lost请求总体比例与重试行为的端到端影响待核验。"
    ],
    "ethics": "本轮未独立核验生产轨迹/请求的隐私协议；未复制用户请求或运行业务数据。"
  },
  "reproducibility_and_transferability": {
    "reproducibility_grade": "unknown：未审完整工件/数据，未复现；未知不等于低。",
    "principle": "借鉴记录事件时间线、区分平均/尾部/单请求指标及真实预测与oracle；不同GPU数不影响先理解这些机制。",
    "conditions_to_recheck": [
      "救急升最高频和创建shadow实例都要求额外资源/功率可用；在外部供电额度突然下降时动作可行性不能沿用。",
      "平均TBT、全轨迹端到端p99、短窗口违约和lost请求比例是不同合同，不能合并成“所有请求均满足SLO”。",
      "长度oracle/合成高斯误差不等同于真实预测误差分布；投影未包含未来新请求。"
    ],
    "cross_domain_transfer": "不适用：只形成来源阅读线索，未选择源方法B。"
  },
  "candidate_P": [
    {
      "label": "AI",
      "text": "当全局分位数达标但短窗口发生违约时，哪种服务统计能保留事件造成的损失和恢复尾巴？原文已有这一现象，候选问题必须进一步收窄。",
      "support": [
        "RP01-E01",
        "RP01-E02",
        "RP01-E03",
        "RP01-E04"
      ],
      "status": "候选，待Mode C及最近邻核验，不是新颖性结论。"
    },
    {
      "label": "AI",
      "text": "当外部功率额度收紧使max-frequency或shadow实例不可行时，原准入/服务可行性判断在哪一步失效？目前只是从动作前提导出的候选问题。",
      "support": [
        "RP01-E01",
        "RP01-E02",
        "RP01-E03",
        "RP01-E04"
      ],
      "status": "候选，待Mode C及最近邻核验，不是新颖性结论。"
    }
  ],
  "candidate_F": [
    {
      "label": "DE",
      "text": "全轨迹p99可以达标，而突发增长后等待新实例启动的窗口出现违约；作者同时观察到阈值附近连续实例切换。",
      "setting": "§V-E图11，Llama2-13B动态TP轨迹",
      "evidence": "RP01-E04",
      "uncertainty": "是文中回放现象，不能由此量化实际供电事件的服务损失；本轮无独立原始日志。"
    },
    {
      "label": "DE",
      "text": "调低频率也会让请求积累成更大批次，改变后续迭代数；能量变化不只是同一执行过程的功率缩放。",
      "setting": "§V-D–F，误差、延迟和能量分析",
      "evidence": "RP01-E03，RP01-E04",
      "uncertainty": "取决于请求长度、KV容量和到达过程；不保证所有负载得到相同效果。"
    }
  ],
  "evidence_index": [
    {
      "id": "RP01-E01",
      "locator": "PDF pp.6–8，§IV-A–E",
      "label": "DE",
      "detail": "scoreboard投影、GBDT性能模型、KV容量/服务检查、lost请求、统一调频和shadow实例缩放；投影当前批次时假定没有未来新请求。",
      "local_pdf": "paper_hpca2025.pdf",
      "url": "https://www.privateer-project.eu/wp-content/uploads/2026/03/throttLLeM_Predictive_GPU_Throttling_for_Energy_Efficient_LLM_Inference_Serving.pdf"
    },
    {
      "id": "RP01-E02",
      "locator": "PDF pp.8–9，§IV-F",
      "label": "DE",
      "detail": "明确不优化TTFT；输出长度误差的保守处理；TP依赖节点内快链路，跨节点PP需额外编排。",
      "local_pdf": "paper_hpca2025.pdf",
      "url": "https://www.privateer-project.eu/wp-content/uploads/2026/03/throttLLeM_Predictive_GPU_Throttling_for_Energy_Efficient_LLM_Inference_Serving.pdf"
    },
    {
      "id": "RP01-E03",
      "locator": "PDF pp.9–11，§V-A–D，图7–10",
      "label": "DE",
      "detail": "实验硬件/模型、平均TBT与p99端到端阈值，oracle和合成长度误差；固定TP和动态TP的比较条件分开。",
      "local_pdf": "paper_hpca2025.pdf",
      "url": "https://www.privateer-project.eu/wp-content/uploads/2026/03/throttLLeM_Predictive_GPU_Throttling_for_Energy_Efficient_LLM_Inference_Serving.pdf"
    },
    {
      "id": "RP01-E04",
      "locator": "PDF pp.11–13，§V-E–F，图11–13",
      "label": "DE",
      "detail": "突发负载后启动延迟引起短时违约，即便全轨迹p99符合；边界负载下反复切换实例，另分析调频引起批量积累的反馈。",
      "local_pdf": "paper_hpca2025.pdf",
      "url": "https://www.privateer-project.eu/wp-content/uploads/2026/03/throttLLeM_Predictive_GPU_Throttling_for_Energy_Efficient_LLM_Inference_Serving.pdf"
    }
  ],
  "unresolved_questions": [
    "未审代码、所有错误分布及完整轨迹；未独立复现。",
    "真实输出长度预测器、lost请求总体比例与重试行为的端到端影响待核验。",
    "当全局分位数达标但短窗口发生违约时，哪种服务统计能保留事件造成的损失和恢复尾巴？原文已有这一现象，候选问题必须进一步收窄。",
    "当外部功率额度收紧使max-frequency或shadow实例不可行时，原准入/服务可行性判断在哪一步失效？目前只是从动作前提导出的候选问题。"
  ]
}
```
