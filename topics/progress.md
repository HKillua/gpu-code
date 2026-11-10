# 推进台账

本台账记录每次推进的阶段、输入、产物、缺项和下游入口。科研工作遵循 [正式流程](../RESEARCH_WORKFLOW.md)；维护任务标为 `maintenance`，不计为科学验证。后续新条目追加，保留历史。

当前总控：`workflow_contract_version: research-pipeline-contract v2.0`。保留原五类包的 v1.0 身份，涉及迁移时另存 v2.0 TRANSFER_PACKAGE。

| 推进 ID / 日期 | 阶段 | 上游依据 | 本次产物 | 状态与缺项 | 下一步 |
|---|---|---|---|---|---|
| PR-20261105-001 / 2026-11-05 | maintenance：接入用户指定流程 | 用户明确要求；WF01 技能包、WF02 问题清单 | 源快照、哈希、RESEARCH_WORKFLOW、AGENTS 入口、五类模板、已有材料状态标记 | 两份独立来源已读；“1a-2b”未定义、Q7 为空、第三 URL 重复；未生成科研验证包 | 按目标候选补来源分析包或检查等价材料，再做 Mode C |
| PR-20261105-002 / 2026-11-05 | maintenance：接入跨领域迁移分支 | 用户补充“跨领域迁移”；transfer-ml-methods 与六技能契约的本地版本 | 扩展快照/哈希、迁移包模板、Idea/实验模板补充、流程及 AGENTS 更新 | 分支规范已记录；未选择 B、未形成实际迁移包或实验结果。新技能明确 1a–1b–2a–2b，未倒改 WF02 未定义短语 | 先补来源与 Mode C；涉及外领域方法时核验机制条件并交回统一 Idea |
| PR-20261105-003 / 2026-11-05 | maintenance：记录全部方法想法与防止科研死循环 | 用户补 WF03 视频文案，并重申 WF02；两份官方 Markdown 导出 | 原始导出/章节索引/哈希、逐条方法记录、1a–4b、净收益/回退/新增知识要求、模板和规范更新 | 已读完整导出文本；WF02 与旧转录一致。WF03 补齐此前 1a–2b 定义；Q7 仍空白；未开展科研实验 | 实质推进时引用方法记录，先补来源与 Mode C；试验必须给出整体去留与回退依据 |
| PR-20261105-004 / 2026-11-05 | analyze-paper → deep-read Mode C：领域入门第一步 | S01 Acun 正文；S02 Phoenix 正文与部分工件；S03 PowerSlider 正文，版本见各分析包 | AN-20261105-001/002/003-v01；MC-20261105-001-v01；领域入门 01 | 全部 partial；有可定位文献证据，尚无本机复现、完整最近邻审计、正式 Idea 或新颖性结论 | 先理解服务约定—运行状态—动作—恢复代价，补读最近邻，再增量更新 Mode C |
| PR-20261105-005 / 2026-11-05 | analyze-paper：正式发表论文广读 | 用户要求优先知名期刊/会议；既有领域地图与MC-001-v01；官方发表记录与公开正文 | 32篇阅读池/中文卡/BibTeX/来源汇总；新增13个partial分析包，复用AN-002-v01 | 18篇仍为摘要初筛；未独立复现；旧预印本与正式版阅读边界分开；本轮没有新Mode C或Idea | 先按阅读路线讨论POLCA、Zeus等，选3–8篇正文来源分批做Mode C增量 |
| PR-20261105-006 / 2026-11-05 | maintenance：项目简洁原则与用户偏好 | 用户明确不要哈希/SHA校验，要求接入Ponytail；Ponytail官方README、INSTALL、AGENTS、技能原文与MIT许可 | 根AGENTS新增常驻Ponytail full原则、近期论文优先和不做哈希要求；本地Ponytail技能与许可；科研流程/实验模板去掉哈希要求 | 项目规则与技能已接入；采用官方支持的项目级方式，未安装宿主插件或启用生命周期hook；科研主链保留 | 后续直接执行常驻规范，研究仍按当前来源分析阶段推进 |
| PR-20261105-007 / 2026-11-05 | maintenance资料获取 + analyze-paper近期摘要初筛 | 用户要求立即下载并优先2025–2026；原32篇阅读池和新论文正式记录 | 原池31份PDF、新增10篇正式来源及10份PDF、另1份待核验会议稿；近期阅读入口与分组来源记录 | 正式池42篇中取得41篇；近期21篇中取得20篇；PctoDL PDF缺失。新增10篇仅初筛，无新分析包或实验；下载未用哈希 | 优先读近期论文正文，补方法/评估证据后分批进入Mode C；保留旧经典作补充 |
| PR-20261105-008 / 2026-11-05 | maintenance：确认资源边界与baseline筛选标准 | 用户限定两机六张3090；已有来源包、近期初筛和MC-001-v01 | AGENTS资源/结论边界，D005决策记录；区分可运行、核心现象复现与原规模完整复现 | baseline尚未选定；服务器能力未知；无新增研究包、算法或实验，未把可行性建议写成结果 | 来源分析阶段补候选正文与工件，核验六卡能否保留关键机制，再经Mode C与诊断Idea进入实测 |
| PR-20261105-009 / 2026-11-05 | maintenance：澄清阅读范围 | 用户补充：广泛阅读找idea，六卡只约束选题与落地；D005 | AGENTS文献范围规则、D006决策记录 | 阅读允许其他规模/硬件/领域；未选择baseline、方法或迁移方案 | 继续广读与来源分析，形成机制线索后再筛六卡可验证问题 |

