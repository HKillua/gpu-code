# PowerSlider · 时变供电上限下的在线推理

2026-11-05，来源 S03，阶段 `analyze-paper`。上游为论文 v1。[上一轮检索笔记](../../surveys/gpu_power_scheduling_2026/gpu_systems_notes.md) 用于定位原文。

**这篇研究帮我们回答“谁来让出功率”。** 当机房可用瓦数下降时，慢一点处理输入，可能直接拖长用户等到回答的时间；某些生成 token 的步骤受内存带宽限制，降低 GPU 核心频率时速度变化较小。作者按输入、思考和输出三个阶段分别调度，并允许部分用户在约定范围内多等一会儿。要理解这个差别，可先看同一模型在不同批量下的频率—吞吐曲线。[§III-B，图4；§IV，图6](https://arxiv.org/html/2608.21719v1#S3.SS2)

**一个可定位的数值。** 表述“降低三成功率后有多少在线请求达标”时，须带上作者采用的 128 卡仿真和突发到达设置。图7给出完整系统 78.3%、最佳基线 47.6%。基线的分池方式、服务合同与功率策略均有区别；这个差距适用于整套配置。动态回放在摘要和图10使用了尚待解释的两组结果，下方包分别保留。[§VI 与 §VII-A](https://arxiv.org/html/2608.21719v1#S7.SS1) · [摘要与图10原页](https://arxiv.org/pdf/2608.21719v1#page=10)

**从来源能推多远。** 论文已经考虑换池时怎样安置在途请求，以及供电恢复不充分时如何观察积压。这为“供电上限变化期间，按请求紧急程度调节 GPU”提供了直接近邻。对于下一次上限何时变化、预测出错会怎样，以及两机六卡的实际控制效果，仍须分开取证。本轮核验的是预印本所报告的研究。[§IV-D 和 §VII-E](https://arxiv.org/html/2608.21719v1#S7.SS5) · [作者机构书目](https://www.microsoft.com/en-us/research/publication/powerslider-exploiting-phase-asymmetry-for-llm-serving-under-demand-response/)

## ANALYSIS_PACKAGE

SC 为作者主张，DE 为在文献中定位的观察或程序，AI 为本分析的推断，UN 为待核事项。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-003-v01",
  "analysis_id": "AN-20261105-003-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "S03"
  ],
  "source_identity_and_version": {
    "title": "PowerSlider: Exploiting Phase Asymmetry for LLM Serving under Demand Response",
    "authors": [
      "Yueying Li",
      "Jiayang Chen",
      "Yuanfan Chen",
      "Leo Han",
      "Haoran Qiu",
      "Esha Choukse",
      "Rodrigo Fonseca",
      "Udit Gupta"
    ],
    "affiliations": [
      "Cornell University",
      "Microsoft Azure Research",
      "Cornell Tech"
    ],
    "edition": "arXiv:2608.21719v1, 2026-08-22",
    "publication_status": "预印本；arXiv 和微软研究院条目已核对。正式会议或期刊版本待确认。",
    "doi": "10.48550/arXiv.2608.21719",
    "evidence": [
      "E0"
    ]
  },
  "research_question": "外部下调允许的集群功率期间，如何安排各类 LLM 推理请求，使更多请求达到各自延迟目标？",
  "study_type_and_context": "工程系统研究，结合硬件特性测量、小规模原型和经校准的离散事件仿真。",
  "design_units_samples_or_materials": {
    "DE-设计": "原型使用 DGX-A100 和 GH200，报告过 8 卡回放校准；64–512 卡结果来自改造的 SplitwiseSim。测试含 Magpie-Reasoning、S1K 及 Azure 编程/聊天请求轨迹。",
    "evidence": [
      "E4"
    ]
  },
  "intervention_input_or_procedure": {
    "DE-输入": "按当前决策时刻接收上限；固定降幅扫描与按 CAISO 净负荷派生的时变曲线分别用于评估。",
    "SC-调度动作": "PSOpt 在上限内选择各阶段的 GPU 数量、频率及 KV 缓存预留粒度。PSSched 调频并在排空或迁移在途状态后换池。PSRoute 根据观察到的输出长度控制新请求准入。",
    "SC-合同": "LC 要求低延迟，Flex 允许约定幅度和比例的延迟放宽，BE 可延至 24 小时内完成。定义细节见限制。",
    "evidence": [
      "E1",
      "E2",
      "E3"
    ]
  },
  "measurements_and_analysis": {
    "DE-主要指标": "分类达标率（goodput）、首个可见回答 token 的时间 TTFAT、末 token 的时间 TTLT、后台吞吐和功率轨迹。",
    "DE-方法": "按组求解连续松弛后映射到离散配置；式(1)给改变 GPU 分配加代价。§VI 的尾延迟只统计稳定窗口中成功完成的请求。",
    "evidence": [
      "E2",
      "E4"
    ]
  },
  "results_with_uncertainty": [
    {
      "label": "DE",
      "setting": "Fig.7 上半部，128 GPU 仿真、推理类负载、60 请求/秒、到达变异系数 13、比额定功率低 30%。",
      "metric": "图示 online goodput，包含 LC 与 Flex。",
      "system_pct": 78.3,
      "comparison": "五个基线中结果最高的 SLOs-Serve+，47.6%。",
      "uncertainty": "原文该处未提供跨种子区间或独立运行数。",
      "evidence": "E5"
    },
    {
      "label": "UN",
      "finding": "同一 v1，摘要的 24 小时案例给出平均 92%、谷底 54%，Fig.10 图注和 §VII-E 却给出最低 60 秒窗口 98%。两者如何对应待原始分组、配置和统计窗口核对。",
      "evidence": "E6"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "SC": "作者认为不同推理阶段收回等量功率的延迟代价不同。",
      "DE": "Fig.4 量出 A100 上 CodeLlama-34B 在不同 batch 和频率下的性能曲线。",
      "AI": "现象有具体硬件与负载条件；系统比较同时改变了分池、服务合同和功率分配。",
      "UN": "六张 3090 上的方向和幅度。",
      "evidence": [
        "E1",
        "E4",
        "E5"
      ]
    },
    {
      "SC": "系统能追踪变化的功率上限并维持服务。",
      "DE": "§V 说明 NVML 最终限功率及换池动作；Fig.10–11 给出事件日曲线。",
      "AI": "作者给出了该问题的直接实现说明。给定回放的结果与真实机房接受指令并完成响应是两类证据。",
      "UN": "离线复现、真实执行的瞬时越限分布、动态回放统计歧义。",
      "evidence": [
        "E3",
        "E6"
      ]
    }
  ],
  "limitations_biases_and_ethics": {
    "DE": "主实验的基线接受不同服务合同，§VII-D 拆除组件比较其在完整系统内的影响。Fig.10 的尾段有观测截尾。",
    "AI": "仅凭端到端差值无法单独识别调频的贡献。Definition 1 文字对超出基础阈值的时段设限；式子却限制超出放宽阈值的概率。即使始终略慢于基础目标，也可能满足所列公式。从图11推断实际用户承诺需要先确定统计口径。",
    "UN": "缺少事件完成后恢复全部后台进度的完整观测、图表级原始记录与配置。流量重放未单独验证真实用户对 Flex 的接受程度。",
    "evidence": [
      "E2",
      "E4",
      "E5",
      "E6"
    ]
  },
  "reproducibility_and_transferability": {
    "reproducibility_grade": "unknown",
    "boundary": "本轮核对正文、附录、元数据与作者机构页。PowerSlider 专用实现的公开版本、逐请求日志和复现脚本待核。",
    "AI": "可以复用“给定该时刻瓦数，比较不同服务阶段代价”的问题表述。RTX 3090 的功率曲线、联网代价与可执行动作均须本机核验。"
  },
  "candidate_P": [
    {
      "id": "S03-P01",
      "tag": "AI",
      "text": "供电方改变功率上限时，如何决定该由哪个推理阶段和哪类请求承担减速？",
      "evidence": [
        "E1",
        "E2"
      ]
    }
  ],
  "candidate_F": [
    {
      "id": "S03-F01",
      "tag": "DE",
      "text": "在 Fig.4 对应模型/设备，某些小批量设置的吞吐曲线在较高频率区趋平，大批量设置仍随频率提高而上升。",
      "evidence": [
        "E1"
      ]
    },
    {
      "id": "S03-F02",
      "tag": "DE",
      "text": "同一 Fig.7 工况下，完整系统与最佳对照的在线达标比例不同，见主要结果；仅作为该仿真工况的观测。",
      "evidence": [
        "E5"
      ]
    }
  ],
  "evidence_index": {
    "E0": "arXiv v1 元数据；PDF p.1 作者栏；Microsoft Research 出版页。",
    "E1": "PDF p.1 Fig.1；§III-B Fig.4；§III-C；§IV 架构图6。",
    "E2": "§IV-B Definition 1，PDF p.5；§IV-C Eq.1；§IV-D 与 Table III。",
    "E3": "§V Algorithm 1、排空与功率上限说明；§VII-F 与 §VII-H 讨论。",
    "E4": "§VI，8卡/5000次请求校准段落、稳定窗口、工作负载与五个基线；附录 D Tables IV–V。",
    "E5": "§VII-A Fig.7 上半图与随后 reasoning 段，PDF p.8；§VII-D Fig.9。",
    "E6": "摘要 PDF p.1 与 §VII-E Fig.10 PDF p.10；Fig.11；附录 F。"
  },
  "evidence_locations": [
    "https://arxiv.org/abs/2608.21719v1",
    "https://arxiv.org/html/2608.21719v1",
    "https://arxiv.org/pdf/2608.21719v1",
    "https://www.microsoft.com/en-us/research/publication/powerslider-exploiting-phase-asymmetry-for-llm-serving-under-demand-response/"
  ],
  "unresolved_questions": [
    {
      "tag": "UN",
      "text": "动态回放两组数字使用了哪些不同的实验条件和分母？"
    },
    {
      "tag": "UN",
      "text": "Flex 实现如何计算超基础阈值的时间与被拒绝的请求？"
    },
    {
      "tag": "UN",
      "text": "系统已有排空、切换惩罚和积压讨论。未来供电上限预测及其误差对跨事件决策的作用，本次在该论文中未见独立评估。"
    }
  ],
  "evidence_status": "partial",
  "missing_evidence": [
    "专用代码及版本、原始日志、独立重复次数与误差范围、动态回放两组数字的对应关系、Flex 合同实际统计口径。"
  ],
  "next_skill": "deep-read Mode C"
}
```
