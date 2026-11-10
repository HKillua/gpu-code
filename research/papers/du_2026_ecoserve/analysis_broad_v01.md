# EcoServe｜正文广读分析包

AN-20261105-046-v01 · RNS06 · 2026-11-05 批次；2026-11-06 完成 · `partial`

阶段：`analyze-paper`；上游为已归档的正式PDF和来源初筛记录。下游：`deep-read Mode C`。本次没有新算法、baseline选择、选题决定或实验；台账由主汇总统一维护。

Efficient LLM Serving on Commodity GPU Clusters with Data-Reduced Cross-Instance Orchestration

USENIX OSDI 2026 正式会议 PDF；含封面，正文印刷页1787–1802，非2025年较早arXiv稿。 [官方来源](https://www.usenix.org/conference/osdi26/presentation/du) · [本地全文](paper.pdf)

## 问题与方法

普通PCIe/Ethernet集群中，prefill/decode互相干扰，但完全分离两阶段又需要传KV状态。怎样在较弱互联上维持SLO并让服务容量随负载改变？

SC（来源陈述）：PaDG在每个实例内按时间分离prefill/decode，在macro instance内部循环激活prefill，KV留在原实例。路由综合预估prefill时间、decode累积的saved TPOT和内存；容量按平均TTFT和slack调整，并等待已有请求排空后缩容。通过序列化InstanceHandler改变实例所属macro scheduler，实现逻辑分组迁移，不复制正在执行的模型/KV状态。

## 本轮读到的证据

DE指原文直接可定位的作者测量/评估证据，不表示本项目重新测量。

- **RNS06-E4**：指定严格SLO对照中99%请求达标下吞吐42→18请求/秒。 条件：这是CodeLlama/A800/ShareGPT的一组条件；99%是请求达标比例，不是吞吐的99分位数；无从推广为所有模型固定变化。
- **RNS06-E5**：作者展示32→64L20的动态请求率实验；本地存储重启一个CodeLlama实例约3分钟。 条件：此计时说明重新初始化的代价；逻辑分组操作避免它，不代表可在任意物理GPU迁移或瞬时增加冷实例。未看到完整冷启动/缩容尾延迟分解。
- **RNS06-E6**：高速互联上部分比较优势缩小且有baseline更好。 条件：效果取决于网络、模型KV形态和SLO；被省略的不可达SLO组合需保留为比较范围，不能只汇总最大倍数。

§4.1主测试床8节点64张L20-48GB、PCIe+10Gbps Ethernet；另2节点16张A800-80GB/25Gbps RoCE，及2节点16张H100-80GB/NVLink/每GPU一条400Gbps IB。BF16模型为Llama-30B、CodeLlama2-34B、Qwen2-72B，Alpaca/ShareGPT/LongBench。动态实验32→64张L20，不应把32写成主测试床总规模。

## 局限与可借鉴之处

作者明确的条件：

- §5：PaDG适合受限网络/有可借时间余量；小模型的prefill干扰可很小，强互联、巨大模型或严格SLO可能更适合其他部署方式。
- §4.2：部分baseline无法满足SLO或模型条件而省略；DistServe实现为未持续维护的原型，版本背景影响解释。
- §4.2：Llama-30B在LongBench中的序列截断至2048，不能把所有长输入结论都理解为原完整数据。

分析者推断（AI）和未知（UN）：

- AI：算法1使用平均saved TPOT和profile估计，不能只凭这条检查宣布每个请求有硬deadline保证；评估是经验SLO达标比例。
- AI：logical migration保留物理运行实例，不能代表GPU退出、断电或跨机状态搬运的可用动作。
- UN：动态实验的请求级尾部、真实冷启动与物理迁移分解未核查；代码、环境和原始日志未审计。
- AI：实例数增加时更多算力被占用；论文没有测量该动作是否符合时变电力上限，也没有证明节能。

与供电主题的关系：间接：涉及推理阶段时序、通信和动态实例管理；没有外部功率预算输入，不能称为供电约束调度。

学习区分“重分组控制关系”和“迁移模型/KV”，以及请求达标率与吞吐之间的条件关系。六卡并不限制阅读此机制，但是否可部署取决于模型、显存和链路实况。

## 阅读覆盖与下一入口

实际阅读本地PDF第5–14页：§3方法/算法/SLO定义/迁移，§4设置、主评估、严格SLO、动态伸缩与消融，§5条件讨论。未阅读完整实现或请求级原始日志；正文定点广读。

未补齐项：未审计代码、请求级日志、重复置信区间和完整冷启动数据。；未建立电力预算到实例数或调度动作的映射。；部分模型/基线不可达SLO组合被省略；尚未以各基线最新实现核查，当前包只分析论文版本。

Idea线索仅作待核验问题，不作为创新结论：

- 已有方法明确避免KV跨实例搬运并借decode时间余量；值得继续理解的是SLO定义如何决定可借时间，而非把slack视为无代价资源。
- 低开销逻辑重组和真正物理缩容/冷启动是不同动作；供电变化语境下必须先确认哪种动作实际释放资源。

## ANALYSIS_PACKAGE

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-046-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "RNS06"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    {
      "evidence_id": "RNS06-E1",
      "local_pdf": "research/papers/du_2026_ecoserve/paper.pdf",
      "location": "§3.1–3.3，图6、算法1，PDF pp5–8（印刷pp1790–1793）"
    },
    {
      "evidence_id": "RNS06-E2",
      "local_pdf": "research/papers/du_2026_ecoserve/paper.pdf",
      "location": "§3.4，PDF pp8–9（印刷pp1793–1794）"
    },
    {
      "evidence_id": "RNS06-E3",
      "local_pdf": "research/papers/du_2026_ecoserve/paper.pdf",
      "location": "§4.1，表4，PDF p9（印刷p1794）"
    },
    {
      "evidence_id": "RNS06-E4",
      "local_pdf": "research/papers/du_2026_ecoserve/paper.pdf",
      "location": "§4.3，PDF p11（印刷p1796）"
    },
    {
      "evidence_id": "RNS06-E5",
      "local_pdf": "research/papers/du_2026_ecoserve/paper.pdf",
      "location": "§4.5，PDF p12（印刷p1797）"
    },
    {
      "evidence_id": "RNS06-E6",
      "local_pdf": "research/papers/du_2026_ecoserve/paper.pdf",
      "location": "§4.2、§5，PDF pp10、14（印刷pp1795、1799）"
    }
  ],
  "missing_evidence": [
    "未审计代码、请求级日志、重复置信区间和完整冷启动数据。",
    "未建立电力预算到实例数或调度动作的映射。",
    "部分模型/基线不可达SLO组合被省略；尚未以各基线最新实现核查，当前包只分析论文版本。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-046-v01",
  "source_identity_and_version": {
    "title": "Efficient LLM Serving on Commodity GPU Clusters with Data-Reduced Cross-Instance Orchestration",
    "authors": [
      "Jiangsu Du",
      "Hongbin Zhang",
      "Taosheng Wei",
      "Zhenyi Zheng",
      "Jiazhi Jiang",
      "Kaiyi Wu",
      "Zhiguang Chen",
      "Yutong Lu"
    ],
    "year": 2026,
    "venue": "OSDI 2026",
    "official_url": "https://www.usenix.org/conference/osdi26/presentation/du",
    "pdf_url": "https://www.usenix.org/system/files/osdi26-du.pdf",
    "version": "USENIX OSDI 2026 正式会议 PDF；含封面，正文印刷页1787–1802，非2025年较早arXiv稿。",
    "local_pdf": "research/papers/du_2026_ecoserve/paper.pdf",
    "pdf_pages": 17,
    "reading_coverage": "实际阅读本地PDF第5–14页：§3方法/算法/SLO定义/迁移，§4设置、主评估、严格SLO、动态伸缩与消融，§5条件讨论。未阅读完整实现或请求级原始日志；正文定点广读。"
  },
  "research_question": "普通PCIe/Ethernet集群中，prefill/decode互相干扰，但完全分离两阶段又需要传KV状态。怎样在较弱互联上维持SLO并让服务容量随负载改变？",
  "study_type_and_context": "LLM推理系统论文：三个硬件测试床、模型/数据组合的SLO吞吐比较、突发/严格SLO/并行度/动态扩缩容及消融。该文不是电力系统控制，也不是训练恢复实验。",
  "design_units_samples_or_materials": "§4.1主测试床8节点64张L20-48GB、PCIe+10Gbps Ethernet；另2节点16张A800-80GB/25Gbps RoCE，及2节点16张H100-80GB/NVLink/每GPU一条400Gbps IB。BF16模型为Llama-30B、CodeLlama2-34B、Qwen2-72B，Alpaca/ShareGPT/LongBench。动态实验32→64张L20，不应把32写成主测试床总规模。",
  "intervention_input_or_procedure": "PaDG在每个实例内按时间分离prefill/decode，在macro instance内部循环激活prefill，KV留在原实例。路由综合预估prefill时间、decode累积的saved TPOT和内存；容量按平均TTFT和slack调整，并等待已有请求排空后缩容。通过序列化InstanceHandler改变实例所属macro scheduler，实现逻辑分组迁移，不复制正在执行的模型/KV状态。",
  "measurements_and_analysis": "主要指标为达到给定请求SLO比例时支持的请求率；按逐渐提高负载直到未达标测量。TTFT在本文包含真正首token时间与prefill→decode切换等待，TPOT在切换后开始算。Poisson默认到达，另Gamma突发。基线vLLM/Sarathi/DistServe/Mooncake对齐vLLM0.7.3，部分无法满足设置的组合被省略。",
  "results_with_uncertainty": [
    {
      "evidence_type": "DE",
      "claim": "指定严格SLO对照中99%请求达标下吞吐42→18请求/秒。",
      "evidence_ids": [
        "RNS06-E4"
      ],
      "uncertainty_and_boundary": "这是CodeLlama/A800/ShareGPT的一组条件；99%是请求达标比例，不是吞吐的99分位数；无从推广为所有模型固定变化。"
    },
    {
      "evidence_type": "DE",
      "claim": "作者展示32→64L20的动态请求率实验；本地存储重启一个CodeLlama实例约3分钟。",
      "evidence_ids": [
        "RNS06-E5"
      ],
      "uncertainty_and_boundary": "此计时说明重新初始化的代价；逻辑分组操作避免它，不代表可在任意物理GPU迁移或瞬时增加冷实例。未看到完整冷启动/缩容尾延迟分解。"
    },
    {
      "evidence_type": "DE",
      "claim": "高速互联上部分比较优势缩小且有baseline更好。",
      "evidence_ids": [
        "RNS06-E6"
      ],
      "uncertainty_and_boundary": "效果取决于网络、模型KV形态和SLO；被省略的不可达SLO组合需保留为比较范围，不能只汇总最大倍数。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "evidence_type": "SC",
      "claim": "PaDG在每个实例内按时间分离prefill/decode，在macro instance内部循环激活prefill，KV留在原实例。路由综合预估prefill时间、decode累积的saved TPOT和内存；容量按平均TTFT和slack调整，并等待已有请求排空后缩容。通过序列化InstanceHandler改变实例所属macro scheduler，实现逻辑分组迁移，不复制正在执行的模型/KV状态。",
      "evidence_ids": [
        "RNS06-E1",
        "RNS06-E2"
      ],
      "implementation_review": "仅据正文；未执行或审计代码。"
    },
    {
      "evidence_type": "DE",
      "claim": "作者的评估结果与其条件见results_with_uncertainty和证据索引。",
      "evidence_ids": [
        "RNS06-E1",
        "RNS06-E2",
        "RNS06-E3",
        "RNS06-E4",
        "RNS06-E5",
        "RNS06-E6"
      ],
      "implementation_review": "原文直接证据不等于本项目独立实测。"
    },
    {
      "evidence_type": "AI",
      "claim": "间接：涉及推理阶段时序、通信和动态实例管理；没有外部功率预算输入，不能称为供电约束调度。",
      "evidence_ids": [
        "RNS06-E1",
        "RNS06-E2",
        "RNS06-E3",
        "RNS06-E4",
        "RNS06-E5",
        "RNS06-E6"
      ],
      "implementation_review": "本项目阅读推断，不追加论文外效果。"
    }
  ],
  "limitations_biases_and_ethics": {
    "author_stated": [
      "§5：PaDG适合受限网络/有可借时间余量；小模型的prefill干扰可很小，强互联、巨大模型或严格SLO可能更适合其他部署方式。",
      "§4.2：部分baseline无法满足SLO或模型条件而省略；DistServe实现为未持续维护的原型，版本背景影响解释。",
      "§4.2：Llama-30B在LongBench中的序列截断至2048，不能把所有长输入结论都理解为原完整数据。"
    ],
    "analyst_inference_and_unknown": [
      "AI：算法1使用平均saved TPOT和profile估计，不能只凭这条检查宣布每个请求有硬deadline保证；评估是经验SLO达标比例。",
      "AI：logical migration保留物理运行实例，不能代表GPU退出、断电或跨机状态搬运的可用动作。",
      "UN：动态实验的请求级尾部、真实冷启动与物理迁移分解未核查；代码、环境和原始日志未审计。",
      "AI：实例数增加时更多算力被占用；论文没有测量该动作是否符合时变电力上限，也没有证明节能。"
    ]
  },
  "reproducibility_and_transferability": {
    "status": "未复现；未运行实验",
    "accessed": "research/papers/du_2026_ecoserve/paper.pdf",
    "not_accessed": "本轮未审计实现代码、原始日志或独立重复结果；未访问不等于材料不存在。",
    "hardware_boundary": "学习区分“重分组控制关系”和“迁移模型/KV”，以及请求达标率与吞吐之间的条件关系。六卡并不限制阅读此机制，但是否可部署取决于模型、显存和链路实况。",
    "transfer_stage": "当前仅analyze-paper。尚未形成Mode C或TRANSFER_PACKAGE，不声明迁移有效。"
  },
  "candidate_P": [
    {
      "id": "RNS06-P1",
      "text": "在推理资源需要改变时，如何区分可借的请求时延余量、控制关系重组与实际GPU状态搬运？",
      "evidence_ids": [
        "RNS06-E1",
        "RNS06-E2",
        "RNS06-E3",
        "RNS06-E4",
        "RNS06-E5",
        "RNS06-E6"
      ],
      "status": "来源支持的候选问题，待Mode C核验"
    }
  ],
  "candidate_F": [
    {
      "id": "RNS06-F1",
      "text": "PaDG对SLO与互联条件敏感；实例逻辑迁移避免初始化，但严格SLO会显著压低可支持请求率。",
      "evidence_ids": [
        "RNS06-E1",
        "RNS06-E2",
        "RNS06-E3",
        "RNS06-E4",
        "RNS06-E5",
        "RNS06-E6"
      ],
      "status": "来源条件内现象候选，不能外推到本项目"
    }
  ],
  "evidence_index": [
    {
      "id": "RNS06-E1",
      "evidence_type": "SC",
      "location": "§3.1–3.3，图6、算法1，PDF pp5–8（印刷pp1790–1793）",
      "observation": "PaDG把prefill与decode在实例内按时间分开并循环激活；算法检查预计prefill时间、当前decode请求的平均saved TPOT和显存；本文TTFT显式包含阶段切换等待。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "RNS06-E2",
      "evidence_type": "SC",
      "location": "§3.4，PDF pp8–9（印刷pp1793–1794）",
      "observation": "扩缩以运行时平均TTFT/slack触发；缩容先drain；Flexible Instance Migration转移InstanceHandler/消息队列入口等元数据，改变macro scheduler的控制权，不物理迁移模型或KV。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "RNS06-E3",
      "evidence_type": "DE",
      "location": "§4.1，表4，PDF p9（印刷p1794）",
      "observation": "主测试床64L20，另16A800和16H100；模型、三数据集、不同SLO与Poisson/Gamma到达。各基线的运行版本和配置条件有明确范围。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "RNS06-E4",
      "evidence_type": "DE",
      "location": "§4.3，PDF p11（印刷p1796）",
      "observation": "CodeLlama-34B/A800/ShareGPT案例中TTFT/TPOT从5秒/100毫秒收紧至1秒/50毫秒时，99%达标比例下吞吐42→18请求/秒（下降57.1%）。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "RNS06-E5",
      "evidence_type": "DE",
      "location": "§4.5，PDF p12（印刷p1797）",
      "observation": "CodeLlama-34B、TP4、ShareGPT、L20动态负载从20增加至50RPS，32→64GPU并重新分macro groups；本地存储重新初始化一个实例约3分钟。逻辑迁移避免已有实例重新初始化。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    },
    {
      "id": "RNS06-E6",
      "evidence_type": "DE",
      "location": "§4.2、§5，PDF pp10、14（印刷pp1795、1799）",
      "observation": "作者说明高带宽H100条件下收益缩小，特定CodeLlama案例Mooncake更好；小模型可适合不分离，极严格SLO/大模型可能需要完整分离及高速网络；部分基线达不到SLO的组合不展示。",
      "provenance": "本地正式 PDF；原文证据，未独立复现"
    }
  ],
  "unresolved_questions": [
    "请求级saved slack的分布与均值判断如何对应尾部SLO失败？",
    "若物理GPU必须释放，drain、冷启动和状态处置各需多少时间？论文逻辑分组实验不足以给出。"
  ],
  "batch_date": "2026-11-05",
  "completed_on": "2026-11-06"
}
```
