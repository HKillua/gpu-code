# TAPAS：问题、方法与证据边界

LP05 · AN-20261105-031-v01 · analyze-paper · partial

本轮始于2026-11-05，完成于2026-11-06（Asia/Shanghai）；保留本轮ID。SC=作者主张，DE=论文可定位的设计/观察，AI=分析推断，UN=待核验。DE不代表本项目复测。页码均为本地PDF查看器页序。

[本地PDF](paper.pdf) · [正式记录](https://doi.org/10.1145/3676641.3716025)

**问题。** 云平台既有不可见的租户 GPU 负载，又有可调整的托管 LLM 服务；怎样避免局部电力、空气流量和 GPU 温度越限，并在设施能力下降时保留服务？

**核心方法（SC/DE）。** 历史画像预测 VM 功率和温度，先放置 VM，再过滤有越限风险的目的地并路由请求；最后调节实例频率、批量、张量并行度或模型配置。对透明的 SaaS 先调整，对不透明 IaaS 的硬件功率限制作为末级动作。

**电力与服务口径。** 直接考虑行级供电、冷却和服务器温度限制及故障后的额度下降；历史预测对象主要是负载/功率/温度。本文所读评估不能解释为任意时变外部供电序列上的严格功率保证。 常态 goodput 是满足 TTFT 与 TBT 的 token/s，§3.3 采用相对空载执行时间的 SLO。应急评估另用频率限幅的性能代理及转向较小模型的估计质量损失；两类服务口径不能互换。

**版本与已读范围。** 本地为作者公开的ASPLOS 2025会议排版稿，首页含DOI与正式页码；正式身份复用此前官方核验。 本轮读摘要及PDF pp.5–13：背景机制、§3画像、§4方法、§5关键评估与适用限制；参考文献、全部附录和代码未逐项审计。

**关键证据。**

- LP05-E01，PDF pp.5–8，§2.2–3.3，图11–15：GPU配置的功率与温度作用不同；减小TP可降低总功率但提高最热GPU温度，小decode批量也可能增加内存温度。
- LP05-E02，PDF pp.9–11，§4.1–4.6：历史画像放置、约束过滤路由、实例重配置、设施故障处理及泛化条件；重配置期间目标实例不收新请求，模型重载需要时间。
- LP05-E03，PDF pp.11–12，§5.1–5.3，图19–23：80台服务器一小时硬件回放与千台级轨迹仿真分开；可控SaaS占比影响可用动作及收益。
- LP05-E04，PDF pp.12–13，§5.4，表2：供电/冷却应急场景单独评估；作者明确无法直接测IaaS性能，以限频比例做代理，质量按模型替换估计。

**作者自述限制。**

- §4.1：GPU IaaS VM不支持这里所需的在线迁移；可控SaaS可通过新建、转移和撤销迁移。
- §4.6：更换工作负载需重训配置控制器；改变机房布局/散热系统需更新回归模型。
- §5.4：IaaS真实性能难直接获取，所以采用频率限幅代理。

**分析者判断与未知。**

- AI：可调SaaS比例、候选落点及模型替换权限决定空间调度余地；不应将本机两节点场景等同于其机房。
- AI：常态一小时回放、仿真、应急代理评估应分列；未看到跨多个下降/恢复事件逐请求统计可直接支持真实恢复期服务保证。
- UN：未审代码、原始轨迹、完整重配置状态机与置信区间；未独立复现。
- UN：估计质量与真实业务质量之间的对应关系尚未独立核验。

**已有处理，不能直接当创新。** 历史功率/温度预测与不确定时的保守画像；请求路由与VM放置的多层协调；硬件限频前的SaaS多动作调节；设施故障后的额度重算；重配置停收请求、模型重载时间及SaaS迁移。

**候选问题，仅供Mode C继续核验。**

- AI：应急表格的性能代理与逐请求服务/质量之间，在哪些事件与服务组成下会出现明显偏离？此处是计量问题线索，尚无本项目失败证据。
- AI：当可路由余量和可调整SaaS比例降低时，重配置前后的等待损失是否改变动作排序？论文已考虑重配置成本，需先核对其覆盖范围。

**阅读价值。** 最直接连接供电/热约束与在线服务，适合先厘清设施约束、控制权限和服务代理指标。 可借鉴把设施指标、请求指标及动作权限分层；原云规模不能因六卡无法原样复现而从阅读中排除，落地时另核对可测机制。

## ANALYSIS_PACKAGE

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-031-v01",
  "created_at": "2026-11-06",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LP05"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LP05-E01 PDF pp.5–8，§2.2–3.3，图11–15",
    "LP05-E02 PDF pp.9–11，§4.1–4.6",
    "LP05-E03 PDF pp.11–12，§5.1–5.3，图19–23",
    "LP05-E04 PDF pp.12–13，§5.4，表2"
  ],
  "missing_evidence": [
    "未审代码、原始轨迹、完整重配置状态机与置信区间；未独立复现。",
    "估计质量与真实业务质量之间的对应关系尚未独立核验。"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-031-v01",
  "source_identity_and_version": {
    "title": "TAPAS: Thermal- and Power-Aware Scheduling for LLM Inference in Cloud Platforms",
    "authors": [
      "Jovan Stojkovic",
      "Chaojie Zhang",
      "Íñigo Goiri",
      "Esha Choukse",
      "Haoran Qiu",
      "Rodrigo Fonseca",
      "Josep Torrellas",
      "Ricardo Bianchini"
    ],
    "year": 2025,
    "venue": "ACM ASPLOS 2025, Volume 2",
    "doi": "10.1145/3676641.3716025",
    "official_url": "https://doi.org/10.1145/3676641.3716025",
    "pdf_url": "https://iacomaweb.web.engr.illinois.edu/iacoma-papers/asplos25_2.pdf",
    "publication_status": "正式发表",
    "version": "本地为作者公开的ASPLOS 2025会议排版稿，首页含DOI与正式页码；正式身份复用此前官方核验。",
    "local_pdf": "research/papers/stojkovic_2025_tapas/paper.pdf"
  },
  "research_question": "云平台既有不可见的租户 GPU 负载，又有可调整的托管 LLM 服务；怎样避免局部电力、空气流量和 GPU 温度越限，并在设施能力下降时保留服务？",
  "study_type_and_context": {
    "design": "生产数据分析、真实服务器回放、回归预测与离散时间仿真相结合。",
    "power_signal_and_objective": "直接考虑行级供电、冷却和服务器温度限制及故障后的额度下降；历史预测对象主要是负载/功率/温度。本文所读评估不能解释为任意时变外部供电序列上的严格功率保证。",
    "service_or_quality_contract": "常态 goodput 是满足 TTFT 与 TBT 的 token/s，§3.3 采用相对空载执行时间的 SLO。应急评估另用频率限幅的性能代理及转向较小模型的估计质量损失；两类服务口径不能互换。"
  },
  "design_units_samples_or_materials": {
    "DE": "§5.1：一周生产轨迹覆盖约1000台服务器、IaaS/SaaS各半及10个SaaS端点；硬件实验用80台A100服务器模拟两行并运行一小时，IaaS只回放历史功率，SaaS实际执行Llama2请求。大规模部分使用仿真。",
    "reading_coverage": "本轮读摘要及PDF pp.5–13：背景机制、§3画像、§4方法、§5关键评估与适用限制；参考文献、全部附录和代码未逐项审计。"
  },
  "intervention_input_or_procedure": {
    "SC": "历史画像预测 VM 功率和温度，先放置 VM，再过滤有越限风险的目的地并路由请求；最后调节实例频率、批量、张量并行度或模型配置。对透明的 SaaS 先调整，对不透明 IaaS 的硬件功率限制作为末级动作。",
    "already_addressed": [
      "历史功率/温度预测与不确定时的保守画像",
      "请求路由与VM放置的多层协调",
      "硬件限频前的SaaS多动作调节",
      "设施故障后的额度重算",
      "重配置停收请求、模型重载时间及SaaS迁移"
    ],
    "implementation_audit": "本轮读论文流程，未审代码或独立执行。"
  },
  "measurements_and_analysis": {
    "DE": "§2–4分开服务器/行功率、GPU温度、空气流量与服务指标；§5.2峰值图的采样粒度与§5.4应急代理指标均限制解释。表2的Perf是受影响负载加权的相对频率限制，Quality是小模型路由带来的估计质量损失，并非全部用户请求的直接测量。"
  },
  "results_with_uncertainty": [
    {
      "label": "DE",
      "setting": "§3.3，配置扫描，图13–15",
      "observation": "功率与最热部件温度对同一并行度/批量动作可呈不同方向的变化。",
      "uncertainty": "这是论文设备和模型的观察；本轮未取得原始测量或复测。",
      "evidence": "LP05-E01"
    },
    {
      "label": "DE",
      "setting": "§5.4，供电或冷却设施能力下降，表2",
      "observation": "论文把应急期的服务损失显式纳入讨论，但性能和质量主要是上述代理口径。",
      "uncertainty": "不能由表2推导真实端到端延迟、逐请求质量或恢复期积压已获保证。",
      "evidence": "LP05-E04"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "claim": "SC：历史画像预测 VM 功率和温度，先放置 VM，再过滤有越限风险的目的地并路由请求；最后调节实例频率、批量、张量并行度或模型配置。对透明的 SaaS 先调整，对不透明 IaaS 的硬件功率限制作为末级动作。",
      "direct_evidence": [
        "LP05-E01",
        "LP05-E02",
        "LP05-E03",
        "LP05-E04"
      ],
      "implementation": "UN：未审代码；所读方法和评估支持限定范围内的论文主张。",
      "boundary": "直接考虑行级供电、冷却和服务器温度限制及故障后的额度下降；历史预测对象主要是负载/功率/温度。本文所读评估不能解释为任意时变外部供电序列上的严格功率保证。"
    }
  ],
  "limitations_biases_and_ethics": {
    "source_stated": [
      "§4.1：GPU IaaS VM不支持这里所需的在线迁移；可控SaaS可通过新建、转移和撤销迁移。",
      "§4.6：更换工作负载需重训配置控制器；改变机房布局/散热系统需更新回归模型。",
      "§5.4：IaaS真实性能难直接获取，所以采用频率限幅代理。"
    ],
    "analyst_inference": [
      "可调SaaS比例、候选落点及模型替换权限决定空间调度余地；不应将本机两节点场景等同于其机房。",
      "常态一小时回放、仿真、应急代理评估应分列；未看到跨多个下降/恢复事件逐请求统计可直接支持真实恢复期服务保证。"
    ],
    "unknown": [
      "未审代码、原始轨迹、完整重配置状态机与置信区间；未独立复现。",
      "估计质量与真实业务质量之间的对应关系尚未独立核验。"
    ],
    "ethics": "本轮未独立核验生产轨迹/请求的隐私协议；未复制用户请求或运行业务数据。"
  },
  "reproducibility_and_transferability": {
    "reproducibility_grade": "unknown：未审完整工件/数据，未复现；未知不等于低。",
    "principle": "可借鉴把设施指标、请求指标及动作权限分层；原云规模不能因六卡无法原样复现而从阅读中排除，落地时另核对可测机制。",
    "conditions_to_recheck": [
      "可调SaaS比例、候选落点及模型替换权限决定空间调度余地；不应将本机两节点场景等同于其机房。",
      "常态一小时回放、仿真、应急代理评估应分列；未看到跨多个下降/恢复事件逐请求统计可直接支持真实恢复期服务保证。"
    ],
    "cross_domain_transfer": "不适用：只形成来源阅读线索，未选择源方法B。"
  },
  "candidate_P": [
    {
      "label": "AI",
      "text": "应急表格的性能代理与逐请求服务/质量之间，在哪些事件与服务组成下会出现明显偏离？此处是计量问题线索，尚无本项目失败证据。",
      "support": [
        "LP05-E01",
        "LP05-E02",
        "LP05-E03",
        "LP05-E04"
      ],
      "status": "候选，待Mode C及最近邻核验，不是新颖性结论。"
    },
    {
      "label": "AI",
      "text": "当可路由余量和可调整SaaS比例降低时，重配置前后的等待损失是否改变动作排序？论文已考虑重配置成本，需先核对其覆盖范围。",
      "support": [
        "LP05-E01",
        "LP05-E02",
        "LP05-E03",
        "LP05-E04"
      ],
      "status": "候选，待Mode C及最近邻核验，不是新颖性结论。"
    }
  ],
  "candidate_F": [
    {
      "label": "DE",
      "text": "功率与最热部件温度对同一并行度/批量动作可呈不同方向的变化。",
      "setting": "§3.3，配置扫描，图13–15",
      "evidence": "LP05-E01",
      "uncertainty": "这是论文设备和模型的观察；本轮未取得原始测量或复测。"
    },
    {
      "label": "DE",
      "text": "论文把应急期的服务损失显式纳入讨论，但性能和质量主要是上述代理口径。",
      "setting": "§5.4，供电或冷却设施能力下降，表2",
      "evidence": "LP05-E04",
      "uncertainty": "不能由表2推导真实端到端延迟、逐请求质量或恢复期积压已获保证。"
    }
  ],
  "evidence_index": [
    {
      "id": "LP05-E01",
      "locator": "PDF pp.5–8，§2.2–3.3，图11–15",
      "label": "DE",
      "detail": "GPU配置的功率与温度作用不同；减小TP可降低总功率但提高最热GPU温度，小decode批量也可能增加内存温度。",
      "local_pdf": "paper.pdf",
      "url": "https://iacomaweb.web.engr.illinois.edu/iacoma-papers/asplos25_2.pdf"
    },
    {
      "id": "LP05-E02",
      "locator": "PDF pp.9–11，§4.1–4.6",
      "label": "DE",
      "detail": "历史画像放置、约束过滤路由、实例重配置、设施故障处理及泛化条件；重配置期间目标实例不收新请求，模型重载需要时间。",
      "local_pdf": "paper.pdf",
      "url": "https://iacomaweb.web.engr.illinois.edu/iacoma-papers/asplos25_2.pdf"
    },
    {
      "id": "LP05-E03",
      "locator": "PDF pp.11–12，§5.1–5.3，图19–23",
      "label": "DE",
      "detail": "80台服务器一小时硬件回放与千台级轨迹仿真分开；可控SaaS占比影响可用动作及收益。",
      "local_pdf": "paper.pdf",
      "url": "https://iacomaweb.web.engr.illinois.edu/iacoma-papers/asplos25_2.pdf"
    },
    {
      "id": "LP05-E04",
      "locator": "PDF pp.12–13，§5.4，表2",
      "label": "DE",
      "detail": "供电/冷却应急场景单独评估；作者明确无法直接测IaaS性能，以限频比例做代理，质量按模型替换估计。",
      "local_pdf": "paper.pdf",
      "url": "https://iacomaweb.web.engr.illinois.edu/iacoma-papers/asplos25_2.pdf"
    }
  ],
  "unresolved_questions": [
    "未审代码、原始轨迹、完整重配置状态机与置信区间；未独立复现。",
    "估计质量与真实业务质量之间的对应关系尚未独立核验。",
    "应急表格的性能代理与逐请求服务/质量之间，在哪些事件与服务组成下会出现明显偏离？此处是计量问题线索，尚无本项目失败证据。",
    "当可路由余量和可调整SaaS比例降低时，重配置前后的等待损失是否改变动作排序？论文已考虑重配置成本，需先核对其覆盖范围。"
  ]
}
```