| PR-20261106-001 / 2026-11-06 | analyze-paper → deep-read Mode C：近期论文正文广读 | 已下载20项2025–2026正式研究、单列GreenLLM观察稿；复用AN002/007/019/023/024，旧MC001 | 新增15份主池partial分析包+1观察包；4组正文卡、20篇总表/五类线索/六篇优先路线；MC002/003/004三批7+6+7来源 | 正文方法/关键评估/局限定点阅读完成；源码/原始数据/独立复现与新颖性未完成。PctoDL全文仍缺；未定baseline/选题 | 先逐篇导读及最近邻/版本核验，Mode C增量；单个C证据充分后再进入Idea，涉及迁移先补迁移包 |
| PR-20261109-001 / 2026-11-09 | 文献精读与交付物制作 | 六份既有partial分析包与本地PDF | 六篇中英对照页、中文学习报告及解析图表；详情见下 | 阅读侧产物；未做浏览器交互验收、代码复现或实验 | 跨篇对照与近邻补证，不将报告建议直接当Idea |
| PR-20261110-001 / 2026-11-10 | maintenance学科边界；analyze-paper → Mode C → discover-innovation诊断设计 | 用户明确不做电气；旧MC002/004:C02、Kareus/LithOS/Tessera分析与DEFT增量 | D007、AN-20261110-001/002-v01、MC-20261110-001-v01、IDEA-DIAG01-v01；探索入口、近邻核查和Kareus报告校正 | 全部研究包partial；仅诊断设计，未确认目标现象/新颖性；硬件/参数未核，未运行 | 先只读能力核验并冻结执行计划，再实际执行→记录→深挖→Mode C回流 |

## PR-20261105-004 交接详情

