# DEFT：运行时联合调度的近邻边界

NN-D · AN-20261110-002-v01 · analyze-paper · partial

分析日期：2026-11-10，Asia/Shanghai。使用项目保存的 [analyze-paper 技能](../../standards/sources/2026-11-05/skills/analyze-paper/SKILL.md)。本轮仅为 Mode C 补充反重复边界，不提出新方法，不判定新颖性，不产生本项目实测现象。

**身份与版本。** Jing Chen、Miquel Pericas，*DEFT: Joint Task Placement and DVFS for Energy-Efficient Multi-GPU Runtimes*。[作者单位正式记录](https://research.chalmers.se/en/publication/553401) 确认 ICS 2026，pp.1115–1127，DOI `10.1145/3797905.3807847`。实际阅读为 [arXiv v1 在线 HTML](https://arxiv.org/html/2608.02122v1)，方法与评估 §3–6 定点核验；没有本地 PDF，未对读出版社终版，代码位置未知。

**已覆盖的机制。** 本文联合处理任务放置、DVFS、数据搬运和运行期就绪任务争用；一次应用画像用于后续执行，另显式估计切频开销。因此这些概念不能直接作为本项目差异。本文不等同于 Kareus 的计算—通信内核时序搜索；本轮也未核出外部时变瓦特额度下的画像失效保证。后一项是阅读边界，不能写成“作者没有处理”的穷尽判断。

**当前交接范围。** 三组证据仅支持“已有方法覆盖什么、哪些条件需再核”。结果数值、统计不确定性、实现一致性与 3090 适配均未完整核查，不用于支持性能增益或本项目机制。

## ANALYSIS_PACKAGE

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "analysis_id": "AN-20261110-002-v01",
  "package_id": "AN-20261110-002-v01",
  "created_at": "2026-11-10",
  "timezone": "Asia/Shanghai",
  "source_ids": ["NN-D"],
  "evidence_status": "partial",
  "evidence_locations": [
    "NN-D-E01：arXiv v1 HTML §3.2–3.3，Eq.1–10",
    "NN-D-E02：arXiv v1 HTML §3.4，Table 1",
    "NN-D-E03：arXiv v1 HTML §4.4、§5–6"
  ],
  "source_identity_and_version": {
    "title": "DEFT: Joint Task Placement and DVFS for Energy-Efficient Multi-GPU Runtimes",
    "authors": ["Jing Chen", "Miquel Pericas"],
    "year": 2026,
    "venue": "ACM ICS 2026",
    "publication_status": "作者单位官方记录确认正式会议论文",
    "doi": "10.1145/3797905.3807847",
    "official_record": "https://research.chalmers.se/en/publication/553401",
    "read_version": "arXiv:2608.02122v1，在线HTML，2026-08-03",
    "read_url": "https://arxiv.org/html/2608.02122v1",
    "reading_coverage": "§3–6 方法、画像、模型、切频代价与评估设置定点阅读；非逐式或完整统计审计",
    "local_pdf": "未下载",
    "publisher_version_comparison": "UN：未对读出版社终版",
    "code_location": "UN：本轮未确认作者公开实现地址，不等于没有开源"
  },
  "research_question": "任务式多GPU运行时如何同时考虑放置、频率、通信与争用，控制能量和执行时间？",
  "study_type_and_context": {
    "type": "计算机系统方法设计与实机评估",
    "context": "CUDASTF任务DAG运行时",
    "scope_boundary": "外部时变硬功率额度不作为本轮已确认的问题设定；不是电气控制研究"
  },
  "design_units_samples_or_materials": {
    "DE": "评估使用L4/L40S及Cholesky、CG、FDTD、miniWeather等任务图应用；见NN-D-E03",
    "unknown": "全部规模、重复次数、统计聚合与原始测量尚未逐项核验；不据此外推两机六张3090"
  },
  "intervention_input_or_procedure": {
    "DE": "§3.2先选择可最早启动任务的设备，再按数据、slack、争用及切频成本选择频率；§3.3跟踪数据驻留状态；见NN-D-E01",
    "already_addressed": ["任务放置与DVFS联合考虑", "运行期就绪任务争用", "通信与切频开销"]
  },
  "measurements_and_analysis": {
    "DE": "§3.4以默认频率下的一次Nsight画像形成任务特征；§4.4测量切频延迟并纳入成本；见NN-D-E02/E03",
    "audit_boundary": "未审测量源码、原始轨迹或独立复算模型；不把采样设置等同于本机计量能力"
  },
  "results_with_uncertainty": {
    "SC": "作者报告能量及能量—时间目标收益",
    "adopted_numeric_results": "none：本轮不采用收益百分比",
    "uncertainty": "UN：未完整核查重复次数、误差区间和全部对照统计，不能用于本项目预期收益估计",
    "DE_evaluation_scope": "§6含均一频率HEFT-G与逐任务HEFT-PT等对照，见NN-D-E03"
  },
  "claim_evidence_implementation_map": [
    {
      "claim": "运行条件影响频率/放置决策",
      "evidence": "NN-D-E01：方法显式使用就绪任务相对GPU数量的争用条件",
      "implementation_status": "论文流程可定位；核心实现未访问，未核源码一致性"
    },
    {
      "claim": "一次画像可跨后续执行摊销",
      "evidence": "NN-D-E02：给出画像过程及适用限制",
      "implementation_status": "作者设计/陈述；本项目未复测摊销收益"
    },
    {
      "claim": "切频成本应进入调度",
      "evidence": "NN-D-E03：成本模型及评估对照",
      "implementation_status": "正文核验；未审控制接口调用链或原始数据"
    }
  ],
  "limitations_biases_and_ethics": {
    "source_limitations": "§3.4将输入相关任务行为和动态生成DAG留作未来工作，见NN-D-E02",
    "analysis_limits": [
      "本轮没有证明作者方法能或不能处理外部cap触发的实际限频",
      "两类原评估硬件不构成3090上的兼容性或机制证据",
      "正文定点核验不足以评价全部模型误差与基线公平性"
    ],
    "ethics_safety_privacy": "本轮仅阅读公开材料，无人类受试或私人数据操作；硬件控制未执行，未来执行需按仓库规则核验权限与恢复"
  },
  "reproducibility_and_transferability": {
    "reproducibility_level": "unknown",
    "missing_materials": "作者代码、原始日志与可运行环境未核",
    "usable_principle": "比较运行时方法时，应检查已覆盖的争用、数据搬运与切频成本",
    "requires_revalidation": "3090支持的频率动作、计量与Nsight能力、模型适配、任务条件及真实开销均需重验",
    "cross_domain_transfer": "不适用：本轮未选择外领域方法B，也未提出迁移方案"
  },
  "candidate_P": [
    {
      "id": "NN-D-P01",
      "type": "SC：来源问题/边界",
      "statement": "一次固定应用画像对输入相关任务行为或动态生成DAG的适用性尚不属于作者已完成范围",
      "evidence": "NN-D-E02",
      "downstream_use": "仅作为画像条件边界；不当作本项目失败已发生，也不据此推导新颖性"
    }
  ],
  "candidate_F": [],
  "evidence_index": [
    {
      "id": "NN-D-E01",
      "tag": "DE（方法文档）；运行效果仍为SC/未独立复验",
      "location": "arXiv v1 HTML §3.2–3.3，Eq.1–10",
      "content": "运行调度/争用：两阶段设备—频率选择、就绪任务争用约束与数据驻留跟踪"
    },
    {
      "id": "NN-D-E02",
      "tag": "DE（画像协议）；摊销收益未独立复验",
      "location": "arXiv v1 HTML §3.4，Table 1",
      "content": "一次画像与条件：默认频率Nsight特征；输入相关任务和动态生成DAG留待后续"
    },
    {
      "id": "NN-D-E03",
      "tag": "DE（成本模型/评估设置）；结果数字未完整核验",
      "location": "arXiv v1 HTML §4.4、§5–6",
      "content": "切频代价/评估范围：非对称切换延迟与能量成本，L4/L40S任务图评估，含均一及逐任务频率对照"
    }
  ],
  "unresolved_questions": [
    "作者实现地址和源码是否完整可访问？",
    "出版社终版与当前作者v1有何差异？",
    "外部功率限频时，任务特征和频率索引模型的有效边界是什么？",
    "全部结果的统计不确定性、模型误差与重复性如何？"
  ],
  "missing_evidence": [
    "出版社终版对读与本地PDF",
    "作者源码、环境、原始日志和独立复现",
    "完整结果数字及不确定性审计",
    "外部时变硬功率额度覆盖与两机六3090适用性"
  ],
  "next_skill": "deep-read Mode C"
}
```
