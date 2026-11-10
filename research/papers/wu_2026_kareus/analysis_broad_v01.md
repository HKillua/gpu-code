# Kareus：问题、方法与证据边界

LP06 · AN-20261105-032-v01 · analyze-paper · partial

本轮始于2026-11-05，完成于2026-11-06（Asia/Shanghai）；保留本轮ID。SC=作者主张，DE=论文可定位的设计/观察，AI=分析推断，UN=待核验。DE不代表本项目复测。页码均为本地PDF查看器页序。

[本地PDF](paper.pdf) · [正式记录](https://www.usenix.org/conference/osdi26/presentation/wu-ruofan)

**问题。** 训练内核重叠可以缩短设备通电时间，降频可以降低活动功率；二者会互相影响，怎样同时降低静态与动态能量而不盲目延长迭代？

**核心方法（SC/DE）。** 把可重叠的计算/通信划分为重复分区，联合搜索通信占用SM数、通信启动时刻与GPU频率；用多目标贝叶斯搜索构建时间–能量前沿，再组合到训练迭代。必要时选择顺序执行。

**电力与服务口径。** 优化训练作业的时间–总能量前沿；dynamic/static指能量组成，不是供电上限时变。搜索中的不确定性用于性能/能量代理模型，并非预测电网功率额度。 关注迭代时间、动态/静态/总能量与给定时间或能量预算内的前沿选择；本轮所读结果不是训练达到同等验证质量的全过程比较。

**版本与已读范围。** 本地为USENIX官方OSDI 2026 PDF，含封面；正式身份复用官方presentation页核验。 本轮读PDF pp.4–15：§2.3–8中方法、实现、主要真实/模拟评估、消融与计量限制；代码、附录工件说明和原始统计未审。PDF包含封面，页码采用查看器页序。

**关键证据。**

- LP06-E01，PDF pp.4–6，§2.3–3.2，图3、表1–2：同一计算/通信分区的最佳SM分配、启动时序随频率改变；通信占SM和访存争用可以抵消重叠收益。
- LP06-E02，PDF pp.7–10，§4–5：分区、多个代理模型/搜索通道、前沿组合与频率粒度；小nanobatch会自动比较顺序执行；静态功率近似常数。
- LP06-E03，PDF pp.10–13，§6.1–6.3，表3–7：16卡真实实验与千卡级模拟分开；迭代时间/能量前沿比较，原配置和并行度有明确条件。
- LP06-E04，PDF pp.14–15，§6.4–6.7，表8、图12：消融分别去除频率或内核调度；报告profiling成本，并实测冷却与窗口长度对能量测量的影响。

**作者自述限制。**

- §4.5：静态功率近似常数，更精细的电压/漏电模型留待后续。
- §4.4：频率切换有毫秒级代价，采用microbatch级统一频率等约束以缩小空间。
- §6.6：profiling有可见成本，以长期训练摊销；§6.7强调测量窗口需适配硬件。

**分析者判断与未知。**

- AI：在线网络争用、温度或外加功率限制改变时，离线前沿的有效范围仍需界定；本文不是运行期动态外部功率预算调度器。
- AI：评估主要是迭代性能与能量；不能直接等同于完整训练达到同等质量所需能量。
- AI：千卡级收益属于模拟外推，不能当作千卡训练的真实部署证据。
- UN：工件入口已知但本轮未审实现、数据与环境；未独立复现。
- UN：不同设备/拓扑、短任务以及快速漂移场景中画像重用与摊销边界待核验。

**已有处理，不能直接当创新。** 频率与计算/通信重叠联合优化；通信SM数量与启动时机；小nanobatch顺序执行回退；profiling温度控制与测量窗口分析；动态/静态能量分别建模和目标贡献消融。

**候选问题，仅供Mode C继续核验。**

- AI：画像时形成的时间–能量前沿，在网络/温度/外部功率限制变化后哪些部分仍有效？这需要先分清设备状态漂移与模型误差，不能把联合调度本身当新意。
- AI：不同任务长度和配置更换频率下，profiling可摊销性是否改变前沿选择价值？原文已量化profile成本，后续需查其缓存/重用覆盖。

**阅读价值。** 本组最清楚的“动作彼此改变资源竞争”机制证据，也能防止把动态能量误解为动态电力预算。 借鉴同时观察时间与总能量，以及让测量窗口覆盖传感器/热状态；阅读不受原16卡限制，是否保留重叠机制另行核验。

## ANALYSIS_PACKAGE

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-032-v01",
  "created_at": "2026-11-06",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LP06"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LP06-E01 PDF pp.4–6，§2.3–3.2，图3、表1–2",
    "LP06-E02 PDF pp.7–10，§4–5",
    "LP06-E03 PDF pp.10–13，§6.1–6.3，表3–7",
    "LP06-E04 PDF pp.14–15，§6.4–6.7，表8、图12"
  ],
  "missing_evidence": [
    "工件入口已知但本轮未审实现、数据与环境；未独立复现。",
    "不同设备/拓扑、短任务以及快速漂移场景中画像重用与摊销边界待核验。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-032-v01",
  "source_identity_and_version": {
    "title": "Kareus: Joint Reduction of Dynamic and Static Energy in Large Model Training",
    "authors": [
      "Ruofan Wu",
      "Jae-Won Chung",
      "Mosharaf Chowdhury"
    ],
    "year": 2026,
    "venue": "USENIX OSDI 2026",
    "doi": null,
    "official_url": "https://www.usenix.org/conference/osdi26/presentation/wu-ruofan",
    "pdf_url": "https://www.usenix.org/system/files/osdi26-wu-ruofan.pdf",
    "publication_status": "正式发表",
    "version": "本地为USENIX官方OSDI 2026 PDF，含封面；正式身份复用官方presentation页核验。",
    "local_pdf": "research/papers/wu_2026_kareus/paper.pdf"
  },
  "research_question": "训练内核重叠可以缩短设备通电时间，降频可以降低活动功率；二者会互相影响，怎样同时降低静态与动态能量而不盲目延长迭代？",
  "study_type_and_context": {
    "design": "GPU内核级测量、模型驱动多目标搜索、真实分布式训练迭代实验与大规模仿真。",
    "power_signal_and_objective": "优化训练作业的时间–总能量前沿；dynamic/static指能量组成，不是供电上限时变。搜索中的不确定性用于性能/能量代理模型，并非预测电网功率额度。",
    "service_or_quality_contract": "关注迭代时间、动态/静态/总能量与给定时间或能量预算内的前沿选择；本轮所读结果不是训练达到同等验证质量的全过程比较。"
  },
  "design_units_samples_or_materials": {
    "DE": "§6.1：两台AWS p4d共16张A100 40GB，机内NVSwitch、跨节点400Gbps；实测Llama3.2-3B与Qwen3-1.7B。§6.3对Llama3.3-70B千卡级配置的结果来自小集群测量驱动的模拟。",
    "reading_coverage": "本轮读PDF pp.4–15：§2.3–8中方法、实现、主要真实/模拟评估、消融与计量限制；代码、附录工件说明和原始统计未审。PDF包含封面，页码采用查看器页序。"
  },
  "intervention_input_or_procedure": {
    "SC": "把可重叠的计算/通信划分为重复分区，联合搜索通信占用SM数、通信启动时刻与GPU频率；用多目标贝叶斯搜索构建时间–能量前沿，再组合到训练迭代。必要时选择顺序执行。",
    "already_addressed": [
      "频率与计算/通信重叠联合优化",
      "通信SM数量与启动时机",
      "小nanobatch顺序执行回退",
      "profiling温度控制与测量窗口分析",
      "动态/静态能量分别建模和目标贡献消融"
    ],
    "implementation_audit": "本轮读论文流程，未审代码或独立执行。"
  },
  "measurements_and_analysis": {
    "DE": "总能量拆为静态功率乘执行时间和动态部分；§5.3用Zeus/NVML对候选重复执行并安排冷却，§6.7检查测量窗口与冷却对profiling的影响。§6.2分别比较最大吞吐及同时间/同能量预算前沿点；不是任意挑选最优数。"
  },
  "results_with_uncertainty": [
    {
      "label": "DE",
      "setting": "§3.2图3：Llama3.2-3B attention分区，4张A100、TP4，改变频率及通信SM/时机",
      "observation": "降低频率后，最优通信重叠策略发生变化；单独优化一个动作可能错过联合配置。",
      "uncertainty": "单个分区用于机制说明，其他模型/拓扑须重新画像；非本项目独立验证。",
      "evidence": "LP06-E01"
    },
    {
      "label": "DE",
      "setting": "§6.2表3：Qwen3-1.7B，CP2/TP4，小microbatch配置",
      "observation": "nanobatching未必有利，系统可回到顺序执行并配合频率调节。",
      "uncertainty": "该回退已在原系统中，不能将“小批量应关闭重叠”列为新的未处理问题。",
      "evidence": "LP06-E03"
    },
    {
      "label": "DE",
      "setting": "§6.7图12：指定Llama attention/AllReduce配置重复profiling",
      "observation": "是否冷却及测量窗口会影响画像的稳定性。",
      "uncertainty": "需要逐硬件核验NVML刷新与热状态；论文参数不是3090通用参数。",
      "evidence": "LP06-E04"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "claim": "SC：把可重叠的计算/通信划分为重复分区，联合搜索通信占用SM数、通信启动时刻与GPU频率；用多目标贝叶斯搜索构建时间–能量前沿，再组合到训练迭代。必要时选择顺序执行。",
      "direct_evidence": [
        "LP06-E01",
        "LP06-E02",
        "LP06-E03",
        "LP06-E04"
      ],
      "implementation": "UN：未审代码；所读方法和评估支持限定范围内的论文主张。",
      "boundary": "优化训练作业的时间–总能量前沿；dynamic/static指能量组成，不是供电上限时变。搜索中的不确定性用于性能/能量代理模型，并非预测电网功率额度。"
    }
  ],
  "limitations_biases_and_ethics": {
    "source_stated": [
      "§4.5：静态功率近似常数，更精细的电压/漏电模型留待后续。",
      "§4.4：频率切换有毫秒级代价，采用microbatch级统一频率等约束以缩小空间。",
      "§6.6：profiling有可见成本，以长期训练摊销；§6.7强调测量窗口需适配硬件。"
    ],
    "analyst_inference": [
      "在线网络争用、温度或外加功率限制改变时，离线前沿的有效范围仍需界定；本文不是运行期动态外部功率预算调度器。",
      "评估主要是迭代性能与能量；不能直接等同于完整训练达到同等质量所需能量。",
      "千卡级收益属于模拟外推，不能当作千卡训练的真实部署证据。"
    ],
    "unknown": [
      "工件入口已知但本轮未审实现、数据与环境；未独立复现。",
      "不同设备/拓扑、短任务以及快速漂移场景中画像重用与摊销边界待核验。"
    ],
    "ethics": "本轮未独立核验生产轨迹/请求的隐私协议；未复制用户请求或运行业务数据。"
  },
  "reproducibility_and_transferability": {
    "reproducibility_grade": "unknown：未审完整工件/数据，未复现；未知不等于低。",
    "principle": "借鉴同时观察时间与总能量，以及让测量窗口覆盖传感器/热状态；阅读不受原16卡限制，是否保留重叠机制另行核验。",
    "conditions_to_recheck": [
      "在线网络争用、温度或外加功率限制改变时，离线前沿的有效范围仍需界定；本文不是运行期动态外部功率预算调度器。",
      "评估主要是迭代性能与能量；不能直接等同于完整训练达到同等质量所需能量。",
      "千卡级收益属于模拟外推，不能当作千卡训练的真实部署证据。"
    ],
    "cross_domain_transfer": "不适用：只形成来源阅读线索，未选择源方法B。"
  },
  "candidate_P": [
    {
      "label": "AI",
      "text": "画像时形成的时间–能量前沿，在网络/温度/外部功率限制变化后哪些部分仍有效？这需要先分清设备状态漂移与模型误差，不能把联合调度本身当新意。",
      "support": [
        "LP06-E01",
        "LP06-E02",
        "LP06-E03",
        "LP06-E04"
      ],
      "status": "候选，待Mode C及最近邻核验，不是新颖性结论。"
    },
    {
      "label": "AI",
      "text": "不同任务长度和配置更换频率下，profiling可摊销性是否改变前沿选择价值？原文已量化profile成本，后续需查其缓存/重用覆盖。",
      "support": [
        "LP06-E01",
        "LP06-E02",
        "LP06-E03",
        "LP06-E04"
      ],
      "status": "候选，待Mode C及最近邻核验，不是新颖性结论。"
    }
  ],
  "candidate_F": [
    {
      "label": "DE",
      "text": "降低频率后，最优通信重叠策略发生变化；单独优化一个动作可能错过联合配置。",
      "setting": "§3.2图3：Llama3.2-3B attention分区，4张A100、TP4，改变频率及通信SM/时机",
      "evidence": "LP06-E01",
      "uncertainty": "单个分区用于机制说明，其他模型/拓扑须重新画像；非本项目独立验证。"
    },
    {
      "label": "DE",
      "text": "nanobatching未必有利，系统可回到顺序执行并配合频率调节。",
      "setting": "§6.2表3：Qwen3-1.7B，CP2/TP4，小microbatch配置",
      "evidence": "LP06-E03",
      "uncertainty": "该回退已在原系统中，不能将“小批量应关闭重叠”列为新的未处理问题。"
    },
    {
      "label": "DE",
      "text": "是否冷却及测量窗口会影响画像的稳定性。",
      "setting": "§6.7图12：指定Llama attention/AllReduce配置重复profiling",
      "evidence": "LP06-E04",
      "uncertainty": "需要逐硬件核验NVML刷新与热状态；论文参数不是3090通用参数。"
    }
  ],
  "evidence_index": [
    {
      "id": "LP06-E01",
      "locator": "PDF pp.4–6，§2.3–3.2，图3、表1–2",
      "label": "DE",
      "detail": "同一计算/通信分区的最佳SM分配、启动时序随频率改变；通信占SM和访存争用可以抵消重叠收益。",
      "local_pdf": "paper.pdf",
      "url": "https://www.usenix.org/system/files/osdi26-wu-ruofan.pdf"
    },
    {
      "id": "LP06-E02",
      "locator": "PDF pp.7–10，§4–5",
      "label": "DE",
      "detail": "分区、多个代理模型/搜索通道、前沿组合与频率粒度；小nanobatch会自动比较顺序执行；静态功率近似常数。",
      "local_pdf": "paper.pdf",
      "url": "https://www.usenix.org/system/files/osdi26-wu-ruofan.pdf"
    },
    {
      "id": "LP06-E03",
      "locator": "PDF pp.10–13，§6.1–6.3，表3–7",
      "label": "DE",
      "detail": "16卡真实实验与千卡级模拟分开；迭代时间/能量前沿比较，原配置和并行度有明确条件。",
      "local_pdf": "paper.pdf",
      "url": "https://www.usenix.org/system/files/osdi26-wu-ruofan.pdf"
    },
    {
      "id": "LP06-E04",
      "locator": "PDF pp.14–15，§6.4–6.7，表8、图12",
      "label": "DE",
      "detail": "消融分别去除频率或内核调度；报告profiling成本，并实测冷却与窗口长度对能量测量的影响。",
      "local_pdf": "paper.pdf",
      "url": "https://www.usenix.org/system/files/osdi26-wu-ruofan.pdf"
    }
  ],
  "unresolved_questions": [
    "工件入口已知但本轮未审实现、数据与环境；未独立复现。",
    "不同设备/拓扑、短任务以及快速漂移场景中画像重用与摊销边界待核验。",
    "画像时形成的时间–能量前沿，在网络/温度/外部功率限制变化后哪些部分仍有效？这需要先分清设备状态漂移与模型误差，不能把联合调度本身当新意。",
    "不同任务长度和配置更换频率下，profiling可摊销性是否改变前沿选择价值？原文已量化profile成本，后续需查其缓存/重用覆盖。"
  ]
}
```