- 日期与时区：2026-11-05，Asia/Shanghai；总控版本维持 v2.0，来源/矩阵包维持 v1.0。
- 用户目标：循序了解供电约束下 GPU 调度的领域与可探索内容。当前停留在来源分析与领域矩阵阶段，没有选定 T01–T04 或启动算法实现。
- 上游与产物：[AN-20261105-001-v01](../research/papers/acun_2026_dr/analysis_v01.md)、[AN-20261105-002-v01](../research/papers/colangelo_2025_grid_interactive/analysis_v01.md)、[AN-20261105-003-v01](../research/papers/li_2026_powerslider/analysis_v01.md) → [MC-20261105-001-v01](../research/matrices/power_constrained_gpu_scheduling/mode_c_v01.md) → [领域入门 01](../research/surveys/gpu_power_scheduling_2026/领域入门_01_研究对象与探索地图.md)。证据定位分别保存在包内 evidence_index；矩阵维持来源命名空间。
- 新增知识：服务约定决定可允许退化；原设备/模型上存在任务或阶段敏感性差异；各系统的状态处理协议不同。不能从稳态曲线直接推出等量实测让电的损失，不能从 checkpoint 接口存在推出恢复成本重要。
- 产物验证：JSON 字段、来源/证据 ID、文档链接静态核对；并行审阅发现的矩阵越界已收紧。未运行论文代码、GPU 控制或实验。
- `evidence_status: partial`；缺项：本机硬件/网络/权限、实际业务与事件时间尺度、完整近期近邻、状态切换测量。S03 的部分合同与动态结果口径待澄清，本轮不采用存疑数值。
- 迁移：不适用；尚未定位目标失败机制或选择源方法 B，`transfer_id: none`。跨领域方法核验仍保留在 Mode C 与正式 Idea 之间。
- 实际下一阶段：`analyze-paper / deep-read Mode C` 增量补证据。先沿服务约定与动作代价补读和解释，不因原包 next_skill 字段指向 discover-innovation 就跳过缺项。后续正式实测前形成诊断 Idea 与版本化计划。

## PR-20261105-005 交接详情

- 日期/时区：2026-11-05，Asia/Shanghai；`workflow_contract_version: research-pipeline-contract v2.0`，本轮 ANALYSIS_PACKAGE 维持 v1.0。
- 阶段与目标：来源检索、正式发表身份核验及分层阅读。用户希望先广读，再讨论探索内容；不急于选择T01–T04或实现方法。
- 上游：[领域入门01](../research/surveys/gpu_power_scheduling_2026/领域入门_01_研究对象与探索地图.md)、[MC-20261105-001-v01](../research/matrices/power_constrained_gpu_scheduling/mode_c_v01.md)、已有AN-20261105-002-v01。
- 本次产物：[32篇导读](../research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/README.md)、四组中文卡与来源JSON、[检索/纳入边界](../research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/search_protocol.md)、[逐篇包血缘](../research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/manifest_v01.json)、BibTeX。
- 新包ID：AN-20261105-005/006/007-v01（功率/能耗）；011/012/013-v01（集群调度）；017/018/019-v01（通信/弹性）；023/024/025/026-v01（能源信号/在线决策）。均为partial；具体路径与证据定位见汇总索引。复用002-v01时明确正文是Phoenix预印本，正式期刊版仅书目已核。
- 阅读计数：32项不同研究，28会议/4期刊；13篇新增正文定点分析、1篇既有正文分析、18篇摘要/书目初筛。POMACS全文和SIGMETRICS展示不重复计数。正式发表不等于方法质量已全面审计。
- 新增知识：POLCA的设施额度、Zeus的作业内能量、GREEN的历史碳信号、DynamoLLM的流量预测属于不同问题；恢复成本已出现在Pollux、GREEN、Sailor与OPR中。进一步探索须定位真实机制差异，不能以关键词组合建立新颖性。
- 验证：对来源JSON、标准包字段与ID、相对链接、去重和阅读计数做静态检查；抽查正式发表记录。未运行论文代码、GPU控制、模型训练或实验。
- `evidence_status: partial`；缺项：18篇正文方法/结果、各代码/轨迹可用性与完整统计、本机可迁移条件、完整最近邻及新颖性审计。
- 跨领域迁移：不适用；目标失败机制和源方法B未选择，`transfer_id: none`。本轮读邻接文献不等价于方法迁移。
- 实际下一阶段：先逐篇讨论；选取相近的3–8个已分析来源做 `deep-read Mode C` 增量，初筛来源先补正文。不将本轮广读清单作为IDEA_PACKAGE，不进入执行阶段。

