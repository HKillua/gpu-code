# 读 Phoenix 现场试验：谁能让出功率，谁承担代价

研究阶段为 `analyze-paper`，来源编号 S02，核验日期为 2026-11-05（上海）。本次以 arXiv v1 为分析底本，读到方法、实现与局限，并抽查作者公开的 SRP 作业数据和控制代码。期刊版在线发表于 2025 年，编入 2026 年卷期。下文的章节和图号均指预印本。[预印本全文](https://arxiv.org/html/2507.00909v1) · [期刊版](https://doi.org/10.1038/s41560-025-01927-1)

**一次需求响应有两个验收方。** 电力合作方关心当地用电峰值时能否在指定时间降载，并受控地回到原功率。算力使用者关心任务在同一时间里还能完成多少工作。Phoenix 团队让 OCI 机房中 256 张 A100 同时运行多类作业，在两个当地峰值窗口实现了持续三小时的 25% 降载。该场景给出了可执行的需求和真实响应，接网时间、合同收益、全机房电表变化仍需各自的证据。[§2.2、图 2–3 与 §4.4](https://arxiv.org/html/2507.00909v1#S2.SS2)

**看任务标签比看“训练/推理”更准确。** 团队与合作方事先指定服务等级。Flex 0 保护原性能。Flex 1、2、3 分别允许在 3–6 小时窗口内平均吞吐下降至多 10%、25%、50%。表 1 中某些推理作业属 Flex 0，另一个代表性组合中的推理作业则分属 Flex 1–3。SRP 的公开文件里，一个 Flex 3 的 MPT-13B 预训练作业归一化表现为 0.666923。以 1 为参照，它少交付了约三分之一的吞吐，仍高于 0.50 门槛。这是“履约”如何包含可观性能代价的具体例子。[§2.1、§4.1 表 1](https://arxiv.org/html/2507.00909v1#S4.SS1) · [SRP 作业数据](https://github.com/ai-emerald/emerald-ai-demo-may-2025/blob/34403aef78c2690a8e4f0ba8f7f62d302cdba967/data/SRP_exp_performance.csv)

**恢复功率和恢复训练进度需要分别观察。** 现场验收包含回升斜坡和避免反弹的要求。暂停训练还涉及保存 checkpoint，让任务能从之前的状态继续。作者因需求响应发生较少、长训练可持续数日而在此场景忽略这笔开销。公开补丁确实实现了接收保存和停止请求的逻辑。每次存盘、重新加载和重新产生有效训练步所需的时间，仍是本来源留下的问题。[§3.3 与 §5](https://arxiv.org/html/2507.00909v1#S3.SS3) · [训练端补丁](https://github.com/ai-emerald/emerald-ai-demo-may-2025/blob/34403aef78c2690a8e4f0ba8f7f62d302cdba967/llm-foundry-d91c4beb-add-emerald.patch)

交接包中，SC 是来源主张，DE 是可定位的直接证据，AI 是本次分析推断，UN 是尚缺证据。状态 `partial` 反映现场日志与部分指标的缺口。本轮完成文献及工件的静态核验，独立复现仍待后续安排。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-002-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "S02"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "S02-E1",
    "S02-E2",
    "S02-E3",
    "S02-E4",
    "S02-E5",
    "S02-E6",
    "S02-E7"
  ],
  "missing_evidence": [
    "完整生产控制器、现场部署配置、仪器校准及逐次试验的端到端日志",
    "每次 checkpoint 写出、读取、重启及恢复到相同进度的耗时分布",
    "全部场站设施进线功率、客户分布、真实购买灵活服务的价格及多年兑现率",
    "独立重复样本的置信区间、在线请求尾延迟与模型最终质量对照"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-002-v01",
  "source_identity_and_version": {
    "id": "S02",
    "main": "Philip Colangelo 等，Turning AI Data Centers into Grid-Interactive Assets: Results from a Field Demonstration in Phoenix, Arizona，arXiv:2507.00909v1，2025-07-01。",
    "institutions": "预印本署名单位包括 Emerald AI、NVIDIA、Salt River Project 和 EPRI，场地和编排合作详见 §2.2。",
    "formal": "期刊版本以 AI data centres as grid-interactive assets 为题，Nature Energy，在线发表 2025-12-05，2026 年 2 月卷期，11:254–261。详细章节和图号引用预印本。",
    "artifacts": "ai-emerald/emerald-ai-demo-may-2025，快照 34403aef78c2690a8e4f0ba8f7f62d302cdba967。",
    "coverage": "从预印本摘要、引言核对到 §§2–5 的协议、结果、图表及局限。正式版核对书目和公开工件说明。补充仓库抽读 README、作业控制命令、LLM Foundry 补丁和 SRP 逐作业 CSV。未逐项核对期刊版修订、所有图源或全部运行轨迹，未独立复现。"
  },
  "research_question": "SC｜电力公司指定降载幅度、时长与恢复规则时，软件调度能否在真实 GPU 集群中兑现要求，同时满足每个作业事先设定的服务约定？E1–E2。",
  "study_type_and_context": "DE｜单个商业云机房的前瞻性工程现场试验。2025-05-01、05-03 分别对齐 APS 与 SRP 峰值，另在实机重演 2020 年 CAISO 应急场景。电网系统总负荷与受控 GPU 集群功率是两套量。E2。",
  "design_units_samples_or_materials": "DE｜256 张 A100，每节点八张。四种人工选择的预训练、微调与推理组合。作者报告 33 轮，每轮 3–6 小时，合计 212 个作业。事件、单个作业和 GPU 是不同分析单位。每轮独立随机抽样或盲法未见报告。E1–E2。",
  "intervention_input_or_procedure": "DE｜先测作业的功率—吞吐关系，结合电网负荷预测、功率目标及作业可退化档位，制定限功率、暂停或调整占卡数的计划。公开伪代码的事件输入显式包含起止时间、斜坡时长和目标功率。E1、E2、E4–E5。",
  "measurements_and_analysis": "DE｜GPU 功率由 nvidia-smi 取得，作业记录 steps/s 或 tokens/s。分别检查目标功率、作业服务等级与模拟功率预测误差。图 2 的电网负荷单位为 GW，GPU 集群功率单位为 kW；图 4 的 GPU 数据为五分钟均值。E2、E4。",
  "results_with_uncertainty": [
    {
      "label": "DE",
      "finding": "当地两次峰值演示均达到平均基线功率的 25% 降幅，维持三小时；上下调各预留 15 分钟，恢复受事前基线约束。",
      "comparison": "已定义的基线和目标曲线。",
      "uncertainty": "事件级描述。没有可用于估计未知时段成功率的独立重复区间。",
      "evidence": "S02-E2"
    },
    {
      "label": "DE",
      "finding": "SRP 六条作业记录之一：Flex 3 的 MPT-13B 预训练作业 Performance 为 0.666923，超过该档的 0.50 下限。",
      "comparison": "归一化值为 1 是全性能参照。",
      "uncertainty": "单个预训练作业的报告值；样本不支持对所有作业或最终训练质量的推断。",
      "evidence": "S02-E1、S02-E6"
    },
    {
      "label": "SC",
      "finding": "作者按其测试准则汇总为零次 SLA 违约，并报告模拟功率相对平均试验功率的 RMSE 为 4.52%。",
      "comparison": "预测与实测功率比较，作业表现与各自门槛比较。",
      "uncertainty": "RMSE 是预测误差指标。全部事件逐次失效率、p99 时延和仪器误差界未由本次公开材料核出。",
      "evidence": "S02-E2、S02-E4"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "SC": "在伙伴设定的峰值窗口兑现降载。",
      "DE": "图 2–3 及工件图源目录同时提供事件和作业计量入口。示例限电命令把瓦数写入 memcached，节点补丁将其传给 nvidia-smi -pl。",
      "AI": "在该集群和所选作业组合内，电力侧需求可以落实为软件控制动作。",
      "UN": "跨场地长期兑现比例。",
      "evidence": "S02-E2、S02-E5、S02-E7"
    },
    {
      "SC": "试验作业履行了其服务约定。",
      "DE": "表 1 标注不同作业的 Flex 档位，SRP CSV 可逐作业核对。",
      "AI": "允许的性能让步构成可调空间；观察到低于全速而达标的实例。",
      "UN": "生产客户愿意选择各档位的比例。",
      "evidence": "S02-E1、S02-E6"
    },
    {
      "SC": "较少发生的需求响应中，可忽略训练 checkpoint 开销。",
      "DE": "§3.3 给出时间尺度理由，补丁在训练批边界处理停机请求并支持按指令保存状态。",
      "AI": "动作能执行与其代价足够小是两个待分别检查的命题。",
      "UN": "反复暂停时保存、恢复、追赶积压各占多久。",
      "evidence": "S02-E3、S02-E7"
    }
  ],
  "limitations_biases_and_ethics": "DE｜专家伙伴参与选择作业组合和可退化档位，研究对象为单一设备代际及站点。AI｜这些设计条件影响向日常多租户业务的外推；供应商和合作伙伴参与执行，第三方独立核验有价值。UN｜本次未核实单独的伦理审查或涉及生产用户数据的处理声明；不据此推测有隐私事件。E1、E3。",
  "reproducibility_and_transferability": {
    "artifact_audit": "中。固定版本的部分图源和控制端/节点示例可检查，完整现场环境、所有对照和逐次运行材料仍有缺项。",
    "field_replication": "低。独立重现真实峰值事件和原规模依赖站点条件与合作方接口。没有运行代码或重新生成图表。",
    "transfer": "可借鉴“先声明服务窗口与计量边界”的读法。对两机六张 RTX 3090，应把每项功率—吞吐关系、状态恢复成本和测量边界视作待验证条件。"
  },
  "candidate_P": [
    {
      "id": "S02-P1",
      "label": "AI",
      "question": "当事件间隔缩短，训练状态保存和恢复占可运行时间的比例如何变化？",
      "basis": "作者只在低频事件设定中忽略这项开销。S02-E3、S02-E7。"
    },
    {
      "id": "S02-P2",
      "label": "AI",
      "question": "若严格服务作业占比改变，固定百分比降载还能否满足全部服务约定？",
      "basis": "表 1 对同类推理任务也赋予不同 Flex 标签。S02-E1。"
    }
  ],
  "candidate_F": [
    {
      "id": "S02-F1",
      "label": "DE",
      "observation": "同一类推理可在不同组合中有不同 Flex 档位。可调性取决于服务约定。",
      "basis": "S02-E1，比较表 1 的组合 2 与 3。"
    },
    {
      "id": "S02-F2",
      "label": "DE",
      "observation": "SRP 的 Flex 3 预训练作业归一化表现约 0.667，同时满足该档阈值。",
      "basis": "S02-E1、S02-E6。"
    }
  ],
  "evidence_index": [
    {
      "id": "S02-E1",
      "where": "arXiv v1，§§2.1、4.1，表 1，作业组合与 Flex 合约",
      "url": "https://arxiv.org/html/2507.00909v1#S4.SS1"
    },
    {
      "id": "S02-E2",
      "where": "arXiv v1，§§2.2–2.3，图 2–5，事件、基线与性能",
      "url": "https://arxiv.org/html/2507.00909v1#S2.SS2"
    },
    {
      "id": "S02-E3",
      "where": "arXiv v1，§§3.3–3.5，图 6，动作代价、假设与场地边界",
      "url": "https://arxiv.org/html/2507.00909v1#S3.SS3"
    },
    {
      "id": "S02-E4",
      "where": "arXiv v1，§§4.2–4.4、§5，策略、测量与实现",
      "url": "https://arxiv.org/html/2507.00909v1#S4.SS4"
    },
    {
      "id": "S02-E5",
      "where": "作者工件 README 的 Data、AI Orchestration Usage、Emerald AI Conductor Pseudocode",
      "url": "https://github.com/ai-emerald/emerald-ai-demo-may-2025/blob/34403aef78c2690a8e4f0ba8f7f62d302cdba967/README.md"
    },
    {
      "id": "S02-E6",
      "where": "data/SRP_exp_performance.csv 的 pt_mpt_13b_sm 记录及 Flexibility 列，图 2 图源",
      "url": "https://github.com/ai-emerald/emerald-ai-demo-may-2025/blob/34403aef78c2690a8e4f0ba8f7f62d302cdba967/data/SRP_exp_performance.csv"
    },
    {
      "id": "S02-E7",
      "where": "控制端的 checkpoint 与 dvfs_set_power_cap；补丁中的 EmeraldCallback.batch_end、save_interval_fn 和 control_power.py",
      "urls": [
        "https://github.com/ai-emerald/emerald-ai-demo-may-2025/blob/34403aef78c2690a8e4f0ba8f7f62d302cdba967/orchestrator_commands.py",
        "https://github.com/ai-emerald/emerald-ai-demo-may-2025/blob/34403aef78c2690a8e4f0ba8f7f62d302cdba967/llm-foundry-d91c4beb-add-emerald.patch"
      ]
    }
  ],
  "unresolved_questions": [
    "带严格时延合同的线上请求在更短时间尺度上的违约率是多少？",
    "每次恢复到第一个有效训练步所需时间与写出开销分别有多大？",
    "不同任务组合、硬件以及事件间隔会在什么范围内改变可兑现的功率？"
  ]
}
```
