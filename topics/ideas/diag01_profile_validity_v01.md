# 诊断Idea：已有频率画像是否足以指导受限GPU执行

IDEA-DIAG01-v01 · 2026-11-10 · diagnostic / partial。所属计算机系统、GPU执行与通信；功率上限是外部约束。问题设计已形成，硬件门槛未满足，尚不可运行；不是最终选题或baseline决定。

先给已有方法充分机会：允许旧画像按实际频率重索引，再判断是否真有选择损失。只证明“降频改变最优重叠”会重复已有工作。

```json
{
  "package_type": "IDEA_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "IDEA-DIAG01-v01",
  "idea_id": "IDEA-DIAG01-v01",
  "created_at": "2026-11-10",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "MC-20261110-001-v01",
    "C01"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "../../research/matrices/power_constrained_gpu_scheduling/mode_c_20261110_profile_v01.md",
    "../../research/papers/wu_2026_kareus/analysis_constraints_v01.md",
    "../../research/papers/chen_2026_deft/analysis_v01.md"
  ],
  "missing_evidence": [
    "3090功率/锁频权限、传感器有效刷新、拓扑及空闲设备",
    "原工件对power cap状态的缓存、重索引、重画像触发完整实现",
    "已有画像在cap生效后的误差和有限候选选择损失",
    "全领域最近邻尚未穷尽；无独立复验"
  ],
  "next_skill": "record-experiment",
  "actual_next_stage": "目标服务器只读能力核验→冻结可执行配置/实验目录→实际执行→记录→深挖→Mode C回流",
  "source_matrix_id": "MC-20261110-001-v01",
  "selected_C_id": "C01",
  "source_P_ids": [
    "P01",
    "P02"
  ],
  "source_F_ids": [
    "F01",
    "F02",
    "F03"
  ],
  "source_M_ids": [
    "M01",
    "M02"
  ],
  "source_E_ids": [
    "LP06-E05",
    "LP06-E06",
    "LP06-E07",
    "LP06-E08",
    "RNS02-E2",
    "LN08-E2",
    "LN08-E6",
    "LP06-E09",
    "NN-D-E01",
    "NN-D-E02",
    "NN-D-E03"
  ],
  "pattern": "3 假设挑战",
  "status": "diagnostic",
  "plan_version": "v01：可归因设计；运行参数待硬件核验后另冻结，尚不可下发",
  "research_question": "固定GPU工作量与通信实现时，外部cap实际触发限频后，已有多频率画像经实际频率重索引，是否仍足以选择通信启动时序？",
  "hypothesis_and_mechanism": {
    "H0": "已有画像经可实现的频率重索引即可解释时序排序与性能；无需新状态变量/调度器。",
    "H1": "cap下频率与执行阶段的对应关系使原画像不足，导致重复可见的配置选择损失。",
    "mechanism_status": "H1是待检验解释，不是已经发生的事实；若只有绝对误差而无决策损失不进入新调度设计。"
  },
  "testable_predictions": [
    "cap实际生效后，旧画像按实测平均频率重新查询仍可能与独立验证的候选优劣不一致；H0允许其一致。",
    "若只是温度、频率覆盖不足或通信实现变化引起误差，补齐这些因素后差异应消失。",
    "新状态画像带来的候选改选只有超过测量噪声且覆盖画像代价才有进一步研究价值。"
  ],
  "design_and_controls": {
    "unit": "同一套合法计算/通信分区，最多同机2张3090；固定输入张量、精度、计算量、通信字节和正确性检查。",
    "candidate_set": "预定三个合法时序：顺序执行、最早合法通信启动、计算中段合法启动；同一kernel/collective，不改SM份额。若依赖结构不允许三种安排则停止并修订协议，不伪造重叠。",
    "profile": "先在p0下独立扫频建立旧画像：每张GPU使用2–3个实测支持的频率点，覆盖两卡的二维组合及每个合法时序。以各GPU实际稳定频率而非请求值建索引；查询仅限测得网格内部，不外插。此有限候选画像是诊断对照，不冒称Kareus完整原实现。",
    "intervention": "主配对实验只改变同一GPU组的cap：原设置p0与实际生效的较低p1。两条件保持完全相同的请求频率、锁频/控制模式、内存频率设置及软件路径；前述画像扫频是独立校准阶段。cap×时序是显式交互对照，不把跨时序差异全归因于cap。",
    "comparators": {
      "A": "原条件的固定时序，仅作弱参照",
      "B": "对每个候选时序s，用独立校准块测得的逐GPU时间加权平均频率向量(f0_s,f1_s)查询旧画像的预测完成时间，选择预测最小的s；不先把两张GPU频率平均成一个数。",
      "B_information": "B只读校准块的频率，不使用验证块的完成时间选择配置。B/C共享相同校准探测安排并单列其成本；完整验证窗事后频率查询只作有额外信息的解释性参照，不称在线策略。",
      "C": "在新cap下用同一候选集合的校准块选时序，再在独立验证块评价；有限集合经验参照，不称全局最优",
      "negative": "p0下重复执行及p0→p0伪切换，识别热漂移/测量顺序影响"
    },
    "matching": "所有执行比较相同cap、相同输入、精度、候选集合、后端与占用资源；学习/探测成本单列，事后信息不混入在线收益。",
    "scope": "不实现新调度器、不复现Kareus全系统；这里的比较策略是机制诊断对照，尚未选论文baseline。",
    "indexing_and_validation": "旧画像对每个时序在两卡频率网格内作双线性插值，精确网格点直接取校准均值。另在p0下用未参与拟合的内部频率点验证插值，误差须在计量预检不确定性内且低于5%筛选阈值。任一候选的查询越界、网格不能保持稳定、插值不合格或频率缺测，则本轮unknown；不把粗网格误差当H1。不在看过p1验证结果后更换插值器。",
    "tie_break": "B/C若预测值或校准平均完成时间相同，均按预先列出的候选顺序选第一个；验证结果不得反向影响选配置。"
  },
  "sample_materials_or_experimental_units": {
    "initial": "1种固定工作量的双nanobatch合法分区；1组同机两卡。只有机制确实存在，再另版扩到第二工作量/跨机。",
    "replicates": "计划10个独立配对块，每块含所有候选×cap；前5块选配置/建立校准，后5块只验证。块内顺序随机并记录种子，固定选用种子20261110；块而非循环迭代作为重复单位。",
    "measurement_window": "能量窗口由传感器刷新/噪声预检决定并冻结；候选在窗内重复，时间用同步后的端到端边界，保留实际频率、温度、功率与通信时间线。",
    "independence": "相邻块可能共享热状态，需记录/匹配起始状态；5个验证块只支持诊断，区间宽则inconclusive。"
  },
  "primary_outcome_and_estimand": {
    "metric": "同一固定有效工作量下，B相对C的完成时间差异率 (T_B−T_C)/T_C，按独立验证块配对汇总",
    "decision_relevance": "比较选出的时序是否一致；仅单kernel误差而无选择损失不成立为调度问题",
    "reference_boundary": "C在同一有限候选集内由校准块选出，可能并非验证集最快；允许负差，不强制裁成0。"
  },
  "secondary_outcomes": [
    "同等工作量的GPU能量及时间—能量前沿；不换算机房收益",
    "旧画像预测误差和候选排序、实际平均频率及可测阶段频率",
    "探测/画像/切换的时间、GPU能量与主机成本",
    "GPU cap实际响应、越限窗口（按可测时间粒度）、温度、错误与未完成工作",
    "N次重复使用的累计净收益与实测画像摊销，N是场景假设不是电网事件事实"
  ],
  "analysis_plan_and_uncertainty": {
    "precommit": "把5%完成时间差异作为本诊断的实用筛选阈值，不声称行业标准；同时报告原始差异及配对95%区间。",
    "statistics": "验证块为单位的配对差异与原始散点/范围；块级bootstrap给描述性区间，5块不足以提供稳健普遍推断；不把窗内迭代当独立样本。",
    "frequency_match": "校准频率不在画像覆盖范围或记录粒度不足时，频率重索引的机制解释标unknown；不能把遥测缺失当H1证据。",
    "no_fishing": "不增加模型/随机种子直到出现显著；若预检噪声≥5%或区间无法区分，记录inconclusive并另拟计量方案。",
    "selection_stability": "在5个校准块上逐一留出1块重选B/C，分别至少4次保持完整校准集的选择，才允许作“足够/有损失”的诊断判断；该4/5阈值是本轮预设筛选规则，非统计保证。选择不稳定则inconclusive。"
  },
  "resources_time_cost_and_risk": {
    "hardware": "先只用同机两卡；不得依赖额外GPU，后续两节点验证总数≤6。",
    "budget": "能力与计量短检≤1 GPU·h；诊断上限6 GPU·h作为计划预算，非实际耗时估计；达到预算则保留未完成记录。",
    "execution_gates": [
      "确认两张空闲3090、驱动/CUDA、拓扑/collective与模型/张量显存可行",
      "读取并保存原cap/锁频设置，确认实际设置/恢复权限和有效响应",
      "无关作业不受影响；功率/温度/频率采样可用，计时和数值正确性通过",
      "冻结p0/p1、两条件相同的请求频率/控制模式、画像网格和独立插值检查点、张量形状、时序、计量窗口、命令及代码版本到实验计划v02后才运行"
    ],
    "failure": "若锁频、cap或通信路径不满足则停止此诊断；不把节流模拟冒充真实cap或偷偷换成另一研究问题。"
  },
  "ethics_safety_privacy_or_compliance": "只用合成/公开张量与自有空闲GPU；不使用用户生产数据，不改供电设备或越过厂家支持范围。",
  "stopping_and_interpretation_rules": {
    "support": "计量/插值/选择稳定性门槛通过，验证配对差异的描述性区间下界高于预设5%且已排除热/实现等解释→仅标该局部条件值得进一步复验；不据此确认阶段相关机制、新颖性或普遍收益。",
    "no_difference": "仅当计量/插值合格、B/C选择稳定，且配对差异区间上界低于5%时，记录“当前条件未见达到实用阈值的损失”，暂缓新增机制；这是小样本诊断边界，不证明所有场景等效。仅点估计小或一次排名相同不足以下结论。",
    "local_only": "预测误差降低但完成时间/能量无净收益→保留分析，不扩展方法。",
    "cost_excessive": "新画像收益不足覆盖其代价→短窗口分支暂缓；不能遗漏代价后称改进。",
    "inconclusive": "频率/插值证据不足、B/C选择不稳、区间跨越5%或过宽、预算耗尽、cap未生效→未知；不当作旧画像已足够，也不当作支持H1。不为得到某方向结果临时加样本。",
    "rollback": "恢复运行前记录的cap/频率配置并验证；恢复失败立即停止该设备后续运行并记录。原始日志/失败全部保留，无稳定代码版本时不虚构版本。"
  },
  "source_transfer_id": null,
  "transfer_condition_commitments": "不适用：未选择源领域方法B",
  "adaptation_vs_method_attribution": "当前只有假设诊断；运行后若需要源方法，先补TRANSFER_PACKAGE，不直接拼接。",
  "readiness": {
    "design": "诊断问题/对照/指标/解释分支已写明",
    "execution": false,
    "reason": "设备能力和具体运行参数未核验，需先完成明确的运行门槛"
  },
  "novelty_review": {
    "judgment": "未建立新颖性；Kareus已联合优化、适应变化并讨论power-limit波动；LithOS、Perseus、PowerFlow、DEFT等已有不同在线控制/成本处理",
    "record": "../../research/surveys/gpu_power_scheduling_2026/exploration_2026-11-10/nearest_neighbor_check.md",
    "scope": "限定近邻核查，未穷尽；DEFT正式ICS2026身份已核、实际读作者v1；PowerFlow仅核作者稿。发表身份与实际阅读版本分列。"
  },
  "value": "即便H0成立也能消除一个看似可做但已有方法足够的方向；H1支持后仍须补真实负载价值与近邻。",
  "implementation_change_budget": "本轮仅文档/源证据；后续只需薄测量入口及合法通信启动时序，不预建调度框架。"
}
```
