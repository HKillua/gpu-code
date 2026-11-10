# ElasticFlow｜来源分析包

AN-20261105-018-v01 · ASPLOS 2023 正式会议论文 · 2026-11-05 · partial。

训练作业给定截止时间时，如何判断还能接多少作业，并选择随时间变化的 GPU 数量？ 用不同卡数下的吞吐预估计算最低可满足份额，做准入与边际收益分配，用 buddy 放置保持通信局部性。

**本轮读到的证据。** §6、图 6(b) 的 16 台/128 卡、195 作业对照中，按期完成的作业数是 EDF 的 7.65×、Chronus 的 1.62×，128 卡比较不含 Pollux。图 9 中仅加入准入或仅加入伸缩均弱于完整策略。倍数随基线和资源紧张程度变化，本次未核到图 6 的跨重复区间。

**与六张 3090 的联系。** 观察同一全局 batch 在不同卡数和放置上的吞吐变化，同时记录等待与重配置；每机三卡与论文的整齐分组有差别。

[正式发表](https://doi.org/10.1145/3575693.3575721) · [正式会议版正文](https://gudiandian.github.io/attaches/asplos23/asplosb23main-p360.pdf)

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-018-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LN02"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "https://doi.org/10.1145/3575693.3575721",
    "https://gudiandian.github.io/attaches/asplos23/asplosb23main-p360.pdf"
  ],
  "missing_evidence": [
    "已核定点正文；尚未核对完整代码、原始日志、独立重复结果与目标 GPU 环境。",
    "调度时变供电上限与 3090 上的因果关系仍待建立。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-018-v01",
  "source_identity_and_version": {
    "title": "ElasticFlow: An Elastic Serverless Training Platform for Distributed Deep Learning",
    "authors": [
      "Diandian Gu",
      "Yihao Zhao",
      "Yinmin Zhong",
      "Yifan Xiong",
      "Zhenhua Han",
      "Peng Cheng",
      "Fan Yang",
      "Gang Huang",
      "Xin Jin",
      "Xuanzhe Liu"
    ],
    "venue": "ASPLOS",
    "year": 2023,
    "version": "正式会议论文；本包按会议版页/节/图记录",
    "official_url": "https://doi.org/10.1145/3575693.3575721",
    "verification_url": "https://asplos-conference.org/asplos2023/index.html%3Fp=3602.html",
    "pdf_url": "https://gudiandian.github.io/attaches/asplos23/asplosb23main-p360.pdf"
  },
  "research_question": "训练作业给定截止时间时，如何判断还能接多少作业，并选择随时间变化的 GPU 数量？",
  "study_type_and_context": "用不同卡数下的吞吐预估计算最低可满足份额，做准入与边际收益分配，用 buddy 放置保持通信局部性。",
  "design_units_samples_or_materials": "Microsoft ITP 的 trace 记录提交时间、GPU 数和持续时间。作者补配模型、batch 与截止时间。真机使用 Azure ND A100 v4，32 卡的小测试和 128 卡的较大测试分开；后者使用 195 个作业，总体评估另含多条 trace 仿真。",
  "intervention_input_or_procedure": "根据不同卡数的吞吐曲线估算各作业满足截止时间的最低资源份额，结合已接纳作业做准入、按边际收益弹性分配，用 buddy 方式安排 worker。调整单 worker 本地 batch 维持全局 batch；重分配时传递模型参数、复用 CUDA 上下文及 NCCL 进程组。",
  "measurements_and_analysis": "按全部到达作业计算截止时间达标率；另看集群效率、混合负载中后台作业平均 JCT、扩缩与迁移耗时。图 9 按 EDF、EDF 加准入、EDF 加弹性与完整系统对照；完整测试床采用稳态迭代跳过。",
  "results_with_uncertainty": "§6、图 6(b) 的 16 台/128 卡、195 作业对照中，按期完成的作业数是 EDF 的 7.65×、Chronus 的 1.62×，128 卡比较不含 Pollux。图 9 中仅加入准入或仅加入伸缩均弱于完整策略。倍数随基线和资源紧张程度变化，本次未核到图 6 的跨重复区间。",
  "claim_evidence_implementation_map": [
    {
      "claim": "调度器对截止时间与非线性伸缩性做联合处理",
      "evidence_id": "LN02-E1",
      "boundary": "算法与接口可定位，依赖工作量及吞吐预测。",
      "implementation_review": "仅按正文核查，未执行工件。"
    },
    {
      "claim": "测试床更多作业在合成截止时间内完成",
      "evidence_id": "LN02-E2",
      "boundary": "真实机器和 trace 回放，截止时间是人为设置。",
      "implementation_review": "仅按正文核查，未执行工件。"
    },
    {
      "claim": "扩缩容会改变有效执行时间",
      "evidence_id": "LN02-E3",
      "boundary": "论文定义 suspend 到 restart 的间隔，并区分不同切换情景。",
      "implementation_review": "仅按正文核查，未执行工件。"
    }
  ],
  "limitations_biases_and_ethics": "trace 缺真实截止时间，作者设定为提交后 λ×原 duration，λ 在 [0.5,1.5] 均匀取值。某些基线无法按相同方式快进，因此比较范围因规模而异。作者机构页面对最低倍数的写法和正文略异，本包以正文图 6(b) 的逐基线值为准。业务 deadline 与电力约束需分别建模。伦理细节未专门审查。",
  "reproducibility_and_transferability": "正文附录 A 给出工件及测试床/仿真说明，未执行。用户六卡可以测不同放置对卡数—吞吐关系的影响，并记录重配期间丢失的有效时间。原 buddy 的硬件组形状、真实截止分布与 3090 曲线需要单独验证。",
  "candidate_P": [
    {
      "id": "LN02-P1",
      "text": "加入一个有截止时间的训练作业之前，哪些实测吞吐和放置条件决定资源份额是否足够？",
      "evidence_ids": [
        "LN02-E1",
        "LN02-E3"
      ],
      "status": "候选，待 Mode C 核查"
    }
  ],
  "candidate_F": [
    {
      "id": "LN02-F1",
      "text": "该组 trace 下，图 9 显示准入和弹性各自解释部分达标收益；两者同时使用时表现不同于只加一个的版本。",
      "evidence_ids": [
        "LN02-E2"
      ],
      "status": "来源内观察"
    }
  ],
  "evidence_index": [
    {
      "id": "LN02-E1",
      "location": "§3–4，图 4–5、§5 Implementation",
      "observation": "最低满足份额、准入、分配和放置，以及保持全局 batch。"
    },
    {
      "id": "LN02-E2",
      "location": "§6.1–6.2，图 6(b)；§6.4 图 9",
      "observation": "trace 合成 deadline、快进测试床、基线范围与消融。"
    },
    {
      "id": "LN02-E3",
      "location": "§6.6、图 12(b)",
      "observation": "五类扩缩/迁移动作及 suspend 到 restart 口径。"
    }
  ],
  "unresolved_questions": [
    "改变每 GPU 的功率限制后，不同放置的吞吐曲线如何变化？",
    "切换间隔若缩短，复用上下文能节省多少可执行时间？"
  ]
}
```
