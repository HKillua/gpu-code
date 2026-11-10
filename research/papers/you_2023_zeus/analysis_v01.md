# Zeus · USENIX NSDI 2023

Zeus: Understanding and Optimizing GPU Energy Consumption of DNN Training

LP01 · AN-20261105-005-v01 · 2026-11-05 · analyze-paper · partial

同一个训练作业可能执行很多次。Zeus 把每次重训当成一次学习机会，一边选批量，一边测 GPU 的功率档位，观察模型达到既定质量需要的时间和焦耳。 [正式记录](https://www.usenix.org/conference/nsdi23/presentation/you)

先读表1，看每项任务用什么指标算完成，再读图6。图中是末期配置的表现，正文还区分测量轨迹回放和真实的端到端运行。 [论文正文](https://www.usenix.org/system/files/nsdi23-you.pdf)

**阅读边界。** 此文适合回答训练进度如何计量。仅靠它无法推断两台服务器在外部限电时会如何同步。

## ANALYSIS_PACKAGE

SC 为作者主张，DE 为正文可定位的设计或观察，AI 为本组推断，UN 为未核验项。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-005-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LP01"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LP01-E1 正式会议页面、首页摘要",
    "LP01-E2 §4.1–4.3，特别是式(2)",
    "LP01-E3 §6.1 表1–2、§6.2 图6",
    "LP01-E4 §6.4 与 §6.6"
  ],
  "missing_evidence": [
    "UN：未核对全部曲线的原始重复样本、置信区间及对应工件版本。",
    "UN：两机三卡分别运行时的通信开销和功率控制能力未知。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-005-v01",
  "source_identity_and_version": {
    "title": "Zeus: Understanding and Optimizing GPU Energy Consumption of DNN Training",
    "authors": [
      "Jie You",
      "Jae-Won Chung",
      "Mosharaf Chowdhury"
    ],
    "year": 2023,
    "venue": "USENIX NSDI 2023",
    "pages": "119–139",
    "doi": null,
    "official_url": "https://www.usenix.org/conference/nsdi23/presentation/you",
    "pdf_url": "https://www.usenix.org/system/files/nsdi23-you.pdf",
    "publication_status": "正式发表"
  },
  "research_question": "反复训练相似模型时，到同一个质量目标要花多久、耗多少电？",
  "study_type_and_context": "面向周期性 DNN 训练的系统研究；批量决定训练动力学，功率上限影响每轮速度及耗电。",
  "design_units_samples_or_materials": {
    "DE": "六种模型–任务组合和各自的目标质量见表1；设备表2含 A40、V100、RTX6000 和 P100。主体分析单 GPU，另测单机四卡。",
    "evidence": "LP01-E3，LP01-E4"
  },
  "intervention_input_or_procedure": {
    "SC": "在周期性作业之间用多臂老虎机探索批量并裁剪失败配置；训练中短时间测不同功率上限下的功耗和吞吐。每轮在同一质量目标下选择时间–能耗折中。",
    "evidence": "LP01-E2"
  },
  "measurements_and_analysis": {
    "DE": "§6.1 把实测收敛轮数与配置功率、吞吐轨迹结合回放；§6.4 数据漂移另做端到端训练。比较默认配置和带剪枝的网格搜索。",
    "evidence": "LP01-E3，LP01-E4"
  },
  "results_with_uncertainty": [
    {
      "label": "DE",
      "setting": "§6.2 图6；用每种任务最后五次训练衡量稳定选择，相对于该任务默认配置归一化。",
      "observation": "各任务的到目标能耗下降，耗时变化因任务而异。图6的末期结果与初次使用时的探索成本对应不同阶段。",
      "uncertainty": "结果取决于目标质量、可选批量和探索历史。没有在此提取可用于跨硬件推断的置信区间。",
      "evidence": "LP01-E3"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "claim": "SC：按训练轮次选择批量，在一次训练里短时扫描 GPU 功率限制，按时间与能耗的加权成本更新选择。",
      "support": [
        "LP01-E2",
        "LP01-E3"
      ],
      "implementation_status": "核对论文报告的方法及关键评估段落；本轮未运行作者代码或独立复测。"
    }
  ],
  "limitations_biases_and_ethics": {
    "AI": "目标质量是预设阈值，达到阈值无法替代任意下游质量保证。多数结果用测量轨迹重放，跨机网络场景需另行验证。",
    "ethics": "本轮聚焦测量边界，未独立审计数据许可。",
    "evidence": "LP01-E3，LP01-E4"
  },
  "reproducibility_and_transferability": {
    "DE": "USENIX 提供正式论文，作者公开 Zeus 仓库。",
    "AI-六卡": "可以先读懂单卡的训练质量、耗时和能耗口径。单机多卡另有小节，作者将涉及网络通信的跨机扩展列为后续工作。",
    "UN": "当前仓库精确重现 2023 版本所需提交及驱动组合尚未核验。",
    "artifact_url": "https://github.com/ml-energy/zeus"
  },
  "candidate_P": [
    {
      "label": "AI",
      "text": "为什么只看每秒样本数会错选批量或功率档位？",
      "evidence": [
        "LP01-E2",
        "LP01-E3"
      ]
    }
  ],
  "candidate_F": [
    {
      "label": "DE",
      "text": "在相同任务质量阈值下，图6中不同作业的到目标耗时随配置变化的方向并不一致。",
      "evidence": [
        "LP01-E3"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "LP01-E1",
      "locator": "正式会议页面、首页摘要",
      "detail": "作者、版本、研究对象。",
      "url": "https://www.usenix.org/conference/nsdi23/presentation/you"
    },
    {
      "id": "LP01-E2",
      "locator": "§4.1–4.3，特别是式(2)",
      "detail": "加权成本、JIT 画像、跨次批量探索。",
      "url": "https://www.usenix.org/system/files/nsdi23-you.pdf"
    },
    {
      "id": "LP01-E3",
      "locator": "§6.1 表1–2、§6.2 图6",
      "detail": "硬件、任务目标、回放来源和比较口径。",
      "url": "https://www.usenix.org/system/files/nsdi23-you.pdf"
    },
    {
      "id": "LP01-E4",
      "locator": "§6.4 与 §6.6",
      "detail": "漂移的端到端评估、单机多卡以及作者列明的跨机边界。",
      "url": "https://www.usenix.org/system/files/nsdi23-you.pdf"
    }
  ],
  "unresolved_questions": [
    "UN：未核对全部曲线的原始重复样本、置信区间及对应工件版本。",
    "UN：两机三卡分别运行时的通信开销和功率控制能力未知。"
  ]
}
```
