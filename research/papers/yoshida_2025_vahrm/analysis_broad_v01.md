# VAHRM｜正文广读分析包

AN-20261105-045-v01 · RP03 · 2026-11-05 批次；2026-11-06 完成 · `partial`

阶段：`analyze-paper`；上游为已归档的正式PDF和来源初筛记录。下游：`deep-read Mode C`。本次没有新算法、baseline选择、选题决定或实验；台账由主汇总统一维护。

VAHRM: Variation-Aware Resource Management in Heterogeneous Supercomputing Systems

作者大学网站保存的IEEE TPDS正式排版版；36(8)，1713–1727，2025年8月；DOI 10.1109/TPDS.2025.3577252。 [官方来源](https://ieeexplore.ieee.org/document/11031465) · [本地全文](paper_tpds2025.pdf)

## 问题与方法

同型号CPU/GPU的制造差异会造成任务相关的功耗差异；如何在总功率预算下选择节点和作业顺序，使能量与等待/周转时间取得可调整的折中？

SC（来源陈述）：两阶段TSMVA功率模型先预测同类设备基线，再学习单设备偏差；输入为PMC特征，CPU/GPU分别建模。VAHRM按预估能量与等待时间的加权优先级选作业，把低预测功耗的空闲节点优先分配给它，再核查系统功率预算；首跑缺PMC的作业按TDP估计并随机选节点、收集画像。等待上限用于防止大作业长期饥饿。

## 本轮读到的证据

DE指原文直接可定位的作者测量/评估证据，不表示本项目重新测量。

- **RP03-E4**：P100功率预测RF+RF平均MAPE1.54%、最大7.64%；真实节点执行预定调度的能量校验平均误差0.94%。 条件：真实设备证据与模拟器能量验证；没有因此验证任意GPU/LLM、时变功率上限、尾部超限概率。训练/测试划分细节及在线附录本轮未完整审计。
- **RP03-E5**：主仿真九负载相对VU-FCFS平均节能4.5%，最大5.8%；相对CVA-FCFS-B平均4.2%。 条件：这里是模拟系统能量，输入含合成功率分布及重复作业假设；不能写成生产实测节电。
- **RP03-E6**：首跑比例超过40%时该敏感性分析的节能约1%。 条件：5%画像附加执行时间是作者设定，不是对所有首次作业的实测；9%首跑估计用用户/队列/核数/运行时间±10%启发式。

TSUBAME3.0上256张P100测GPU功率，Cygnus的128CPU及256V100用于模型/校验。主仿真为TSUBAME3.0式2,160GPU系统：256GPU用实测，其余1,904GPU按均值/方差生成正态随机功率；CPU分布从Cygnus外推。九种CPU/GPU比例和功率组合，使用2019年6–7月10,000条作业记录的提交/执行时间和节点请求。实机校验64节点，每种工作负载300作业。

## 局限与可借鉴之处

作者明确的条件：

- §III：已知运行时、独占节点和加速器作业CPU近似idle等前提；没有严格瞬时功率约束。
- §III/§VI：节能优先可恶化部分作业等待，β需要结合运行目标选择；平均改善不保证逐作业改善。
- §V：目标系统CPU功率无法由用户级RAPL获得，改用另一系统分布；大量GPU功率是统计扩展。
- §VI-C6：初次作业比例影响画像收益，真实日志不直接标注是否首次运行，使用启发式估计。

分析者推断（AI）和未知（UN）：

- AI：制造差异是同型号设备的静态/任务相关差异；没有证据表明作者把电网波峰波谷或动态预算作为控制输入。
- AI：缺CPU忙时和通信/重配成本会影响向分布式AI的迁移，不能直接把HPC节点模型视作多卡训练完整功耗。
- UN：PMC模型代码、完整附录、训练划分、原始时序功率和超限持续时间未核验；本轮未实施画像。
- 伦理/数据：使用既有调度日志的匿名用户ID作重复任务估计；本轮未访问原始用户数据。

与供电主题的关系：较直接但边界明确：系统总功率预算进入入场检查，目标含能量和作业周转；预算并非本文的时变输入，且允许瞬时超限，没有严格响应保证。

学习同型号硬件也可能有任务相关功耗差异、画像成本与排序公平性；六张3090有无足够差异目前未知，不能从P100/V100结果推定。

## 阅读覆盖与下一入口

实际阅读本地PDF第3–12页：§III前提/排序/分配，§IV两阶段画像，§V真实测量与仿真构造，§VI全部主要评估和敏感性。未读取在线附录PMC完整定义，未审核实现/原始数据；不是全面复现。

未补齐项：在线附录、实现、原始功率与任务日志未读取。；缺动态预算响应、checkpoint/抢占、通信与恢复阶段证据；原文未评价这些任务。；功率传感器边界/刷新及热状态影响没有在本轮逐项核对，不能解释为整机计量验证。

Idea线索仅作待核验问题，不作为创新结论：

- 已有方法已处理同型号功耗差异、等待老化和首跑保守画像；需理解什么时候设备排名重要、什么时候预算误差重要。
- “功率模型准确”与“调度更好”是两项证据；首跑比例和节点独占假设可能改变能效收益。

## ANALYSIS_PACKAGE

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-045-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "RP03"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    {
      "evidence_id": "RP03-E1",
      "local_pdf": "research/papers/yoshida_2025_vahrm/paper_tpds2025.pdf",
      "location": "§III-A–C，PDF pp3–4（印刷pp1715–1716）"
    },
    {
      "evidence_id": "RP03-E2",
      "local_pdf": "research/papers/yoshida_2025_vahrm/paper_tpds2025.pdf",
      "location": "§IV，PDF pp5–6（印刷pp1717–1718）"
    },
    {
      "evidence_id": "RP03-E3",
      "local_pdf": "research/papers/yoshida_2025_vahrm/paper_tpds2025.pdf",
      "location": "§V-A–E，PDF pp6–8（印刷pp1718–1720）"
    },
    {
      "evidence_id": "RP03-E4",
      "local_pdf": "research/papers/yoshida_2025_vahrm/paper_tpds2025.pdf",
      "location": "§VI-A–B，图5–6，PDF pp8–9（印刷pp1720–1721）"
    },
    {
      "evidence_id": "RP03-E5",
      "local_pdf": "research/papers/yoshida_2025_vahrm/paper_tpds2025.pdf",
      "location": "§VI-C1，图7–9，PDF p9（印刷p1721）"
    },
    {
      "evidence_id": "RP03-E6",
      "local_pdf": "research/papers/yoshida_2025_vahrm/paper_tpds2025.pdf",
      "location": "§III-C，图3，PDF p5；§VI-C2–6，PDF pp10–12（印刷pp1722–1724）"
    }
  ],
  "missing_evidence": [
    "在线附录、实现、原始功率与任务日志未读取。",
    "缺动态预算响应、checkpoint/抢占、通信与恢复阶段证据；原文未评价这些任务。",
    "功率传感器边界/刷新及热状态影响没有在本轮逐项核对，不能解释为整机计量验证。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-045-v01",
  "source_identity_and_version": {
    "title": "VAHRM: Variation-Aware Resource Management in Heterogeneous Supercomputing Systems",
    "authors": [
      "Kohei Yoshida",
      "Ryuichi Sakamoto",
      "Kento Sato",
      "Abhinav Bhatele",
      "Hayato Yamaki",
      "Hiroki Honda",
      "Shinobu Miwa"
    ],
    "year": 2025,
    "venue": "IEEE Transactions on Parallel and Distributed Systems, 36(8), 1713–1727",
    "official_url": "https://ieeexplore.ieee.org/document/11031465",
    "pdf_url": "https://www.cs.umd.edu/~bhatele/pubs/pdf/2025/tpds2025.pdf",
    "version": "作者大学网站保存的IEEE TPDS正式排版版；36(8)，1713–1727，2025年8月；DOI 10.1109/TPDS.2025.3577252。",
    "local_pdf": "research/papers/yoshida_2025_vahrm/paper_tpds2025.pdf",
    "pdf_pages": 15,
    "reading_coverage": "实际阅读本地PDF第3–12页：§III前提/排序/分配，§IV两阶段画像，§V真实测量与仿真构造，§VI全部主要评估和敏感性。未读取在线附录PMC完整定义，未审核实现/原始数据；不是全面复现。"
  },
  "research_question": "同型号CPU/GPU的制造差异会造成任务相关的功耗差异；如何在总功率预算下选择节点和作业顺序，使能量与等待/周转时间取得可调整的折中？",
  "study_type_and_context": "功率模型实机数据＋主要调度仿真＋真实节点能量校验。前提为CPU作业或单类加速器作业、节点独占；加速器作业中CPU按idle功率计。系统级预算允许瞬时超过，不做节点功率封顶。论文没有研究随时间变化的外部供电额度。",
  "design_units_samples_or_materials": "TSUBAME3.0上256张P100测GPU功率，Cygnus的128CPU及256V100用于模型/校验。主仿真为TSUBAME3.0式2,160GPU系统：256GPU用实测，其余1,904GPU按均值/方差生成正态随机功率；CPU分布从Cygnus外推。九种CPU/GPU比例和功率组合，使用2019年6–7月10,000条作业记录的提交/执行时间和节点请求。实机校验64节点，每种工作负载300作业。",
  "intervention_input_or_procedure": "两阶段TSMVA功率模型先预测同类设备基线，再学习单设备偏差；输入为PMC特征，CPU/GPU分别建模。VAHRM按预估能量与等待时间的加权优先级选作业，把低预测功耗的空闲节点优先分配给它，再核查系统功率预算；首跑缺PMC的作业按TDP估计并随机选节点、收集画像。等待上限用于防止大作业长期饥饿。",
  "measurements_and_analysis": "功率预测MAPE、模拟系统能量、平均周转/等待与吞吐；仿真步长1秒，忽略调度决策耗时。对照含不感知差异和CPU/CPU+GPU差异感知的FCFS及backfill。报告β、到达压缩SR和功率约束比PCR敏感性、预测误差与首跑比例。能量收益不等于瞬时功率响应。",
  "results_with_uncertainty": [
    {
      "evidence_type": "DE",
      "claim": "P100功率预测RF+RF平均MAPE1.54%、最大7.64%；真实节点执行预定调度的能量校验平均误差0.94%。",
      "evidence_ids": [
        "RP03-E4"
      ],
      "uncertainty_and_boundary": "真实设备证据与模拟器能量验证；没有因此验证任意GPU/LLM、时变功率上限、尾部超限概率。训练/测试划分细节及在线附录本轮未完整审计。"
    },
    {
      "evidence_type": "DE",
      "claim": "主仿真九负载相对VU-FCFS平均节能4.5%，最大5.8%；相对CVA-FCFS-B平均4.2%。",
      "evidence_ids": [
        "RP03-E5"
      ],
      "uncertainty_and_boundary": "这里是模拟系统能量，输入含合成功率分布及重复作业假设；不能写成生产实测节电。"
    },
    {
      "evidence_type": "DE",
      "claim": "首跑比例超过40%时该敏感性分析的节能约1%。",
      "evidence_ids": [
        "RP03-E6"
      ],
      "uncertainty_and_boundary": "5%画像附加执行时间是作者设定，不是对所有首次作业的实测；9%首跑估计用用户/队列/核数/运行时间±10%启发式。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "evidence_type": "SC",
      "claim": "两阶段TSMVA功率模型先预测同类设备基线，再学习单设备偏差；输入为PMC特征，CPU/GPU分别建模。VAHRM按预估能量与等待时间的加权优先级选作业，把低预测功耗的空闲节点优先分配给它，再核查系统功率预算；首跑缺PMC的作业按TDP估计并随机选节点、收集画像。等待上限用于防止大作业长期饥饿。",
      "evidence_ids": [
        "RP03-E1",
        "RP03-E2"
      ],
      "implementation_review": "仅据正文；未执行或审计代码。"
    },
    {
      "evidence_type": "DE",
      "claim": "作者的评估结果与其条件见results_with_uncertainty和证据索引。",
      "evidence_ids": [
        "RP03-E1",
        "RP03-E2",
        "RP03-E3",
        "RP03-E4",
        "RP03-E5",
        "RP03-E6"
      ],
      "implementation_review": "原文直接证据不等于本项目独立实测。"
    },
    {
      "evidence_type": "AI",
      "claim": "较直接但边界明确：系统总功率预算进入入场检查，目标含能量和作业周转；预算并非本文的时变输入，且允许瞬时超限，没有严格响应保证。",
      "evidence_ids": [
        "RP03-E1",
        "RP03-E2",
        "RP03-E3",
        "RP03-E4",
        "RP03-E5",
        "RP03-E6"
      ],
      "implementation_review": "本项目阅读推断，不追加论文外效果。"
    }
  ],
  "limitations_biases_and_ethics": {
    "author_stated": [
      "§III：已知运行时、独占节点和加速器作业CPU近似idle等前提；没有严格瞬时功率约束。",
      "§III/§VI：节能优先可恶化部分作业等待，β需要结合运行目标选择；平均改善不保证逐作业改善。",
      "§V：目标系统CPU功率无法由用户级RAPL获得，改用另一系统分布；大量GPU功率是统计扩展。",
      "§VI-C6：初次作业比例影响画像收益，真实日志不直接标注是否首次运行，使用启发式估计。"
    ],
    "analyst_inference_and_unknown": [
      "AI：制造差异是同型号设备的静态/任务相关差异；没有证据表明作者把电网波峰波谷或动态预算作为控制输入。",
      "AI：缺CPU忙时和通信/重配成本会影响向分布式AI的迁移，不能直接把HPC节点模型视作多卡训练完整功耗。",
      "UN：PMC模型代码、完整附录、训练划分、原始时序功率和超限持续时间未核验；本轮未实施画像。",
      "伦理/数据：使用既有调度日志的匿名用户ID作重复任务估计；本轮未访问原始用户数据。"
    ]
  },
  "reproducibility_and_transferability": {
    "status": "未复现；未运行实验",
    "accessed": "research/papers/yoshida_2025_vahrm/paper_tpds2025.pdf",
    "not_accessed": "本轮未审计实现代码、原始日志或独立重复结果；未访问不等于材料不存在。",
    "hardware_boundary": "学习同型号硬件也可能有任务相关功耗差异、画像成本与排序公平性；六张3090有无足够差异目前未知，不能从P100/V100结果推定。",
    "transfer_stage": "当前仅analyze-paper。尚未形成Mode C或TRANSFER_PACKAGE，不声明迁移有效。"
  },
  "candidate_P": [
    {
      "id": "RP03-P1",
      "text": "同型号设备差异和任务画像可用性如何影响预算下的可分配节点与能量/等待权衡？",
      "evidence_ids": [
        "RP03-E1",
        "RP03-E2",
        "RP03-E3",
        "RP03-E4",
        "RP03-E5",
        "RP03-E6"
      ],
      "status": "来源支持的候选问题，待Mode C核验"
    }
  ],
  "candidate_F": [
    {
      "id": "RP03-F1",
      "text": "在作者的混合工作负载模拟中节能收益依赖首跑比例，且平均周转收益可与个别大作业等待恶化共存。",
      "evidence_ids": [
        "RP03-E1",
        "RP03-E2",
        "RP03-E3",
        "RP03-E4",
        "RP03-E5",
        "RP03-E6"
      ],
      "status": "来源条件内现象候选，不能外推到本项目"
    }
  ],
  "evidence_index": [
    {
      "id": "RP03-E1",
      "evidence_type": "SC",
      "location": "§III-A–C，PDF pp3–4（印刷pp1715–1716）",
      "observation": "节点独占、CPU/accelerator二类任务；加速器任务CPU按idle计；仅系统预算、无节点power cap，允许瞬时超过预算。预计执行时间事先可知，权重β需运行测试确定。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "RP03-E2",
      "evidence_type": "SC",
      "location": "§IV，PDF pp5–6（印刷pp1717–1718）",
      "observation": "TSMVA用同类基线模型加资源特定残差模型，六类PMC特征，RF+RF为主要实例；首跑缺画像按TDP保守入场。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "RP03-E3",
      "evidence_type": "DE",
      "location": "§V-A–E，PDF pp6–8（印刷pp1718–1720）",
      "observation": "主要评估为1秒步长调度模拟；九组合负载和10,000条日志；2,160GPU中256GPU实测，其余1,904GPU用正态随机数补齐，CPU分布从另一系统外推。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "RP03-E4",
      "evidence_type": "DE",
      "location": "§VI-A–B，图5–6，PDF pp8–9（印刷pp1720–1721）",
      "observation": "P100的RF+RF两阶段功率模型平均MAPE1.54%、最大7.64%；64节点真实执行预定调度的能量校验平均误差0.94%、最大1.76%。后者不是生产在线调度部署。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "RP03-E5",
      "evidence_type": "DE",
      "location": "§VI-C1，图7–9，PDF p9（印刷p1721）",
      "observation": "主调度仿真：β=(1/4)^6、SR=1、PCR=1；九负载相对VU-FCFS平均能量减少4.5%、最大5.8%。相对CVA-FCFS-B平均节能4.2%；吞吐相对VU-FCFS提升7.1%。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "RP03-E6",
      "evidence_type": "DE",
      "location": "§III-C，图3，PDF p5；§VI-C2–6，PDF pp10–12（印刷pp1722–1724）",
      "observation": "排序会产生个体等待代价；部分大作业等待可达backfill对照18倍。首跑作业收集PMC的附加执行时间在敏感性实验中假设为5%，首跑比例>40%时节能约1%；原日志不标注重跑，约9%首跑来自启发式同作业判定。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    }
  ],
  "unresolved_questions": [
    "静态制造差异是否在实际AI计算、通信和低功率运行状态下保持相同排序？",
    "瞬时超限允许多大、多长，缺失尾部功率证据时如何理解预算可行性？原文没有给出硬响应保证。"
  ],
  "batch_date": "2026-11-05",
  "completed_on": "2026-11-06"
}
```