## PR-20261105-007 交接详情

- 日期/时区：2026-11-05，Asia/Shanghai；阶段为资料获取维护与来源摘要初筛，不计实验或独立复现。
- 上游：用户要求下载全文、优先2025–2026；[旧32篇阅读池](../research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/README.md)。按当前用户要求不计算/记录哈希。
- 产物：[近期论文与本地全文](../research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/近期论文与本地全文.md)、[原池下载记录](../research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/downloads_existing_2026-11-05.json)、[新增功率/能耗来源](../research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/recent_power_additions.json)、[新增共享/抢占来源](../research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/recent_systems_additions.json)；PDF在各论文目录。
- 来源ID：RP01–RP04为4篇正式功率/能耗邻接工作；RNS01–RNS06为6篇正式共享/抢占/动态调度工作。全部只是摘要和首页初筛，未生成标准ANALYSIS_PACKAGE，下游前需补正文证据。GreenLLM保存为RPW01待核验稿，不计正式主池；其余观察线索不充数。
- 下载计数：原池27份新下载、4份已有复用，共31/32；新增正式10份、待核验稿1份。正式主池42篇、全文41篇，另存1份待核验稿；其中2025–2026正式论文21篇、已有20篇全文。
- 版本边界：Phoenix使用arXiv v1；GFS正式身份已核、本地为arXiv v1；MLPerf Power使用带HPCA标识的arXiv v2；FlipFlop为作者稿；其余来源记录保留正式/作者公开版身份。FastServe正式发表于2026，但同系预印本2023已公开，不能将发表年份当首次提出时间。
- 唯一主池下载缺项：PctoDL出版社返回403/安全验证页，作者公开仓库未找到全文；没有绕过验证或把HTML冒充PDF。原始公开地址与尝试已记录。
- 验证：所有已取得PDF可打开并核对首页题名，逐项记录页数、来源和版本；汇总检查本地链接、年份、去重及数量。未运行任何GPU作业或论文代码。
- `evidence_status: partial`；缺项为新增论文正文设计/评估、版本差异与本机适用性。资料取得状态与研究证据状态分开。
- 迁移：不适用，未选择源方法B，`transfer_id: none`。实际下一阶段仍为analyze-paper正文阅读，再按有效包选择3–8个来源进入Mode C；未定题、未设计新方法。

## 新条目字段

PR-20261105-008补充：迁移不适用，尚无源方法B；只读核对Nixie官方摘要/README、GREEN官方条目及已有正文证据、Zeus官方工具说明。Nixie的公开代码与消费GPU定位不等于已在本机兼容；Zeus的计量/优化工具与某个具体调度算法baseline分开。资料与规范更新没有运行GPU、功率控制或复现实验。

- 推进 ID、日期与时区：
- 当前阶段及涉及候选/实验：
- 是否涉及跨领域迁移、实际下一阶段、transfer_id（不适用时说明）：
- 上游包 ID、版本与证据位置：
- 本次目标、改动及新增证据：
- 产物路径/包 ID：
- `evidence_status` 与缺失证据：
- 下一阶段、最低成本动作及其前提：

运行细节保存在实验目录，本文链接过去；不能用一行“实验成功”替代实验记录。

## PR-20261106-001 交接详情

