# Themis｜把共享的代价量给每个用户看

Kshiteej Mahajan 等，NSDI 2020。本次定位阅读§3.1–3.3、§4.2–4.3、§6.1–6.3，重点表 1、图 9–12，阶段为 `analyze-paper`，来源 LS02。下文的实测指作者报告的结果。 [正式书目](https://www.usenix.org/conference/nsdi20/presentation/mahajan) · [会议全文](https://www.usenix.org/system/files/nsdi20-paper-mahajan.pdf)

两份训练都拿到两张 GPU，其中一份跨机同步，另一份留在同机。卡数分配一样，实际进度可以不同。Themis 进一步把等待也算进去，问每个应用共享后相对自己的公平独占份额多花了多久。 [方法](https://www.usenix.org/system/files/nsdi20-paper-mahajan.pdf#page=5)

§6.2 的两组实机负载中，Themis 相比对照把最差应用的完成时间比值缩小了 2.2–3.25 倍，改善的是最差者承担的共享代价。 [评估设置](https://www.usenix.org/system/files/nsdi20-paper-mahajan.pdf#page=10) · [实验结果](https://www.usenix.org/system/files/nsdi20-paper-mahajan.pdf#page=12)

两作业各用两卡时，可区分同机与跨机放置。要解释公平，先说明独占参照、等待是否计入以及放置是否相同。在外部限功率时，可借其参照定义辨认哪个应用承受额外延误；原指标本身没有供电信号或硬截止期。 本轮未核对代码或做独立测量。

SC 表示作者主张，DE 表示可定位的正文证据，AI 表示本次迁移推断，UN 表示待核验项。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-012-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LS02"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LS02-E1",
    "LS02-E2",
    "LS02-E3",
    "LS02-E4"
  ],
  "missing_evidence": [
    "未核对拍卖求解实现、损失曲线日志或每项应用原始运行时间，未独立复现。",
    "缺少外部功率信号、变化预算下的独享参考和六卡上的拓扑测量。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-012-v01",
  "source_identity_and_version": {
    "id": "LS02",
    "title": "Themis: Fair and Efficient GPU Cluster Scheduling",
    "authors": [
      "Kshiteej Mahajan",
      "Arjun Balasubramanian",
      "Arjun Singhvi",
      "Shivaram Venkataraman",
      "Aditya Akella",
      "Amar Phanishayee",
      "Shuchi Chawla"
    ],
    "venue": "NSDI 2020",
    "pages": "289–304",
    "official_url": "https://www.usenix.org/conference/nsdi20/presentation/mahajan",
    "pdf_url": "https://www.usenix.org/system/files/nsdi20-paper-mahajan.pdf",
    "material": "正式会议版本，正文图表和节号依 USENIX 公开 PDF，链接页码从封面算起。",
    "coverage": "§3.1–3.3、§4.2–4.3、§6.1–6.3，重点表 1、图 9–12。仅查正式论文及书目，本轮未核对代码、数据工件或执行机器。"
  },
  "research_question": "SC｜共享 GPU 的用户获得相同卡数，为什么完成时间仍可能相差很远？什么才是让用户愿意共享的公平定义？",
  "study_type_and_context": "DE｜在线 GPU 集群调度原型与轨迹驱动模拟。公平的分析单位是 ML 应用，一个应用可以包含多个训练作业。实机和大规模模拟分开展示。",
  "design_units_samples_or_materials": "DE｜Azure 20 台共 64 张 K80，其中八台各两张、十二台各四张。实机比较两组不同的工作负载，§6.2 设置公平参数 f=0.8、10 分钟租约。默认 256 GPU 模拟预先给定作业损失曲线估计。",
  "intervention_input_or_procedure": "DE｜以应用在共享集群中的完成时间除以其独享公平份额时的完成时间。应用估算不同放置的价值，集群按轮次拍卖资源和发放租约。",
  "measurements_and_analysis": "DE｜r=T_shared/T_independent。分子为共享集群中的应用完成时间，包含排队与放置代价，分母为应用独占 1/N 集群时的完成时间。论文还观察 r 的分布与最大值、完成整组工作所耗的总 GPU 时间。",
  "results_with_uncertainty": [
    {
      "label": "DE",
      "finding": "§6.2 中，Themis 对应的最大 r 约为所比较基线最大 r 的 1/2.2 至 1/3.25。",
      "comparison_and_uncertainty": "64 卡实机上的两组负载，比较量是最差应用的比值。未查到该范围的统计置信区间，范围来自不同对照。",
      "evidence": "LS02-E4"
    },
    {
      "label": "DE",
      "finding": "表 1 描述放置敏感性因模型而异，图 5 用构造的放置例子说明同卡数仍可能损害某些训练。",
      "comparison_and_uncertainty": "图 5 的说明性反例与 §6.2 实验在证据类型上不同。",
      "evidence": "LS02-E1"
    }
  ],
  "claim_evidence_implementation_map": [
    "SC｜长期完成时间比值能体现平均分卡难以表达的放置与排队代价。",
    "DE｜§3.2 给出比值与准入条件，§3.3 及 §4 说明应用报告资源估值。图 9–10 汇报测试床的最大 r 与整体分布。",
    "AI｜若服务口径改为业务截止期，应另给按期完成指标；公平的参照仍需明确。",
    "UN｜在上限随时间变化时如何确定独占参照与估值误差，本文未给出供电实验。"
  ],
  "limitations_biases_and_ethics": [
    "等待和拓扑都能改变分子，独占份额如何建立参照会改变解释。§3.2 的 r≤1 共享激励条件假设有准入机制。",
    "模拟步骤借助先验损失曲线，原测试床与当前两节点在 GPU 型号和布局上不同。",
    "公平是应用间资源分配结果，可用于问谁承担延误，无法直接代表生产合同中对租户的责任。"
  ],
  "reproducibility_and_transferability": {
    "evidence": "论文报告的配置和结果可定位。代码与结果的一致性尚未独立复核，本次不构成复现。",
    "transferable": "两作业各用两卡时，可区分同机与跨机放置。要解释公平，先说明独占参照、等待是否计入以及放置是否相同。",
    "boundary": "间接。在外部限功率时，可借其参照定义辨认哪个应用承受额外延误；原指标本身没有供电信号或硬截止期。"
  },
  "candidate_P": [
    {
      "id": "LS02-P1",
      "status": "AI｜候选，待 Mode C 核验",
      "statement": "共享容量变化时，如何定义适当的独占份额，使应用完成时间比值有可比较的分母？",
      "evidence": [
        "LS02-E1",
        "LS02-E2",
        "LS02-E3",
        "LS02-E4"
      ]
    }
  ],
  "candidate_F": [
    {
      "id": "LS02-F1",
      "status": "DE｜来源观察，待 Mode C 核验",
      "statement": "同一集群同样数量的卡可对应不同放置和等待，论文的最差应用完成时间比值对调度政策敏感。",
      "evidence": [
        "LS02-E1",
        "LS02-E4"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "LS02-E1",
      "where": "§3.1–3.2，表 1、图 5、式 r 与准入脚注",
      "url": "https://www.usenix.org/system/files/nsdi20-paper-mahajan.pdf#page=5"
    },
    {
      "id": "LS02-E2",
      "where": "§3.3、§4.2–4.3，拍卖、应用估值与租约",
      "url": "https://www.usenix.org/system/files/nsdi20-paper-mahajan.pdf#page=6"
    },
    {
      "id": "LS02-E3",
      "where": "§6.1，64 卡试验与 256 卡模拟、两组负载",
      "url": "https://www.usenix.org/system/files/nsdi20-paper-mahajan.pdf#page=10"
    },
    {
      "id": "LS02-E4",
      "where": "§6.2，图 9–12，测试床公平分布与 GPU 时间",
      "url": "https://www.usenix.org/system/files/nsdi20-paper-mahajan.pdf#page=12"
    }
  ],
  "unresolved_questions": [
    "两台各三卡时，同机与跨机对不同任务的影响有多大？",
    "如果政策目标改为紧急任务的截止期，参照分母与最长可接受延误该由谁定义？"
  ]
}
```
