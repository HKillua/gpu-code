# Alibaba ASI / SpotGPU｜正文广读分析包

AN-20261105-044-v01 · LS08 · 2026-11-05 批次；2026-11-06 完成 · `partial`

阶段：`analyze-paper`；上游为已归档的正式PDF和来源初筛记录。下游：`deep-read Mode C`。本次没有新算法、baseline选择、选题决定或实验；台账由主汇总统一维护。

Heterogeneity at Hyperscale: Characterization and Scheduling of Large Production AI Clusters at Alibaba (Operational Systems)

USENIX OSDI 2026 正式会议 PDF，Operational Systems Paper；PDF含封面，正文印刷页2187–2203。 [官方来源](https://www.usenix.org/conference/osdi26/presentation/li-suyi) · [本地全文](paper.pdf)

## 问题与方法

大型生产AI集群里，预留容量、GPU类型偏好、CPU/GPU配比与网络局部性使“有空卡”和“任务能启动”并不等价。怎样使用低优先级任务消化可暂借容量，同时控制抢占损失和高优先级服务影响？

SC（来源陈述）：ASI结合真实任务/设备画像、In-place Consolidation与SpotGPU。IPC用局部分组和递归迁移减少碎片，但任务可锁定、并非全部可迁。SpotGPU由用户把HP任务标为有期限Standby，保留容器、停止主要服务进程并让出资源；回收时先驱逐LP，再恢复HP命令。调度首先尝试不抢占，按紧凑放置、同优先级共置、历史驱逐次数排序；必须抢占时最小化GPU数量×距最近成功checkpoint的时间。

## 本轮读到的证据

DE指原文直接可定位的作者测量/评估证据，不表示本项目重新测量。

- **LS08-E4**：部署中的LP退出均值13秒、P95为48秒，清理窗口60秒。 条件：用户自定义清理逻辑是主要耗时；未给出本段样本量/置信区间；不能当作HP服务完整恢复或电力响应时延。
- **LS08-E6**：分配率68%→93%，LP完成时间比上述复合消融对照减少24%。 条件：前者不是SM利用率，也不是有效进度或节能比例；后者对照同时改变多个策略，不能全归因于一个cost公式。
- **LS08-E2**：IPC两个月trace replay中部分占用节点减少20.2%。 条件：回放结果不能冒充该幅度的现场生产因果测量。

资源与任务级观测包括用户优先级/型号约束、分配和SM/显存利用、任务退出与迁移。IPC的20.2%部分占用节点减少来自两个月trace replay，不应写成生产直接实测值。SpotGPU退出时延来自部署；报告未在所读段落给出该时延样本量或置信区间。

## 局限与可借鉴之处

作者明确的条件：

- §6：专门的大规模foundation预训练集群不在观察范围；未细分SFT/LoRA等训练子类；关注GPU工作负载。
- §4.1：任务锁定与拓扑约束限制整理能力；§5.2明确仍有资源耦合和prefill/decode放置问题。

分析者推断（AI）和未知（UN）：

- AI：checkpoint之后的GPU-time只估计丢弃工作，不能自动表示清理、状态写出、通信初始化及首个有效结果的总时间。
- AI：用户自愿Standby与LP价格/SLO合约支撑可让出容量；不能推断所有训练任务可抢占，也不能将内部容量变化解释为外部电网限额。
- UN：HP“未受影响”的完整尾延迟曲线、负载匹配和SLO统计未独立审计；生产trace有单平台代表性限制。
- 伦理/数据：正文使用聚合生产数据；本轮未访问个人或租户原始记录，未审核更细的匿名化/发布程序。

与供电主题的关系：间接但需求依据强：真实存在混合紧急度、可暂借预留容量和有代价的抢占。原文处理集群容量和业务承诺，不处理物理供电功率上限。

学习状态定义与指标分母：已分配、忙碌、有用进度、可回收是四种不同量。六卡能帮助理解这些概念，但不限制阅读155,410GPU生产经验，也不能验证其规模收益。

## 阅读覆盖与下一入口

实际阅读本地PDF第4–5、8–14页：§2数据来源、§3任务/优先级特征、§4碎片与SpotGPU完整机制、§5开放挑战、§6边界。未逐页精读全部表征图、附录MILP或实现代码；正文定点广读。

未补齐项：未审计附录MILP、调度实现和原始生产trace。；缺少所报退出/HP影响的样本量、端到端恢复分解和独立重复统计。；未核验任何供电W/kW限制或现场电力响应实验；论文不是该类实验。

Idea线索仅作待核验问题，不作为创新结论：

- 已有方法已用checkpoint年龄估计抢占损失；后续阅读应继续区分工作丢弃量、资源释放延迟与服务恢复时间，不把三者合并。
- 可让出资源依赖用户选定窗口、任务类型和服务保证；混合紧急度研究需先解释任务为何允许等待或退出。

## ANALYSIS_PACKAGE

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-044-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LS08"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    {
      "evidence_id": "LS08-E1",
      "local_pdf": "research/papers/li_2026_spotgpu/paper.pdf",
      "location": "§2、§3.1，表1，PDF pp4–5（印刷pp2189–2190）；§6，PDF pp13–14"
    },
    {
      "evidence_id": "LS08-E2",
      "local_pdf": "research/papers/li_2026_spotgpu/paper.pdf",
      "location": "§4.1，PDF pp8–10（印刷pp2193–2195）"
    },
    {
      "evidence_id": "LS08-E3",
      "local_pdf": "research/papers/li_2026_spotgpu/paper.pdf",
      "location": "§4.2，PDF pp10–11（印刷pp2195–2196）"
    },
    {
      "evidence_id": "LS08-E4",
      "local_pdf": "research/papers/li_2026_spotgpu/paper.pdf",
      "location": "§4.2 Standby HP tasks，PDF p11（印刷p2196）"
    },
    {
      "evidence_id": "LS08-E5",
      "local_pdf": "research/papers/li_2026_spotgpu/paper.pdf",
      "location": "§4.2 Preemptive task scheduling，PDF p11（印刷p2196）"
    },
    {
      "evidence_id": "LS08-E6",
      "local_pdf": "research/papers/li_2026_spotgpu/paper.pdf",
      "location": "§4.2 Benefits of SpotGPU，PDF p12（印刷p2197）"
    },
    {
      "evidence_id": "LS08-E7",
      "local_pdf": "research/papers/li_2026_spotgpu/paper.pdf",
      "location": "§5.2、§6，PDF pp13–14（印刷pp2198–2199）"
    }
  ],
  "missing_evidence": [
    "未审计附录MILP、调度实现和原始生产trace。",
    "缺少所报退出/HP影响的样本量、端到端恢复分解和独立重复统计。",
    "未核验任何供电W/kW限制或现场电力响应实验；论文不是该类实验。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-044-v01",
  "source_identity_and_version": {
    "title": "Heterogeneity at Hyperscale: Characterization and Scheduling of Large Production AI Clusters at Alibaba (Operational Systems)",
    "authors": [
      "Suyi Li",
      "Lingyun Yang",
      "Haoxuan Yu",
      "Sheng Yao",
      "Tianyuan Wu",
      "Xiaoxiao Jiang",
      "Hanfeng Lu",
      "Kangjin Wang",
      "Chenhao Wang",
      "Shenglin Xu",
      "Lun Wang",
      "Qingyang Duan",
      "Shenghao Liang",
      "Xiu Lin",
      "Meng Zhang",
      "Wenchao Wu",
      "Yinghao Yu",
      "Guodong Yang",
      "Liping Zhang",
      "Wei Wang"
    ],
    "year": 2026,
    "venue": "OSDI",
    "official_url": "https://www.usenix.org/conference/osdi26/presentation/li-suyi",
    "pdf_url": "https://www.usenix.org/system/files/osdi26-li-suyi.pdf",
    "version": "USENIX OSDI 2026 正式会议 PDF，Operational Systems Paper；PDF含封面，正文印刷页2187–2203。",
    "local_pdf": "research/papers/li_2026_spotgpu/paper.pdf",
    "pdf_pages": 18,
    "reading_coverage": "实际阅读本地PDF第4–5、8–14页：§2数据来源、§3任务/优先级特征、§4碎片与SpotGPU完整机制、§5开放挑战、§6边界。未逐页精读全部表征图、附录MILP或实现代码；正文定点广读。"
  },
  "research_question": "大型生产AI集群里，预留容量、GPU类型偏好、CPU/GPU配比与网络局部性使“有空卡”和“任务能启动”并不等价。怎样使用低优先级任务消化可暂借容量，同时控制抢占损失和高优先级服务影响？",
  "study_type_and_context": "生产平台表征与已部署机制的经验报告，附trace replay和机制对照。数据覆盖2025年六个月、155,410张GPU、81个部门；专门的大规模基础模型预训练集群不在本trace内。",
  "design_units_samples_or_materials": "资源与任务级观测包括用户优先级/型号约束、分配和SM/显存利用、任务退出与迁移。IPC的20.2%部分占用节点减少来自两个月trace replay，不应写成生产直接实测值。SpotGPU退出时延来自部署；报告未在所读段落给出该时延样本量或置信区间。",
  "intervention_input_or_procedure": "ASI结合真实任务/设备画像、In-place Consolidation与SpotGPU。IPC用局部分组和递归迁移减少碎片，但任务可锁定、并非全部可迁。SpotGPU由用户把HP任务标为有期限Standby，保留容器、停止主要服务进程并让出资源；回收时先驱逐LP，再恢复HP命令。调度首先尝试不抢占，按紧凑放置、同优先级共置、历史驱逐次数排序；必须抢占时最小化GPU数量×距最近成功checkpoint的时间。",
  "measurements_and_analysis": "区分allocation ratio（已分配比例）、GPU实际计算/显存利用、LP完成时间、HP性能、碎片数量与退出时延。预抢占成本是GPU-time的丢弃工作估计，不是W，也不是完整恢复延迟。LP机制对照同时关闭共置/驱逐历史并改为随机选victim，不是只隔离单个checkpoint项。",
  "results_with_uncertainty": [
    {
      "evidence_type": "DE",
      "claim": "部署中的LP退出均值13秒、P95为48秒，清理窗口60秒。",
      "evidence_ids": [
        "LS08-E4"
      ],
      "uncertainty_and_boundary": "用户自定义清理逻辑是主要耗时；未给出本段样本量/置信区间；不能当作HP服务完整恢复或电力响应时延。"
    },
    {
      "evidence_type": "DE",
      "claim": "分配率68%→93%，LP完成时间比上述复合消融对照减少24%。",
      "evidence_ids": [
        "LS08-E6"
      ],
      "uncertainty_and_boundary": "前者不是SM利用率，也不是有效进度或节能比例；后者对照同时改变多个策略，不能全归因于一个cost公式。"
    },
    {
      "evidence_type": "DE",
      "claim": "IPC两个月trace replay中部分占用节点减少20.2%。",
      "evidence_ids": [
        "LS08-E2"
      ],
      "uncertainty_and_boundary": "回放结果不能冒充该幅度的现场生产因果测量。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "evidence_type": "SC",
      "claim": "ASI结合真实任务/设备画像、In-place Consolidation与SpotGPU。IPC用局部分组和递归迁移减少碎片，但任务可锁定、并非全部可迁。SpotGPU由用户把HP任务标为有期限Standby，保留容器、停止主要服务进程并让出资源；回收时先驱逐LP，再恢复HP命令。调度首先尝试不抢占，按紧凑放置、同优先级共置、历史驱逐次数排序；必须抢占时最小化GPU数量×距最近成功checkpoint的时间。",
      "evidence_ids": [
        "LS08-E2",
        "LS08-E3",
        "LS08-E5"
      ],
      "implementation_review": "仅据正文；未执行或审计代码。"
    },
    {
      "evidence_type": "DE",
      "claim": "作者的评估结果与其条件见results_with_uncertainty和证据索引。",
      "evidence_ids": [
        "LS08-E1",
        "LS08-E2",
        "LS08-E3",
        "LS08-E4",
        "LS08-E5",
        "LS08-E6",
        "LS08-E7"
      ],
      "implementation_review": "原文直接证据不等于本项目独立实测。"
    },
    {
      "evidence_type": "AI",
      "claim": "间接但需求依据强：真实存在混合紧急度、可暂借预留容量和有代价的抢占。原文处理集群容量和业务承诺，不处理物理供电功率上限。",
      "evidence_ids": [
        "LS08-E1",
        "LS08-E2",
        "LS08-E3",
        "LS08-E4",
        "LS08-E5",
        "LS08-E6",
        "LS08-E7"
      ],
      "implementation_review": "本项目阅读推断，不追加论文外效果。"
    }
  ],
  "limitations_biases_and_ethics": {
    "author_stated": [
      "§6：专门的大规模foundation预训练集群不在观察范围；未细分SFT/LoRA等训练子类；关注GPU工作负载。",
      "§4.1：任务锁定与拓扑约束限制整理能力；§5.2明确仍有资源耦合和prefill/decode放置问题。"
    ],
    "analyst_inference_and_unknown": [
      "AI：checkpoint之后的GPU-time只估计丢弃工作，不能自动表示清理、状态写出、通信初始化及首个有效结果的总时间。",
      "AI：用户自愿Standby与LP价格/SLO合约支撑可让出容量；不能推断所有训练任务可抢占，也不能将内部容量变化解释为外部电网限额。",
      "UN：HP“未受影响”的完整尾延迟曲线、负载匹配和SLO统计未独立审计；生产trace有单平台代表性限制。",
      "伦理/数据：正文使用聚合生产数据；本轮未访问个人或租户原始记录，未审核更细的匿名化/发布程序。"
    ]
  },
  "reproducibility_and_transferability": {
    "status": "未复现；未运行实验",
    "accessed": "research/papers/li_2026_spotgpu/paper.pdf",
    "not_accessed": "本轮未审计实现代码、原始日志或独立重复结果；未访问不等于材料不存在。",
    "hardware_boundary": "学习状态定义与指标分母：已分配、忙碌、有用进度、可回收是四种不同量。六卡能帮助理解这些概念，但不限制阅读155,410GPU生产经验，也不能验证其规模收益。",
    "transfer_stage": "当前仅analyze-paper。尚未形成Mode C或TRANSFER_PACKAGE，不声明迁移有效。"
  },
  "candidate_P": [
    {
      "id": "LS08-P1",
      "text": "在混合优先级系统中，哪些资源已分配但可暂时出借，以及抢占成本应按丢弃工作还是响应时延理解？",
      "evidence_ids": [
        "LS08-E1",
        "LS08-E2",
        "LS08-E3",
        "LS08-E4",
        "LS08-E5",
        "LS08-E6",
        "LS08-E7"
      ],
      "status": "来源支持的候选问题，待Mode C核验"
    }
  ],
  "candidate_F": [
    {
      "id": "LS08-F1",
      "text": "Standby资源可以被LP使用，但回收时存在13秒均值、48秒P95的用户清理；checkpoint损失和该退出时延是两个量。",
      "evidence_ids": [
        "LS08-E1",
        "LS08-E2",
        "LS08-E3",
        "LS08-E4",
        "LS08-E5",
        "LS08-E6",
        "LS08-E7"
      ],
      "status": "来源条件内现象候选，不能外推到本项目"
    }
  ],
  "evidence_index": [
    {
      "id": "LS08-E1",
      "evidence_type": "DE",
      "location": "§2、§3.1，表1，PDF pp4–5（印刷pp2189–2190）；§6，PDF pp13–14",
      "observation": "六个月生产trace覆盖155,410GPU；训练94.1%为HP、离线推理98.6%为LP。高/低优先级由用户与服务保证定义，不能按“训练=可随意暂停”分类。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "LS08-E2",
      "evidence_type": "DE",
      "location": "§4.1，PDF pp8–10（印刷pp2193–2195）",
      "observation": "GPU、CPU和网络局部性共同约束放置；约40%任务锁定不可迁移。IPC报告部署经验，并用两个月trace replay报告部分占用节点减少20.2%。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "LS08-E3",
      "evidence_type": "SC",
      "location": "§4.2，PDF pp10–11（印刷pp2195–2196）",
      "observation": "Standby由用户通过API选择时间窗并获得费用激励；容器保温，主进程替换sleep且切断请求流量。恢复跳过重新调度/拉镜像，但仍须驱逐LP并恢复HP服务命令。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "LS08-E4",
      "evidence_type": "DE",
      "location": "§4.2 Standby HP tasks，PDF p11（印刷p2196）",
      "observation": "LP优雅退出上限60秒；部署平均退出13秒，P95为48秒；超时SIGKILL少于5%。这是资源驱逐阶段的时延，不是HP恢复到首个有效请求的端到端时延。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "LS08-E5",
      "evidence_type": "SC",
      "location": "§4.2 Preemptive task scheduling，PDF p11（印刷p2196）",
      "observation": "已有抢占机制定义Ct=Gt×(τnow−τck)，最小化距最近成功checkpoint后丢弃的GPU-time；优先不抢占，LP新任务只能不抢占放置。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "LS08-E6",
      "evidence_type": "DE",
      "location": "§4.2 Benefits of SpotGPU，PDF p12（印刷p2197）",
      "observation": "GPU平均分配率68%→93%；关闭共置/驱逐历史并使用随机victim的对照下，LP完成时间减少24%，作者称HP性能未受影响。测量窗口和逐次统计在该段未展开。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "LS08-E7",
      "evidence_type": "DE",
      "location": "§5.2、§6，PDF pp13–14（印刷pp2198–2199）",
      "observation": "作者尚未解决的条件包括资源耦合、跨ASW拓扑与异构性能、prefill/decode的KV相关约束；trace不含专用foundation预训练集群，训练子类标签也未充分细分。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    }
  ],
  "unresolved_questions": [
    "HP恢复到第一个有效请求/稳定SLO的完整时间分布是什么？",
    "不同LP任务的checkpoint损失和退出清理耗时是否相关？原文现有汇总不足以判断。"
  ],
  "batch_date": "2026-11-05",
  "completed_on": "2026-11-06"
}
```