- 日期/时区：2026-11-06，Asia/Shanghai；阅读批次起于2026-11-05，因此目录与AN/MC主体ID沿用05。`workflow_contract_version: research-pipeline-contract v2.0`；来源/矩阵包维持v1.0。
- 用户目标：按科研流程广读已下载2025–2026论文，分析问题、方法、局限和idea线索，给优先阅读清单；不急于定baseline或选题。
- 上游：[近期全文入口](../research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/近期论文与本地全文.md)、已有效AN002/007/019/023/024、旧MC001。正式主池20项及GreenLLM观察稿分别计数。
- 来源产物：[广读总报告](../research/surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/README.md)、[索引与血缘](../research/surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/index_v01.json)、四组正文卡与JSON。新增主池AN031–039、041–046共15份；复核复用5份；观察稿AN047另计。20项均已定位阅读方法、关键评估与局限，非逐页全文/代码精读。
- 证据整合：[MC002功率/服务/窗口](../research/matrices/power_constrained_gpu_scheduling/mode_c_002_power_v01.md)、[MC003共享/状态/保障](../research/matrices/power_constrained_gpu_scheduling/mode_c_003_sharing_v01.md)、[MC004资源/画像/副作用](../research/matrices/power_constrained_gpu_scheduling/mode_c_004_resources_v01.md)。7+6+7不同来源，每批3P/3F/3M、完整9格，共7个C；普通导读不冒充Idea包。旧MC001保留。
- 新增知识：供电额度、能量、碳信号、卡数保障不是同一问题；短时与全轨迹SLO、稳态与完整事件窗不同；恢复、预测、共享/DVFS已有大量研究。候选线索收窄到动作权限、状态兑现、画像有效性和信息窗口，均未声称创新成立。
- 交叉审阅修正：TAPAS应急Perf为5分钟峰值窗限频代理；SpotGPU分配率/退出时延、Tessera MFU/内存代价、VAHRM仿真与瞬时超限分开；Sailor真实硬件范围比旧包简述广，增量另编号LN03-E04/E05/E06-BR，不覆盖旧E1/E2；GFS v1公式、FlipFlop协议、GreenLLM出版与计量歧义保留UN。
- `evidence_status: partial`；缺项：源码/原始日志、独立复验、完整统计与版本差异、本机环境/权限/网络/计量、真实事件及服务合同、完整最近邻与新颖性。PctoDL无全文，本轮不采其正文结论。
- 验证：逐篇JSON与必填字段、本地PDF可打开、相对链接、包/来源/证据ID、20项去重、7+6+7分批覆盖及矩阵格检查；并行只读审阅关键数字/概括。没有运行GPU、论文工件、调度器或任何实验，不计算哈希。
- 迁移不适用：未选目标A或源方法B，`transfer_id: none`；阅读SPADE等邻接工作不等于迁移。未生成正式IDEA_PACKAGE，不跳入执行/实验记录/深挖阶段。
- 实际下游：按六篇路线逐篇讨论与补最近邻，先回答现象/条件/竞争解释；形成单个C的诊断依据后进入discover-innovation，需要外领域方法时先transfer-ml-methods。当前baseline和题目均未选定。

## PR-20261109-001 交接详情

