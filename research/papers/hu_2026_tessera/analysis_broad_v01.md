# Tessera｜正文广读分析包

AN-20261105-043-v01 · LN08 · 2026-11-05 批次；2026-11-06 完成 · `partial`

阶段：`analyze-paper`；上游为已归档的正式PDF和来源初筛记录。下游：`deep-read Mode C`。本次没有新算法、baseline选择、选题决定或实验；台账由主汇总统一维护。

Tessera: A Holistic Pipeline Parallelism Framework for Trillion-Parameter Heterogeneous MoE Training (Operational Systems)

USENIX OSDI 2026 正式会议 PDF，Operational Systems Paper；PDF 含封面，正文印刷页287–304。 [官方来源](https://www.usenix.org/conference/osdi26/presentation/hu-weifang) · [本地全文](paper.pdf)

## 问题与方法

异构 MoE 层的计算与通信可重叠程度不同；按层数或串行 FLOPs 平衡流水线，仍可能留下不同的实际关键路径。怎样联合划分流水线与安排可移动任务？

SC（来源陈述）：静态部分把候选相邻 chunk 的方向性重叠构成图，细化计算/通信 DAG，用事件启发式生成执行顺序并在匹配 TP/EP 拓扑的参考 GPU 组上 profile；MILP 最小化最慢的重叠后成本。运行期 DBO 从路由 token 数和离线画像预测局部 EP 空隙，把满足依赖、截止位置和内存约束的 Wgrad/梯度归约放入空隙；有界任务池限制延长存活期的状态。

## 本轮读到的证据

DE指原文直接可定位的作者测量/评估证据，不表示本项目重新测量。

- **LN08-E3**：生产表2报告MFU相对增益20%–32.8%；15.9%→21.1%是+5.2个百分点。 条件：不同模型与规模；对照为内部优化Megatron。未核验原始日志、重复运行置信区间或第三方复现，MFU不是板卡/整机能效。
- **LN08-E5**：指定DBO消融中迭代时间6.98→6.67秒，峰值分配内存67.4%→70.0%。 条件：同一静态分区的组件对照，内存代价明确；不是所有MoE模型的固定收益。
- **LN08-E4**：启发式的profile后成本距ILP参考结果约1%。 条件：部分ILP未证明最优，因此不能说距全局最优1%。

生产表2含5个4,096–12,288 GPU作业；服务器每台8张Hopper GPU，RoCE网络。公开比较用256GPU；静态消融128/256GPU并以均匀mock router隔离动态路由；DBO消融256GPU、4K序列、Qwen3-Next-M，每GPU可移动任务池上限8。

## 局限与可借鉴之处

作者明确的条件：

- §5：理论成本与硬件重叠存在SM争用导致的偏差；任务延后增加激活/梯度存活期，需有界池防止OOM。
- §6.2：模型计算/通信比决定可隐藏通信；Next-M与Next-L的暴露通信不同，分区平衡仍保留warmup/cooldown与结构性等待。
- §6.5–6.7：候选空间与ILP时间限制；profile是重要离线成本，求解快不表示无画像成本。

分析者推断（AI）和未知（UN）：

- AI：原文处理层/算子异构和路由变化，没有以GPU功率上限变化为输入；功率变化后画像、可重叠窗口与内存余量是否仍有效未知。
- AI：生产收益受内部框架、模型和特定拓扑条件影响；mock-router消融帮助归因，却不能替代真实路由尾部行为。
- UN：代码、原始trace、逐次运行及不确定区间未审计；生产比特一致性是作者对指定确定性运行的报告，不是本项目验证。

与供电主题的关系：间接：提供实际重叠成本、关键路径与内存副作用的系统证据；没有直接处理外部供电上限或能量优化。

可学习“串行成本和实际暴露通信不同”及组件消融的组织方式；概念学习不受六卡限制。原生产规模、路由特征和多级并行收益不可由六卡结果外推。

## 阅读覆盖与下一入口

实际阅读本地PDF第4–15页：§2问题与重叠现象，§3–4方法和执行机制，§5模型—实机差距，§6评估/消融/开销。未逐条审阅相关工作与参考文献，未读代码或原始训练日志；属于正文定点广读，不是全文精读或复现。

未补齐项：未核查实现、原始计时和逐次运行；统计区间未独立验证。；未建立功率改变时profile和依赖时限的有效范围；没有读取真实功率测量。；尚未对完整相关工作和其他框架上的可迁移性作系统对读。

Idea线索仅作待核验问题，不作为创新结论：

- 已有机制已覆盖重叠后画像与局部bubble填充；待理解的是画像与执行条件变化时，哪些成本仍稳定，哪些会改变关键路径。
- 调度改善速度同时延长状态存活期；需要把内存代价视为原文明确的条件，而不是默认免费重叠。

## ANALYSIS_PACKAGE

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-043-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LN08"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    {
      "evidence_id": "LN08-E1",
      "local_pdf": "research/papers/hu_2026_tessera/paper.pdf",
      "location": "§2–3，PDF pp4–8（印刷pp289–293）"
    },
    {
      "evidence_id": "LN08-E2",
      "local_pdf": "research/papers/hu_2026_tessera/paper.pdf",
      "location": "§5，PDF p10（印刷p295）"
    },
    {
      "evidence_id": "LN08-E3",
      "local_pdf": "research/papers/hu_2026_tessera/paper.pdf",
      "location": "§6.1–6.2，表2，PDF pp11–12（印刷pp296–297）"
    },
    {
      "evidence_id": "LN08-E4",
      "local_pdf": "research/papers/hu_2026_tessera/paper.pdf",
      "location": "§6.3–6.5，PDF pp12–14（印刷pp297–299）"
    },
    {
      "evidence_id": "LN08-E5",
      "local_pdf": "research/papers/hu_2026_tessera/paper.pdf",
      "location": "§6.6，表3，PDF p14（印刷p299）"
    },
    {
      "evidence_id": "LN08-E6",
      "local_pdf": "research/papers/hu_2026_tessera/paper.pdf",
      "location": "§6.7，PDF pp14–15（印刷pp299–300）"
    }
  ],
  "missing_evidence": [
    "未核查实现、原始计时和逐次运行；统计区间未独立验证。",
    "未建立功率改变时profile和依赖时限的有效范围；没有读取真实功率测量。",
    "尚未对完整相关工作和其他框架上的可迁移性作系统对读。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-043-v01",
  "source_identity_and_version": {
    "title": "Tessera: A Holistic Pipeline Parallelism Framework for Trillion-Parameter Heterogeneous MoE Training (Operational Systems)",
    "authors": [
      "Weifang Hu",
      "Langshi Chen",
      "Man Yuan",
      "Youyang Yao",
      "Xiulong Yuan",
      "Li Tian",
      "Yong Li",
      "Wei Lin",
      "Xuanhua Shi",
      "Zhengping Qian",
      "Jingren Zhou"
    ],
    "year": 2026,
    "venue": "OSDI",
    "official_url": "https://www.usenix.org/conference/osdi26/presentation/hu-weifang",
    "pdf_url": "https://www.usenix.org/system/files/osdi26-hu-weifang.pdf",
    "version": "USENIX OSDI 2026 正式会议 PDF，Operational Systems Paper；PDF 含封面，正文印刷页287–304。",
    "local_pdf": "research/papers/hu_2026_tessera/paper.pdf",
    "pdf_pages": 19,
    "reading_coverage": "实际阅读本地PDF第4–15页：§2问题与重叠现象，§3–4方法和执行机制，§5模型—实机差距，§6评估/消融/开销。未逐条审阅相关工作与参考文献，未读代码或原始训练日志；属于正文定点广读，不是全文精读或复现。"
  },
  "research_question": "异构 MoE 层的计算与通信可重叠程度不同；按层数或串行 FLOPs 平衡流水线，仍可能留下不同的实际关键路径。怎样联合划分流水线与安排可移动任务？",
  "study_type_and_context": "生产系统论文：内部优化的 Megatron 基线上进行生产规模前后配置比较；另有公开模型、组件消融和 profile/规划开销实验。重叠图的最慢边是分区代理目标，不等同于整项训练的全局最优。",
  "design_units_samples_or_materials": "生产表2含5个4,096–12,288 GPU作业；服务器每台8张Hopper GPU，RoCE网络。公开比较用256GPU；静态消融128/256GPU并以均匀mock router隔离动态路由；DBO消融256GPU、4K序列、Qwen3-Next-M，每GPU可移动任务池上限8。",
  "intervention_input_or_procedure": "静态部分把候选相邻 chunk 的方向性重叠构成图，细化计算/通信 DAG，用事件启发式生成执行顺序并在匹配 TP/EP 拓扑的参考 GPU 组上 profile；MILP 最小化最慢的重叠后成本。运行期 DBO 从路由 token 数和离线画像预测局部 EP 空隙，把满足依赖、截止位置和内存约束的 Wgrad/梯度归约放入空隙；有界任务池限制延长存活期的状态。",
  "measurements_and_analysis": "MFU、迭代时间、暴露通信/流水线等待、峰值分配内存、profile和求解耗时。生产表2以内部优化基线为对照；公开256GPU实验另以Megatron-Core MoE v0.16.1为对照。DBO的always-keep保留监测决策但不改变任务位置，用以区分控制路径开销和重排收益。",
  "results_with_uncertainty": [
    {
      "evidence_type": "DE",
      "claim": "生产表2报告MFU相对增益20%–32.8%；15.9%→21.1%是+5.2个百分点。",
      "evidence_ids": [
        "LN08-E3"
      ],
      "uncertainty_and_boundary": "不同模型与规模；对照为内部优化Megatron。未核验原始日志、重复运行置信区间或第三方复现，MFU不是板卡/整机能效。"
    },
    {
      "evidence_type": "DE",
      "claim": "指定DBO消融中迭代时间6.98→6.67秒，峰值分配内存67.4%→70.0%。",
      "evidence_ids": [
        "LN08-E5"
      ],
      "uncertainty_and_boundary": "同一静态分区的组件对照，内存代价明确；不是所有MoE模型的固定收益。"
    },
    {
      "evidence_type": "DE",
      "claim": "启发式的profile后成本距ILP参考结果约1%。",
      "evidence_ids": [
        "LN08-E4"
      ],
      "uncertainty_and_boundary": "部分ILP未证明最优，因此不能说距全局最优1%。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "evidence_type": "SC",
      "claim": "静态部分把候选相邻 chunk 的方向性重叠构成图，细化计算/通信 DAG，用事件启发式生成执行顺序并在匹配 TP/EP 拓扑的参考 GPU 组上 profile；MILP 最小化最慢的重叠后成本。运行期 DBO 从路由 token 数和离线画像预测局部 EP 空隙，把满足依赖、截止位置和内存约束的 Wgrad/梯度归约放入空隙；有界任务池限制延长存活期的状态。",
      "evidence_ids": [
        "LN08-E1",
        "LN08-E2"
      ],
      "implementation_review": "仅据正文；未执行或审计代码。"
    },
    {
      "evidence_type": "DE",
      "claim": "作者的评估结果与其条件见results_with_uncertainty和证据索引。",
      "evidence_ids": [
        "LN08-E1",
        "LN08-E2",
        "LN08-E3",
        "LN08-E4",
        "LN08-E5",
        "LN08-E6"
      ],
      "implementation_review": "原文直接证据不等于本项目独立实测。"
    },
    {
      "evidence_type": "AI",
      "claim": "间接：提供实际重叠成本、关键路径与内存副作用的系统证据；没有直接处理外部供电上限或能量优化。",
      "evidence_ids": [
        "LN08-E1",
        "LN08-E2",
        "LN08-E3",
        "LN08-E4",
        "LN08-E5",
        "LN08-E6"
      ],
      "implementation_review": "本项目阅读推断，不追加论文外效果。"
    }
  ],
  "limitations_biases_and_ethics": {
    "author_stated": [
      "§5：理论成本与硬件重叠存在SM争用导致的偏差；任务延后增加激活/梯度存活期，需有界池防止OOM。",
      "§6.2：模型计算/通信比决定可隐藏通信；Next-M与Next-L的暴露通信不同，分区平衡仍保留warmup/cooldown与结构性等待。",
      "§6.5–6.7：候选空间与ILP时间限制；profile是重要离线成本，求解快不表示无画像成本。"
    ],
    "analyst_inference_and_unknown": [
      "AI：原文处理层/算子异构和路由变化，没有以GPU功率上限变化为输入；功率变化后画像、可重叠窗口与内存余量是否仍有效未知。",
      "AI：生产收益受内部框架、模型和特定拓扑条件影响；mock-router消融帮助归因，却不能替代真实路由尾部行为。",
      "UN：代码、原始trace、逐次运行及不确定区间未审计；生产比特一致性是作者对指定确定性运行的报告，不是本项目验证。"
    ]
  },
  "reproducibility_and_transferability": {
    "status": "未复现；未运行实验",
    "accessed": "research/papers/hu_2026_tessera/paper.pdf",
    "not_accessed": "本轮未审计实现代码、原始日志或独立重复结果；未访问不等于材料不存在。",
    "hardware_boundary": "可学习“串行成本和实际暴露通信不同”及组件消融的组织方式；概念学习不受六卡限制。原生产规模、路由特征和多级并行收益不可由六卡结果外推。",
    "transfer_stage": "当前仅analyze-paper。尚未形成Mode C或TRANSFER_PACKAGE，不声明迁移有效。"
  },
  "candidate_P": [
    {
      "id": "LN08-P1",
      "text": "资源执行条件改变时，用单独算子或串行成本估算最终可用进度会遗漏哪些重叠与内存条件？",
      "evidence_ids": [
        "LN08-E1",
        "LN08-E2",
        "LN08-E3",
        "LN08-E4",
        "LN08-E5",
        "LN08-E6"
      ],
      "status": "来源支持的候选问题，待Mode C核验"
    }
  ],
  "candidate_F": [
    {
      "id": "LN08-F1",
      "text": "同一静态分区中DBO降低迭代时间，但增加峰值内存；理论最优顺序与实际硬件成本也有偏差。",
      "evidence_ids": [
        "LN08-E1",
        "LN08-E2",
        "LN08-E3",
        "LN08-E4",
        "LN08-E5",
        "LN08-E6"
      ],
      "status": "来源条件内现象候选，不能外推到本项目"
    }
  ],
  "evidence_index": [
    {
      "id": "LN08-E1",
      "evidence_type": "SC",
      "location": "§2–3，PDF pp4–8（印刷pp289–293）",
      "observation": "串行平衡不足；静态候选划分、重叠成本画像和MILP；DBO使用局部路由信号和受依赖/截止位置/内存约束的可移动任务池。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "LN08-E2",
      "evidence_type": "DE",
      "location": "§5，PDF p10（印刷p295）",
      "observation": "作者报告纯理论成本平均低估约5%，EP通信会占用SM并拖慢重叠计算；过度延后任务曾导致OOM。移动平均预测抖动属于未来系统验证。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "LN08-E3",
      "evidence_type": "DE",
      "location": "§6.1–6.2，表2，PDF pp11–12（印刷pp296–297）",
      "observation": "五组生产工作负载相对内部优化基线的MFU增益为20%–32.8%。Qwen3-Next-XL的12,288GPU结果为15.9%→21.1% MFU；绝对提升5.2个百分点。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "LN08-E4",
      "evidence_type": "DE",
      "location": "§6.3–6.5，PDF pp12–14（印刷pp297–299）",
      "observation": "公开模型比较与mock-router静态消融另行报告；ILP每实例300秒上限，多例未证明最优，约1%的差距是相对于该ILP参考结果。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "LN08-E5",
      "evidence_type": "DE",
      "location": "§6.6，表3，PDF p14（印刷p299）",
      "observation": "256GPU、Qwen3-Next-M、4K序列，overlap-aware静态分区上启用DBO：迭代6.98→6.67秒（作者报告4.4%下降），峰值内存67.4%→70.0%（+2.6个百分点）；always-keep为7.06秒。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "LN08-E6",
      "evidence_type": "DE",
      "location": "§6.7，PDF pp14–15（印刷pp299–300）",
      "observation": "8,192GPU目标配置的离线profile在64GPU参考组上执行；最长3,050秒，MILP耗时0.34–4.22秒，不能只以求解时间代表整个规划成本。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    }
  ],
  "unresolved_questions": [
    "功率或频率改变后参考组profile是否仍能预测重叠成本？原文未测试。",
    "不同延后策略的尾部内存和截止位置违约是否有逐次记录？未读取原始数据。"
  ],
  "batch_date": "2026-11-05",
  "completed_on": "2026-11-06"
}
```
