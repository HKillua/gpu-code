# Tiresias｜短作业能否绕过长队

Juncheng Gu 等，NSDI 2019。本次定位阅读§3、§5.1–5.4、§6，重点图 10–11 与表 4，阶段为 `analyze-paper`，来源 LS01。下文的实测指作者报告的结果。 [正式书目](https://www.usenix.org/conference/nsdi19/presentation/gu) · [会议全文](https://www.usenix.org/system/files/nsdi19-gu.pdf)

设一份训练已经用了 2 张卡 100 秒，另一份用了 1 张卡 100 秒。以卡数乘运行时间计，前者已获得的服务更多。Tiresias 用这类能在线观测的量给作业排队。等待处理的是系统开销，任务的业务截止期需要单独提供。 [方法](https://www.usenix.org/system/files/nsdi19-gu.pdf#page=6)

§5.2 实机中，Tiresias-L 的平均作业完成时间约为 YARN-CS 的 1/5.5。图 11 的 GPU 利用率曲线大致相似。优先服务短小作业主要改变了等待时间。 [评估设置](https://www.usenix.org/system/files/nsdi19-gu.pdf#page=10) · [实验结果](https://www.usenix.org/system/files/nsdi19-gu.pdf#page=11)

在两台各三卡的环境里，区分等待、实际运行和提交至完成；把作业所需卡数和能否跨机放置作为单独条件。队列优先级由已获服务决定。论文未用外部供电上限定义业务紧急度或调度可行性。 本轮未核对代码或做独立测量。

SC 表示作者主张，DE 表示可定位的正文证据，AI 表示本次迁移推断，UN 表示待核验项。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-011-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LS01"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LS01-E1",
    "LS01-E2",
    "LS01-E3",
    "LS01-E4"
  ],
  "missing_evidence": [
    "未检查代码、原始排队日志或随机种子，未独立复现。",
    "没有电功率读数、功率上限、截止期标签或 3090 上抢占与恢复实测。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-011-v01",
  "source_identity_and_version": {
    "id": "LS01",
    "title": "Tiresias: A GPU Cluster Manager for Distributed Deep Learning",
    "authors": [
      "Juncheng Gu",
      "Mosharaf Chowdhury",
      "Kang G. Shin",
      "Yibo Zhu",
      "Myeongjae Jeon",
      "Junjie Qian",
      "Hongqiang Liu",
      "Chuanxiong Guo"
    ],
    "venue": "NSDI 2019",
    "pages": "485–500",
    "official_url": "https://www.usenix.org/conference/nsdi19/presentation/gu",
    "pdf_url": "https://www.usenix.org/system/files/nsdi19-gu.pdf",
    "material": "正式会议版本，正文图表和节号依 USENIX 公开 PDF，链接页码从封面算起。",
    "coverage": "§3、§5.1–5.4、§6，重点图 10–11 与表 4。仅查正式论文及书目，本轮未核对代码、数据工件或执行机器。"
  },
  "research_question": "SC｜多卡训练要等所需 GPU 凑齐，提交时又常说不准运行多久。怎样减少作业从提交到完成的时间？",
  "study_type_and_context": "DE｜实机原型与离散时间模拟。真正执行训练的测试床为 15 台四卡 P100，来自 Microsoft 生产轨迹的并发分布用于构造输入。大规模模拟使用原始轨迹，不能计算动态环境导致的训练时间变化。",
  "design_units_samples_or_materials": "DE｜480 个合成作业，半数单卡，各跑固定迭代数，泊松到达。每机四卡、有 100 Gbps InfiniBand，抢占用的共享 GPFS 每机读写吞吐为 1.2 GB/s。规模、到达、GPU 数和存储性能都影响等待与切换成本。",
  "intervention_input_or_procedure": "DE｜按作业累计使用的 GPU 数和运行时间更新优先级，将连续优先级分成少数队列，减少反复抢占；结合模型对放置的敏感性安排 GPU。",
  "measurements_and_analysis": "DE｜从提交到结束的 JCT 包含排队和执行。比较平均 JCT、按大小和长短分组的 JCT、排队延迟及整组 GPU 利用率。原实验图 10 的 5.5 是平均完成时间比值，图 11 是随时间变化的利用率。",
  "results_with_uncertainty": [
    {
      "label": "DE",
      "finding": "Tiresias-L 的平均 JCT 约为 YARN-CS 的 1/5.5。短小作业获益明显，大且长组的平均 JCT 与 YARN-CS 接近。",
      "comparison_and_uncertainty": "相同合成负载的两种调度器。实验关闭防饥饿提升，5.5 不能解释为每作业都加速相同比例。",
      "evidence": "LS01-E3"
    },
    {
      "label": "DE",
      "finding": "整体 GPU 利用率曲线相似，队列等待和平均 JCT 却明显不同。",
      "comparison_and_uncertainty": "原图的定性比较。未从曲线反推功率或忙闲间隔。",
      "evidence": "LS01-E3"
    }
  ],
  "claim_evidence_implementation_map": [
    "SC｜不依赖每个作业的精确时长即可降低平均等待与完成时间。",
    "DE｜§3 伪代码给出卡数和已运行时间的作用，§5.2 记录了真实训练进程在合成负载上的对照。",
    "AI｜新增的业务紧急标签应单列。短、少占卡与临近截止是不同属性。",
    "UN｜未核对代码分支、实际 checkpoint 延迟分布和本地网络条件。"
  ],
  "limitations_biases_and_ethics": [
    "等待长短受负载组合和短作业占比影响。固定迭代、缩放的提交分布及高速共享存储限制外推。",
    "模拟复用轨迹时长，无法重新估计变化的放置或速度对训练时长的全部影响。实机对比未检出重复试验置信区间。",
    "长作业的等待属于分配影响，生产租户的紧急标签和逐作业服务承诺未由本次输入给出。"
  ],
  "reproducibility_and_transferability": {
    "evidence": "论文报告的配置和结果可定位。代码与结果的一致性尚未独立复核，本次不构成复现。",
    "transferable": "在两台各三卡的环境里，区分等待、实际运行和提交至完成；把作业所需卡数和能否跨机放置作为单独条件。",
    "boundary": "间接。队列优先级由已获服务决定。论文未用外部供电上限定义业务紧急度或调度可行性。"
  },
  "candidate_P": [
    {
      "id": "LS01-P1",
      "status": "AI｜候选，待 Mode C 核验",
      "statement": "卡数相同而单卡执行速率因配置改变时，GPU·秒对累计有效服务的代表性如何？这是本文度量启发的待核验问题。",
      "evidence": [
        "LS01-E1",
        "LS01-E2",
        "LS01-E3",
        "LS01-E4"
      ]
    }
  ],
  "candidate_F": [
    {
      "id": "LS01-F1",
      "status": "DE｜来源观察，待 Mode C 核验",
      "statement": "在作者的 480 项合成负载中，总体利用率曲线相近，而平均提交至完成时间和短作业等待差别明显。",
      "evidence": [
        "LS01-E3"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "LS01-E1",
      "where": "§3.2.2–3.3，累计服务、多级队列、放置",
      "url": "https://www.usenix.org/system/files/nsdi19-gu.pdf#page=6"
    },
    {
      "id": "LS01-E2",
      "where": "§5.1，实机与模拟、480 项作业来源、对照",
      "url": "https://www.usenix.org/system/files/nsdi19-gu.pdf#page=10"
    },
    {
      "id": "LS01-E3",
      "where": "§5.2.1–5.2.3，图 10–11、表 4",
      "url": "https://www.usenix.org/system/files/nsdi19-gu.pdf#page=11"
    },
    {
      "id": "LS01-E4",
      "where": "§5.3–5.4，基于原始轨迹的模拟与参数影响",
      "url": "https://www.usenix.org/system/files/nsdi19-gu.pdf#page=12"
    }
  ],
  "unresolved_questions": [
    "给定本地作业和存储后，保存与重启代价多大，是否改变重排收益？",
    "长作业防饥饿与用户给定截止期之间如何区分？本研究未提供统一结果。"
  ]
}
```