- 推进 ID、日期与时区：PR-20261109-001；2026-11-09，Asia/Shanghai。阶段为**文献精读与交付物制作**（阅读侧），不计实验、复现或 Idea 验证。
- 当前阶段及涉及来源：按用户给定的六篇优先清单逐篇完成「中英对照精读页 + 中文学习报告页」。来源 ID：LP05/TAPAS、LS08/SpotGPU(ASI)、LC02/GREEN、LP03/DynamoLLM、RNS01/Nixie、LP06/Kareus；对应分析卡 AN-20261105-031/044/023/007/035/032-v01，均为 partial，正文已按 PDF 通读。
- 是否涉及跨领域迁移：不适用。本轮为阅读与呈现，未选目标 A/源方法 B，`transfer_id: none`。
- 上游包 ID、版本与证据位置：上述六份 ANALYSIS_PACKAGE（v1.0，partial）；正文证据为各论文目录下 `bilingual/raw.txt`（Kareus 因控制字符另存 `raw_clean.txt`）与 `bilingual/figures.json`；本地 PDF 均按首页题名、页数、来源核对。
- 本次目标、改动及新增证据：①六篇均产出牛来阅读器风格双语页（左英右中、句级联动、可批注）与学术报告页（Mermaid/MathJax、目录与主题）。②逐图裁图质检：DynamoLLM 重裁 fig8–12/14–17 九张叠裁；Kareus 抽检 15 张全部干净；Nixie 抽检 fig2/fig3。③Kareus 双语表 1–10 逐格回查原文核对，附录 A 定理与附录 B/C 超参按原文补齐，参考文献精简为主要条目并注明完整条目页码。
- 产物路径/包 ID：
  - 双语：[TAPAS](../research/papers/stojkovic_2025_tapas/bilingual/TAPAS_中英对照.html)、[SpotGPU](../research/papers/li_2026_spotgpu/bilingual/SpotGPU_中英对照.html)、[GREEN](../research/papers/xu_2025_green/bilingual/GREEN_中英对照.html)、[DynamoLLM](../research/papers/stojkovic_2025_dynamollm/bilingual/DynamoLLM_中英对照.html)、[Nixie](../research/papers/xu_2026_nixie/bilingual/Nixie_中英对照.html)、[Kareus](../research/papers/wu_2026_kareus/bilingual/Kareus_中英对照.html)
  - 报告：[TAPAS](../research/papers/stojkovic_2025_tapas/study/TAPAS_学习报告.html)、[SpotGPU](../research/papers/li_2026_spotgpu/study/SpotGPU_学习报告.html)、[GREEN](../research/papers/xu_2025_green/study/GREEN_学习报告.html)、[DynamoLLM](../research/papers/stojkovic_2025_dynamollm/study/DynamoLLM_学习报告.html)、[Nixie](../research/papers/xu_2026_nixie/study/Nixie_学习报告.html)、[Kareus](../research/papers/wu_2026_kareus/study/Kareus_学习报告.html)；Markdown 源稿同名保留在 `study/`。
  - 计数：Kareus 双语 493 句对 / 177 块 / 15 图 / 10 表 / doc_key `kareus-osdi26`；Kareus 报告 50.9KB / 657 行 / 11 Mermaid / 27 表。
- `evidence_status`：读后记录为 `partial`。缺项：未运行任何论文代码或开源实现（Kareus 仓库未克隆审计）；70B 数字为论文模拟器结果，非实测；六篇 HTML 均只做静态核验（句对数、图/表数、MathJax、doc_key、无占位符），**未做浏览器交互验收**（悬停联动、批注持久化、主题切换、目录跳转）；不计算哈希。
- 下一阶段、最低成本动作及其前提：以六篇的"带着什么问题读"为线索做跨篇对照与最近邻补证，优先 Kareus 的"降频改变最优重叠安排"与 DynamoLLM 的"三层节律"，判断能否在两机六卡上做最小机理检验；前提是先确认 3090 锁频/功耗上限权限与可调 SM 的通信 kernel 是否可得，否则只能做到"时机 + 频率"二维。

## PR-20261110-001 交接详情

