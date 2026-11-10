# MLPerf Power: Benchmarking the Energy Efficiency of Machine Learning Systems from μWatts to MWatts for Sustainable AI｜广读正文分析

日期：2026-11-05。正文§§II–VII方法、结果及局限重点阅读，非逐条提交日志审计；未复现。

## 问题与方法

如何跨不同规模的机器学习系统，统一声明测量边界、对齐性能与功率，并公平比较能效？

§IV：时间戳对齐、按执行阶段取窗口，整系统计量；推理依规模使用外部分析仪，训练/HPC使用节点遥测并可估计交换机功率。设置测量时长与质量/报告要求，性能与功率共同换算samples/J或energy-to-train。

## 阅读边界

§IV-A：执行期为主要窗口，排除初始化、预处理等；多数场景最少60秒。§IV-C训练采用IPMI/Redfish/PDU等，交换机部分可估计。冷却与存储节点尚未完全纳入，不能把full-system泛化成完整设施能量。

作者局限：§III/IV：移动设备标准化、冷却归因以及某些交换机/设施计量仍不完备。；§V-C：超大专有模型因资源与可获得性无法直接覆盖；tokens到samples转换会产生长度误差。

分析者判断：跨版本提交非随机，汇总趋势不等于某个软件机制的因果收益。；执行期至少60秒的平均/积分不能直接作为毫秒功率峰值或动态事件响应保证。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-042-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "RP02"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "RP02-E1",
    "RP02-E2",
    "RP02-E3",
    "RP02-E4"
  ],
  "missing_evidence": [
    "本机可用整机与GPU计量手段",
    "事件恢复窗口中遗漏项是否影响结论",
    "精度、任务质量与计量口径是否对齐"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-042-v01",
  "source_identity_and_version": {
    "title": "MLPerf Power: Benchmarking the Energy Efficiency of Machine Learning Systems from μWatts to MWatts for Sustainable AI",
    "authors": [
      "Arya Tschand",
      "Arun Tejusve Raghunath Rajan",
      "Sachin Idgunji",
      "Anirban Ghosh",
      "Jeremy Holleman",
      "Csaba Kiraly",
      "Pawan Ambalkar",
      "Ritika Borkar",
      "Ramesh Chukka",
      "Trevor Cockrell",
      "Oliver Curtis",
      "Grigori Fursin",
      "Miro Hodak",
      "Hiwot Kassa",
      "Anton Lokhmotov",
      "Dejan Miskovic",
      "Yuechao Pan",
      "Manu Prasad Manmathan",
      "Liz Raymond",
      "Tom St. John",
      "Arjun Suresh",
      "Rowan Taubitz",
      "Sean Zhan",
      "Scott Wasson",
      "David Kanter",
      "Vijay Janapa Reddi"
    ],
    "venue": "IEEE HPCA 2025",
    "year": 2025,
    "official_url": "https://ieeexplore.ieee.org/document/10946778/",
    "actual_local_version": "论文已发表于 HPCA 2025；本地为作者公开 arXiv:2410.12032v2（2025-02-06），首页标 HPCA 2025。未逐字比对出版社终版。",
    "local_pdf": "paper_arxiv_v2_hpca2025.pdf",
    "reading_coverage": "正文§§II–VII方法、结果及局限重点阅读，非逐条提交日志审计；未复现。"
  },
  "research_question": "如何跨不同规模的机器学习系统，统一声明测量边界、对齐性能与功率，并公平比较能效？",
  "study_type_and_context": "能效基准方法与行业提交数据的观测分析；本地是arXiv v2带HPCA2025标识，不是独立新调度算法。",
  "design_units_samples_or_materials": "§V：1841个提交结果，含590数据中心、792边缘、447微型推理及12训练提交。不同平台和多代提交构成观察材料，不是随机对照试验；Fig11b硬件示例脚注明确未由MLPerf验证。",
  "intervention_input_or_procedure": "§IV：时间戳对齐、按执行阶段取窗口，整系统计量；推理依规模使用外部分析仪，训练/HPC使用节点遥测并可估计交换机功率。设置测量时长与质量/报告要求，性能与功率共同换算samples/J或energy-to-train。",
  "measurements_and_analysis": "§IV-A：执行期为主要窗口，排除初始化、预处理等；多数场景最少60秒。§IV-C训练采用IPMI/Redfish/PDU等，交换机部分可估计。冷却与存储节点尚未完全纳入，不能把full-system泛化成完整设施能量。",
  "results_with_uncertainty": [
    {
      "mark": "DE",
      "finding": "§V-B/Fig6：Llama2-70B LoRA案例中，增加加速器缩短时间但增加到达目标的能量；只说明该规模比较，不证明所有任务如此。",
      "location": "PDF p.9，Fig6",
      "uncertainty": "提交配置比较，未独立审计硬件/并行差异或重复区间。"
    },
    {
      "mark": "DE",
      "finding": "§V-F/Fig10比较相同硬件连续版本，观察软件侧能效变化；软件配置也变化，不能推成单项优化的因果贡献。",
      "location": "PDF pp.11–12，Fig10、Fig11脚注",
      "uncertainty": "行业提交的选择偏差、软硬件变化与归一化边界仍需逐项对照。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "SC": "来源提出上述测量协议与设置内能效比较。",
      "DE": "方法、结果与局限可按evidence_index定位；非本项目独立验证。",
      "AI": "跨论文连接需要重新核验窗口、边界与信息权限。",
      "UN": "代码实现一致性与本机效应。"
    }
  ],
  "limitations_biases_and_ethics": {
    "author_stated": [
      "§III/IV：移动设备标准化、冷却归因以及某些交换机/设施计量仍不完备。",
      "§V-C：超大专有模型因资源与可获得性无法直接覆盖；tokens到samples转换会产生长度误差。"
    ],
    "analyst_inferred": [
      "跨版本提交非随机，汇总趋势不等于某个软件机制的因果收益。",
      "执行期至少60秒的平均/积分不能直接作为毫秒功率峰值或动态事件响应保证。"
    ],
    "ethics": "本轮未独立核查所有数据许可/隐私处理，未使用用户生产数据。"
  },
  "reproducibility_and_transferability": {
    "artifact_status": "阅读公开正文，未运行代码/重新产生结果；材料存在不等于可复现已证实。",
    "scope": "读论文不受六卡限制；落地时重新核验关键机制与测量。"
  },
  "candidate_P": [
    {
      "id": "RP02-P1",
      "mark": "AI",
      "text": "同一调度策略在GPU边界、整机边界与完整事件窗口的排序，是否会因状态搬运及空闲开销而改变？",
      "evidence": [
        "RP02-E1",
        "RP02-E2"
      ]
    }
  ],
  "candidate_F": [
    {
      "id": "RP02-F1",
      "mark": "DE",
      "text": "§IV分别采用执行窗口和不同计量手段，且明确未完全计入冷却/存储节点；全系统与GPU口径不同。",
      "evidence": [
        "RP02-E1",
        "RP02-E2",
        "RP02-E4"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "RP02-E1",
      "location": "§III-C、§IV-A，PDF pp.5–6：误区、窗口和边界",
      "local_pdf": "paper_arxiv_v2_hpca2025.pdf"
    },
    {
      "id": "RP02-E2",
      "location": "§IV-B–D，PDF pp.6–8：分析仪、遥测、估计及报告",
      "local_pdf": "paper_arxiv_v2_hpca2025.pdf"
    },
    {
      "id": "RP02-E3",
      "location": "§V-B/C/F，PDF pp.9–12：规模、长度换算和非随机提交结果",
      "local_pdf": "paper_arxiv_v2_hpca2025.pdf"
    },
    {
      "id": "RP02-E4",
      "location": "§III、§V-C、§VI，PDF pp.4/10/12–13：覆盖与未来方向",
      "local_pdf": "paper_arxiv_v2_hpca2025.pdf"
    }
  ],
  "unresolved_questions": [
    "本机可用整机与GPU计量手段",
    "事件恢复窗口中遗漏项是否影响结论",
    "精度、任务质量与计量口径是否对齐"
  ]
}
```
