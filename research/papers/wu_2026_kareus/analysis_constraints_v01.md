# Kareus约束与画像复用增量分析 · 2026-11-10

复用AN032并增加E05–E09。关键修正是：动态适应、功率限制与多频率前沿已有覆盖。当前只保留画像适用条件的诊断问题，不声称创新。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261110-001-v01",
  "created_at": "2026-11-10",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LP06"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LP06-E01 PDF pp.4–6，§2.3–3.2，图3、表1–2",
    "LP06-E02 PDF pp.7–10，§4–5",
    "LP06-E03 PDF pp.10–13，§6.1–6.3，表3–7",
    "LP06-E04 PDF pp.14–15，§6.4–6.7，表8、图12",
    "LP06-E05 会议PDF p7 §4.1；p10 §5.1 Fig8",
    "LP06-E06 会议PDF p19 Appendix A Theorem1及Implications",
    "LP06-E07 会议PDF p10 §5.2–5.3；p14–15 §6.6–6.7",
    "LP06-E08 会议PDF p20 Appendix C；p8–9 §4.4 Alg2",
    "LP06-E09 作者osdi26ae分支：artifact/toy README、GPU_CONFIGS、run_kareus.sh；读取2026-11-10"
  ],
  "missing_evidence": [
    "3090功率/锁频权限、传感器有效刷新、拓扑及空闲设备",
    "原工件对power cap状态的缓存、重索引、重画像触发完整实现",
    "已有画像在cap生效后的误差和有限候选选择损失",
    "全领域最近邻尚未穷尽；无独立复验"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261110-001-v01",
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
    "local_pdf": "research/papers/wu_2026_kareus/paper.pdf",
    "increment_scope": "本轮直接读本地会议PDF第7–10、14–15、19–20页；旧包结果复用。11-09学习报告不是新增原始实验。 另定点核作者osdi26ae工件说明/配置/启动脚本，见E09；不是完整源码审计。"
  },
  "research_question": "训练内核重叠可以缩短设备通电时间，降频可以降低活动功率；二者会互相影响，怎样同时降低静态与动态能量而不盲目延长迭代？",
  "study_type_and_context": {
    "design": "GPU内核级测量、模型驱动多目标搜索、真实分布式训练迭代实验与大规模仿真。",
    "power_signal_and_objective": "优化训练作业的时间–总能量前沿；dynamic/static指能量组成，不是供电上限时变。搜索中的不确定性用于性能/能量代理模型，并非预测电网功率额度。",
    "service_or_quality_contract": "关注迭代时间、动态/静态/总能量与给定时间或能量预算内的前沿选择；本轮所读结果不是训练达到同等验证质量的全过程比较。"
  },
  "design_units_samples_or_materials": {
    "DE": "§6.1：两台AWS p4d共16张A100 40GB，机内NVSwitch、跨节点400Gbps；实测Llama3.2-3B与Qwen3-1.7B。§6.3对Llama3.3-70B千卡级配置的结果来自小集群测量驱动的模拟。",
    "reading_coverage": "旧包PDF pp.4–15定点阅读复用；本轮新增PDF pp.7–10、14–15、19–20的条件定位及E09工件入口核查。核心源码与原始统计未审，未执行。页码采用含封面的查看器页序。"
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
    "implementation_audit": "论文流程及E09列出的工件说明/配置/启动脚本已定点核查；核心调度器和频率调用链未审，未独立执行。"
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
    },
    {
      "claim": "不能将已有前沿视为只能用一次的静态计划",
      "direct_evidence": [
        "LP06-E05",
        "LP06-E08"
      ],
      "implementation": "本轮正文核验；公开工件检索另见近邻记录，未全面审计源码/运行",
      "boundary": "动态选点不自动证明任意新功率约束下画像均准确，也不证明必然失效"
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
      "id": "LP06-P1110",
      "label": "AI",
      "text": "硬件功率上限实际触发限频时，按固定频率测得的画像在依据实际频率重索引后，是否仍足以正确选择通信启动时序？",
      "evidence": [
        "LP06-E05",
        "LP06-E06",
        "LP06-E08"
      ],
      "status": "待诊断；不存在已证实的新失败"
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
    },
    {
      "id": "LP06-F1110",
      "label": "DE",
      "text": "源文已有动态环境选点、功率限制造成频率波动的讨论及同平均频率同耗时假设。",
      "evidence": [
        "LP06-E05",
        "LP06-E06"
      ]
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
    },
    {
      "id": "LP06-E05",
      "label": "DE",
      "locator": "会议PDF p7 §4.1；p10 §5.1 Fig8",
      "detail": "前沿用于任务时限/能量要求和changing environments（如stragglers）；运行时按目标迭代时延从已有前沿选择计划。不能说Kareus不支持运行时适应。",
      "local_pdf": "paper.pdf",
      "url": "https://www.usenix.org/system/files/osdi26-wu-ruofan.pdf"
    },
    {
      "id": "LP06-E06",
      "label": "DE",
      "locator": "会议PDF p19 Appendix A Theorem1及Implications",
      "detail": "恒频节能定理条件含动态功率三次方、静态功率常数、相同平均频率完成时间相同；随后明确讨论power limits导致频率波动。定理未证明这些前提对所有通信/计算混合执行成立。",
      "local_pdf": "paper.pdf",
      "url": "https://www.usenix.org/system/files/osdi26-wu-ruofan.pdf"
    },
    {
      "id": "LP06-E07",
      "label": "DE",
      "locator": "会议PDF p10 §5.2–5.3；p14–15 §6.6–6.7",
      "detail": "MSCCL++ grid控制通信SM、分CUDA streams和events；原环境单候选约13秒含预热/测量/冷却，MBO平均2小时/32GPU小时，画像占97%；冷却参数依赖环境。",
      "local_pdf": "paper.pdf",
      "url": "https://www.usenix.org/system/files/osdi26-wu-ruofan.pdf"
    },
    {
      "id": "LP06-E08",
      "label": "DE",
      "locator": "会议PDF p20 Appendix C；p8–9 §4.4 Alg2",
      "detail": "搜索覆盖多个频率，按通信组大小选SM网格，构建microbatch前沿；微批内部同频以避免切换开销。同频率/时序的旧画像重索引应作为对照，不应只比较默认满频计划。",
      "local_pdf": "paper.pdf",
      "url": "https://www.usenix.org/system/files/osdi26-wu-ruofan.pdf"
    },
    {
      "id": "LP06-E09",
      "label": "DE（源码/说明定点）；兼容性未实测",
      "locator": "作者osdi26ae分支 tests/artifact/README.md、tests/toy/README.md、tests/bayesian/common/model_config.py、tests/toy/run_kareus.sh；读取2026-11-10",
      "detail": "主工件两机16×A100；toy单机4×A40 PP2/TP2；GPU_CONFIGS仅列A40/A100。toy脚本按已有输出复用BO/CSV/优化结果，不足以证明整个运行时缺少在线适应。未固定本地源码快照、未审完整调用链、未运行。",
      "urls": [
        "https://github.com/ml-energy/kareus/blob/osdi26ae/tests/artifact/README.md",
        "https://github.com/ml-energy/kareus/blob/osdi26ae/tests/toy/README.md",
        "https://github.com/ml-energy/kareus/blob/osdi26ae/tests/bayesian/common/model_config.py",
        "https://github.com/ml-energy/kareus/blob/osdi26ae/tests/toy/run_kareus.sh"
      ],
      "audit_record": "../../surveys/gpu_power_scheduling_2026/exploration_2026-11-10/nearest_neighbor_check.md"
    }
  ],
  "unresolved_questions": [
    "3090功率/锁频权限、传感器有效刷新、拓扑及空闲设备",
    "原工件对power cap状态的缓存、重索引、重画像触发完整实现",
    "已有画像在cap生效后的误差和有限候选选择损失",
    "全领域最近邻尚未穷尽；无独立复验"
  ],
  "supersedes": "none；在AN-20261105-032-v01上增加有限范围证据，旧包保留"
}
```