- 日期/时区：2026-11-10，Asia/Shanghai。总控保持 `research-pipeline-contract v2.0`，ANALYSIS/MODE_C/IDEA 包保持 v1.0；按项目保存的 analyze-paper、deep-read Mode C、discover-innovation 技能推进。
- 用户约束维护：新增 [D007](decisions.md) 并更新根 AGENTS/README。研究贡献必须落在电子信息/计算机系统，供电预算只作外部输入；电网、发电机理、出力预测、储能和电气设备控制不作为核心研究。T04 原建议按新边界重审，旧记录保留。两机六张3090仍是实现与证据边界，文献阅读不受其规模限制。
- 上游与选择：从 `MC-20261105-002-v01:C02` 和 `MC-20261105-004-v01:C02` 的画像条件线索收窄；原包 `handoff_ready:false` 未改。本轮补证后只开放单一连接点的诊断设计，不把原广读建议追认为创新。
- 来源产物：[AN-20261110-001-v01](../research/papers/wu_2026_kareus/analysis_constraints_v01.md) 复用 Kareus AN032 并增 E05–E09，定位前沿选点、恒频定理前提、画像开销和工件入口；[AN-20261110-002-v01](../research/papers/chen_2026_deft/analysis_v01.md) 补 DEFT 已有运行时处理边界。复用 LithOS AN036、Tessera AN043。DEFT正式ICS2026身份已核，实际读作者arXiv v1在线HTML，未对读出版社版/下载PDF。
- 近邻与版本：[限定核查记录](../research/surveys/gpu_power_scheduling_2026/exploration_2026-11-10/nearest_neighbor_check.md) 覆盖 Kareus、Perseus、DEFT、PowerFlow；后两类版本/阅读边界分别保留。Kareus仅定点读作者 `osdi26ae` 分支说明、配置及启动脚本，未审完整调用链、未运行。此增量不改历史阅读池数量。
- 矩阵产物：[MC-20261110-001-v01](../research/matrices/power_constrained_gpu_scheduling/mode_c_20261110_profile_v01.md)，4份来源，2P×3F共6格、2M、1C。已有联合调度/动态适应/切换成本作为反重复边界；cap导致画像失效仍为AI。`handoff_ready:true` 仅限诊断Idea，`execution_ready:false`。
- Idea产物：[IDEA-DIAG01-v01](ideas/diag01_profile_validity_v01.md)，只绑定C01：在实际cap生效后，旧多频率画像重新查询是否仍足够选择计算—通信时序？先同机两卡、固定工作量/通信实现；比较固定配置、逐卡实际频率重索引旧画像、同候选集新条件校准选择。写明二维插值检查、相同请求频率/控制模式、独立校准/验证、成本、选择稳定性和支持/无实用差异/未知分支；5%仅为预设诊断阈值。
- 新增知识：Kareus已支持前沿选点适应变化并讨论功率限制波动；不能以“动态联合优化”作为差异。恒频论证的同平均频率同耗时前提可检验，但尚无其在本项目失效的证据。DEFT等已有争用、切频与画像代价处理；只加这些因素不能建立新颖性。
- 阅读产物校正：同步修订 [Kareus学习报告Markdown](../research/papers/wu_2026_kareus/study/Kareus_学习报告.md) 与 [HTML](../research/papers/wu_2026_kareus/study/Kareus_学习报告.html)，标注2026-11-10；更正四卡案例、总/动态能量区别，补齐定理条件并收紧未经核验的3090能力/发表判断。双语原页未改。
- 入口：[计算机侧探索说明](exploration_2026-11-10.md) 保留任务/状态、计算/通信、干扰/准入三条路线；当前只设计中间一条诊断，未确定最终题目或论文baseline。
- `evidence_status: partial`。缺项：硬件/拓扑/控制权限/遥测，完整实现与近邻审计，目标失效及净收益的真实证据。当前无实验记录或深挖包；不生成空结果、不把设计完成当实验完成。
- 验证：新包JSON、必填交接字段、来源/证据引用、矩阵覆盖与相对路径静态检查；并行审阅来源边界及诊断对照，修正重索引规则、cap/频率混杂和无差异判定。文档任务未运行GPU、控制硬件或执行论文工件。
- 迁移不适用：未选择源方法B，`transfer_id: none`；后续若出现需借鉴的机制，仍先 `transfer-ml-methods → TRANSFER_PACKAGE`，不以“跨领域”名义拼接模块。
- 实际下一阶段：只读核验服务器能力；通过后将具体配置/命令/版本冻结为实验计划，再 `实际执行 → record-experiment → squeeze-experiment → deep-read Mode C`。若画像已足够则记录条件边界并暂缓新增机制；若计量不充分则为未知，不能强推选题。
