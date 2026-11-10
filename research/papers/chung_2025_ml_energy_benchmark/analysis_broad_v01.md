# The ML.ENERGY Benchmark: Toward Automated Inference Energy Measurement and Optimization｜广读正文分析

日期：2026-11-05。正文§§2–6、Appendix B/C/E重点阅读；不含代码审计与独立复现。

## 问题与方法

怎样以真实生成服务的粒度测量能耗，并按应用延迟要求推荐部署配置？

§§2–3：选择模型/任务数据与GPU/并行/batch配置，分别测时间和GPU能耗，构建时间—能量Pareto前沿，再按延迟要求选点。§3.2按稳定期每token能量乘输出长度归账，不是每条请求的独立物理测量。

## 阅读边界

GPU软件能耗由Zeus采集。单位主要是J/完整生成；平均TPOT或完整生成时延随应用选取。GPU边界与整机/网络/机房边界分开，平均TPOT不等于请求尾时延保证。

作者局限：Appendix E：任务、模型、数据集和硬件不穷尽，更新成本高，主要覆盖旗舰NVIDIA设备。

分析者判断：稳定饱和区间不覆盖全部启动、低负载、排空与恢复；不能直接拿稳态每请求J评价动态降载事件。；能量归账依赖共同batch与输出长度，不能认作单请求独立因果能耗；跨模型比较需考虑任务质量/输出内容。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-041-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LP07"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LP07-E1",
    "LP07-E2",
    "LP07-E3",
    "LP07-E4"
  ],
  "missing_evidence": [
    "真实事件时间尺度下稳态与完整窗口排名差异",
    "各GPU遥测误差与整机额外开销",
    "同质量业务/有限请求流的比较条件"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-041-v01",
  "source_identity_and_version": {
    "title": "The ML.ENERGY Benchmark: Toward Automated Inference Energy Measurement and Optimization",
    "authors": [
      "Jae-Won Chung",
      "Jeff J. Ma",
      "Ruofan Wu",
      "Jiachen Liu",
      "Oh Jun Kweon",
      "Yuxuan Xia",
      "Zhiyu Wu",
      "Mosharaf Chowdhury"
    ],
    "venue": "NeurIPS 2025, Datasets and Benchmarks Track",
    "year": 2025,
    "official_url": "https://proceedings.neurips.cc/paper_files/paper/2025/hash/9dc510e3d7b0b3b2a58ffed7a3ad6b0f-Abstract-Datasets_and_Benchmarks_Track.html",
    "actual_local_version": "NeurIPS2025 D&B正式公开PDF",
    "local_pdf": "paper.pdf",
    "reading_coverage": "正文§§2–6、Appendix B/C/E重点阅读；不含代码审计与独立复现。"
  },
  "research_question": "怎样以真实生成服务的粒度测量能耗，并按应用延迟要求推荐部署配置？",
  "study_type_and_context": "生成式AI能耗基准、配置扫描与结果分析；不是时变供电控制器。",
  "design_units_samples_or_materials": "§4：40种模型架构、6种任务，AWS上的A100 40GB/H100 80GB，vLLM与Diffusers。LLM请求集整体提交，§3.2在batch达到配置上限的稳定区间计量；Appendix B另查抢占、TP、PD及chunked prefill。",
  "intervention_input_or_procedure": "§§2–3：选择模型/任务数据与GPU/并行/batch配置，分别测时间和GPU能耗，构建时间—能量Pareto前沿，再按延迟要求选点。§3.2按稳定期每token能量乘输出长度归账，不是每条请求的独立物理测量。",
  "measurements_and_analysis": "GPU软件能耗由Zeus采集。单位主要是J/完整生成；平均TPOT或完整生成时延随应用选取。GPU边界与整机/网络/机房边界分开，平均TPOT不等于请求尾时延保证。",
  "results_with_uncertainty": [
    {
      "mark": "DE",
      "finding": "Fig4：特定Phi-3 Mini/Small对比中，较大模型可因GQA降低KV占用、容纳更大batch而降低每请求能量；不是参数量决定能耗的普遍单调规律。",
      "location": "§4.2，PDF p.8，Fig4",
      "uncertainty": "作者设置内曲线；未独立复现，也未审计重复运行区间。"
    },
    {
      "mark": "DE",
      "finding": "Appendix B.1/Fig8已比较过载下重计算与换出，B.2/Fig9已测TP从1到8卡的通信能量变化，B.3/Table4已测PD配置。",
      "location": "PDF pp.23–25",
      "uncertainty": "原模型、负载、硬件设置内结果，不可把抢占能量或通信能量当作无人研究。"
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
      "Appendix E：任务、模型、数据集和硬件不穷尽，更新成本高，主要覆盖旗舰NVIDIA设备。"
    ],
    "analyst_inferred": [
      "稳定饱和区间不覆盖全部启动、低负载、排空与恢复；不能直接拿稳态每请求J评价动态降载事件。",
      "能量归账依赖共同batch与输出长度，不能认作单请求独立因果能耗；跨模型比较需考虑任务质量/输出内容。"
    ],
    "ethics": "本轮未独立核查所有数据许可/隐私处理，未使用用户生产数据。"
  },
  "reproducibility_and_transferability": {
    "artifact_status": "阅读公开正文，未运行代码/重新产生结果；材料存在不等于可复现已证实。",
    "scope": "读论文不受六卡限制；落地时重新核验关键机制与测量。"
  },
  "candidate_P": [
    {
      "id": "LP07-P1",
      "mark": "AI",
      "text": "稳定区间配置排名，在包含启动、排空、恢复与间歇到达的完整窗口中是否仍成立？",
      "evidence": [
        "LP07-E1"
      ]
    }
  ],
  "candidate_F": [
    {
      "id": "LP07-F1",
      "mark": "DE",
      "text": "§3.2显式选择batch饱和稳定区间；Appendix B已直接研究抢占与TP通信能耗。",
      "evidence": [
        "LP07-E1",
        "LP07-E3"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "LP07-E1",
      "location": "§§2.1–2.4、§3.1–3.3，PDF pp.2–5：边界、稳定期及配置前沿",
      "local_pdf": "paper.pdf"
    },
    {
      "id": "LP07-E2",
      "location": "§4.1–4.3，PDF pp.6–10，Fig3–7：设备、结果与解释",
      "local_pdf": "paper.pdf"
    },
    {
      "id": "LP07-E3",
      "location": "Appendix B.1–B.4，PDF pp.23–25，Fig8–9、Table4–5：抢占/通信/PD/chunk",
      "local_pdf": "paper.pdf"
    },
    {
      "id": "LP07-E4",
      "location": "Appendix E，PDF p.28：作者局限",
      "local_pdf": "paper.pdf"
    }
  ],
  "unresolved_questions": [
    "真实事件时间尺度下稳态与完整窗口排名差异",
    "各GPU遥测误差与整机额外开销",
    "同质量业务/有限请求流的比较条件"
  ]
}
```
