# 本仓库科研推进流程

**生效日期：2026-11-05。用户要求：以后每次科研推进严格遵循指定飞书流程。**

五技能主协议为用户提供的 `research-pipeline-contract v1.0`。用户补充“跨领域迁移”后，总控采用 `research-pipeline-contract v2.0` 增加迁移分支；五类原包仍保持其 v1.0 版本。原飞书快照不改写，新分支明确记录本地技能来源。本文件把流程映射到仓库目录；详细字段和阶段行为以保存的原文及显式扩展为依据。

## 1. 流程来源与版本

| 来源 | 已读取内容 | 本地依据 |
|---|---|---|
| [WF01：飞书技能包](https://my.feishu.cn/wiki/NsUHwFrKfipMc8kArkhctYU6n0f) | `skills.zip`：总协议与五份 `SKILL.md` | [总流程原文](research/standards/sources/2026-11-05/skills/5-skill-research-workflow.md) |
| [WF02：问题清单](https://my.feishu.cn/wiki/TWXBw9US4iloe8kN2IpcAFiBnyc) | 六项研究/工程问答及一个空白的第七项 | [正文转录](research/standards/sources/2026-11-05/questions.md) |
| [WF03：视频文案](https://my.feishu.cn/wiki/OHs3w9meHiQ3OXk4XC7ctFPXnxd) | 三章方法建议、五技能闭环、科研死循环与跨领域论文方法迁移 | [完整文本导出](research/standards/sources/2026-11-05_video/video_script.md) |

用户最初提供的第二、第三条 URL 完全相同，当时为两份独立来源；本轮补 WF03 后合计三份。原包的校验信息见 [初次来源清单](research/standards/sources/2026-11-05/manifest.json)。WF03 与 WF02 已通过飞书官方导出完整文本，重新核验的 [来源、章节位置与哈希](research/standards/sources/2026-11-05_video/manifest.json) 单独保存，旧快照保留。后续更新另存版本，不静默替换。

全部想法的归档入口为 [科研方法逐条记录](research/standards/research_method_notes.md)：包含原文章节、含义、执行落点和材料间差异，原作者经验不自动作为本项目已证实事实。

跨领域扩展来源为本机可用的 [transfer-ml-methods](research/standards/extensions/cross_domain_2026-11-05/transfer-ml-methods/SKILL.md) 及 [research-workflow 六技能交接契约](research/standards/extensions/cross_domain_2026-11-05/research-workflow/references/six-skill-contract.md)，已另存 [扩展快照与哈希](research/standards/extensions/cross_domain_2026-11-05/manifest.json)。它们不是原飞书技能包的一部分；本项目采纳其中迁移机制、归因设计与交接分支，不用总控的快捷路由跳过本仓库既有必经前提。

## 2. 必经顺序

```text
来源 / 论文
  → analyze-paper
  → ANALYSIS_PACKAGE
  → deep-read（仅 Mode C）
  → MODE_C_PACKAGE：P/F/M/E + P×F 矩阵 + C 连接点
  → 如涉及跨领域方法：transfer-ml-methods → TRANSFER_PACKAGE
  → discover-innovation
  → IDEA_PACKAGE：假设、预测、对照、指标、最小验证
  → 实际执行
  → record-experiment
  → EXPERIMENT_RECORD：全部运行、实际配置、结果、偏差
  → squeeze-experiment
  → SQUEEZE_REPORT：机制线索、边界、新 P/F/M/E
  → deep-read Mode C 增量合并
  → 下一轮 MODE_C_PACKAGE
```

每次推进在该链上定位，不必从头重复已完成且仍适用的阶段；可复用已有、满足字段要求的上游包。不能用普通综述、候选题表或一页建议自动冒充标准包。

原文允许直接材料输入或等价包；本项目统一要求先检查等价性，记录来源/版本、ID、证据位置和缺项，再继续对应阶段。空缺标为 `partial` 或 `unknown`；可以补证据、做范围内分析，但不能越过尚未满足的前提去声称已验证或已完成。

用户于 2026-11-05 补充：本项目不使用哈希/SHA-256 校验流程。后续资料与运行追踪使用来源、路径、日期、明确版本及已有 Git commit；没有 Git 时保存版本化快照。本文或技能原始快照中的哈希建议不转为本项目执行要求，历史记录保留原身份。

涉及跨领域借鉴时必须做迁移核验；不涉及迁移时在推进记录中注明“不适用”及原因，保持主链。若在 Idea 筛选中才发现需迁移，先进入迁移分支，再修订同一个 Idea 的版本，不另造一套冲突的计划。

## 3. 阶段、产物与归档位置

| 阶段 | 开始前核对 | 必须留下的产物 | 推荐路径 |
|---|---|---|---|
| analyze-paper | 来源身份、版本及实际能访问的材料 | ANALYSIS_PACKAGE：事实、结果不确定性、主张/证据/实现核对、候选 P/F、限制与缺项 | `research/papers/<source>/analysis_vNN.md` |
| deep-read Mode C | 来源/分析包或实验回流包及定位 | MODE_C_PACKAGE：来源地图、P/F/M/E、交叉矩阵、C 状态与评分、最低成本核验 | `research/matrices/<topic>/mode_c_vNN.md` |
| transfer-ml-methods（涉及迁移时） | Mode C 单个连接点、目标任务/基线及失败证据、源方法或检索目标 | TRANSFER_PACKAGE：机制、条件缺口、已有迁移案例、适配与归因设计 | `topics/transfers/<topic_id>_transfer_vNN.md` |
| discover-innovation | 单个 C ID 与相应 P/F/M/E、条件及未知项 | IDEA_PACKAGE：可检验假设、预测、控制变量、主要结果、对照、停止/解释规则 | `topics/ideas/<topic_id>_idea_vNN.md` |
| 实际执行 | Idea/协议版本、当前任务授权和真实资源条件 | 实际配置、命令、日志、数据、图表及所有运行状态 | `experiments/<experiment_id>/runs/<run_id>/` |
| record-experiment | 计划版本和实际执行证据 | EXPERIMENT_RECORD：计划值与实际值、全部运行、不确定性、异常和偏差 | `experiments/<experiment_id>/record_vNN.md` |
| squeeze-experiment | 实验记录及原始证据 | SQUEEZE_REPORT：主结果、动态、机制线索、异质性、稳健性及 Mode C 输入包 | `experiments/<experiment_id>/squeeze_vNN.md` |
| Mode C 回流 | 深挖报告及可定位的新 P/F/M/E | 新矩阵版本，指出增量、冲突、证据状态与下一连接点 | 原矩阵目录新增版本 |

研究目录和包按实际工作创建；模板不算有效包。实际执行的授权依据来自用户任务与当前会话，不因每个交接阶段重复增加确认步骤；原协议也不使未授权的执行自动获得授权。

## 4. 每次推进的固定动作

**开始时：** 读 `AGENTS.md`、本文件、[推进台账](topics/progress.md)及本次阶段的本地 `SKILL.md`；说明本次阶段、上游包/来源、将补哪项证据以及预期产物。查新证据和补材料也归入相应阶段，注明状态，不能仅生成一段没有去向的想法。

**执行中：** 沿选定问题推进。问题、观测、解释和未知项分开；最小诊断一次只改变一个关键因素。依据 `IDEA_PACKAGE` 的明确方案修改关键代码，禁止顺带大范围重构或堆叠模块。若既有方案无法解释为何需要较大改动，先返回定位与方案阶段。为让诊断可运行所需的机械配套修改须列明，不作为多个研究变量同时改变。

**结束时：** 保存当前阶段包或增量版本、证据位置、缺项和下游入口；更新推进台账。涉及候选判断或实验状态时同步相应记录。回答中说明所在阶段、形成的证据/产物和下一步，不把假设或未运行方案写成结果。

目录整理、修链接等维护也遵守上述追踪原则，但标记 `maintenance`，不伪造科研包或把维护计为实验。

## 5. 统一交接字段与证据标记

每个包均包含：

```text
package_type
package_version: research-pipeline-contract v1.0
package_id
created_at（Asia/Shanghai，带日期；运行另带时分秒）
source_ids
evidence_status: verified / partial / unknown
evidence_locations
missing_evidence（无则 none）
next_skill
```

各阶段完整业务字段见 [原协议第二、三节](research/standards/sources/2026-11-05/skills/5-skill-research-workflow.md) 与各阶段技能。包 ID 采用稳定主体加版本：如 `AN-YYYYMMDD-001-v01`、`MC-YYYYMMDD-001-v01`、`IDEA-T01-v01`、`EXP-YYYYMMDD-001-v01`、`SQ-YYYYMMDD-001-v01`。具体论文、C/P/F/M/E 和 run 继续保留各自 ID。

血缘为：`analysis → source/P/F → matrix + C/P/F/M/E → idea → experiment + plan_version → squeeze → matrix 新版本`。同一事实跨版本引用时保留旧 ID/位置，不能用新结论覆盖旧证据。

迁移分支补充 `matrix/connection_C_id → transfer_id → idea/source_transfer_id`。推进台账记录 `workflow_contract_version: research-pipeline-contract v2.0`；原五类包不因此改版本。迁移包采用 v2.0 与 `TR-YYYYMMDD-001-vNN`。若旧矩阵的 `next_skill` 是 discover-innovation，在台账另记实际下一阶段为 transfer-ml-methods，不篡改原包。

- **SC**：来源明确声称。
- **DE**：有可定位的直接证据。
- **AI**：分析者推断，写清推理链。
- **UN**：未知。

`verified` 只表示当前可定位证据及边界已核对，不等同于完成独立复现或证明普遍机制。实验的生命周期状态与协议的 `execution_status`、`evidence_status` 分字段保存。

## 6. 《问题清单》的执行映射

以下是原文要求在本项目中的执行解释；原句保留在 [WF02](research/standards/sources/2026-11-05/questions.md)，不把解释倒写成原文。

| 原文位置 | 本仓库执行 |
|---|---|
| Q1：最终数据不及 SOTA | 不以“未赢 SOTA”单独否定题目；先做公平、可复现的 baseline 比较。仍按五技能协议核查最近邻与强对照，不把赢 baseline 自动解释成新颖性或发表保证 |
| Q2：关键几行、单变量 | 修改有问题定位和方案依据的关键模块；每次对照仅改变一个关键研究变量，记录代码差异与有效性。不可借实验顺带做大范围重构 |
| Q3：从失败与不同方案交叉中找现象 | 可将方案组合用于探索故障和接口冲突；标记 exploratory。失败先记录可观测事实，根因是待检验解释，不直接等同于已经证实的现象 |
| Q4：问题价值 | 查文献、定位未解决问题并预设可量化收益/适用边界；“未发现报道”不自动变成“无人做过”。无明显增益时按协议保留不确定性和限制，不删结果 |
| Q5：先分析、后实现 | 正式运行前留下诊断与完整 Idea/实现方案，代码逐项对照方案。WF02 单独未给定义，但 WF03 第1章附录和跨领域章节已明确 1a观察–1b归类–2a追问–2b串联；该缺口现已补齐，旧记录保持历史身份 |
| Q6：组合实验与论文 | 用来发现问题的组合探索保留在内部试错证据中，不直接充作论文创新/主实验；后续围绕明确问题、针对性解决方案建立独立验证 |
| Q7 | 来源为空，不补写额外规范 |

若个别经验表述与五技能详细协议存在张力，保留来源与差异，遵守两者都明确要求的最小改动、可归因、如实记录和证据边界；涉及实质取舍时显式说明，不默默删掉任一来源。

## 7. 本地技能与模板入口

本项目按下载快照读取阶段说明；系统全局同名技能不能静默替换本地协议版本。此次只保存为项目内流程依据，未改动全局安装。

| 阶段 | 本地技能 | 输出模板 |
|---|---|---|
| 来源分析 | [analyze-paper](research/standards/sources/2026-11-05/skills/analyze-paper/SKILL.md) | [分析包](research/_templates/analysis_package.md) |
| 证据整合 | [deep-read，仅 Mode C](research/standards/sources/2026-11-05/skills/deep-read/SKILL.md) | [矩阵包](research/_templates/mode_c_package.md) |
| 跨领域迁移核验 | [transfer-ml-methods](research/standards/extensions/cross_domain_2026-11-05/transfer-ml-methods/SKILL.md) | [迁移包](topics/_templates/transfer_package.md) |
| 方案生成 | [discover-innovation](research/standards/sources/2026-11-05/skills/discover-innovation/SKILL.md) | [Idea 包](topics/_templates/idea_package.md) |
| 执行记录 | [record-experiment](research/standards/sources/2026-11-05/skills/record-experiment/SKILL.md) | [实验记录包](experiments/_template/experiment_record.md) |
| 结果深挖 | [squeeze-experiment](research/standards/sources/2026-11-05/skills/squeeze-experiment/SKILL.md) | [深挖包](experiments/_template/squeeze_report.md) |

## 8. 既有工作的接入状态

目前的综述、精读和 T01–T04 台账均产生于本规范接入之前；作为历史输入保留，**尚未核验为五类标准交接包**。现有候选不是可以直接执行的 Idea 协议；不能追认已完成 Mode C、已核准实验或已深挖结果。

下一次实质科研推进先从与目标候选有关的来源补 `ANALYSIS_PACKAGE`，或核验已有等价材料并列缺项，再进入 Mode C。新规范采用和目录更新本身只计维护进度，不计研究验证。

## 9. 跨领域迁移的必查项

跨领域迁移是研究方法，不局限于迁移模型权重。本项目的目标 A 可以是 AI 工作负载、调度算法或 GPU 系统，源领域不限定在机器学习。使用迁移技能时按实际对象核验变量、约束和机制，不强行套用训练/梯度术语。超出技能适用范围的内容注明范围并按相应学科证据核验。

1. **目标 A**：先定义 Baseline 版本、失败现象和发生条件。按 1a–1b–2a–2b 形成观测、分类、竞争解释及候选因果链。文献现象与本机实测分开；缺实测可先做条件性判断。
2. **源方法 B**：按相同底层困难检索原始研究，核实它改变哪个操作、为什么可能有效、依赖什么条件。术语相同或接口能接上只说明表面相似，不构成机制对应。
3. **已有迁移案例**：查 B 的跨任务使用、成功和失败，比较真实改造及边界。案例用 `transfer_case_ids`，不能与 Mode C 的 `connection_C_id` 混淆；未找到案例保留检索范围与缺项。
4. **条件缺口与 A′**：逐项列出 B 的条件在 A 中“已满足／可建立／不满足／未知”，说明适配位置、条件测量、代价与新混杂；必要性未经验证时不能写成必要条件。若改 B 更合理，解释如何保留机制。
5. **可归因验证**：至少覆盖概念上的 A、A′、A′+B，分别识别适配收益与 B 的增量作用。A′ 无必要变化时可与 A 合并并说明。额外的 A+B、破坏条件、移除核心运算、匹配计算预算等对照由竞争解释决定，单个对照只改一个关键因素。
6. **交接与回流**：迁移技能给出“值得最小验证／先补证据／目前不适合”的条件判断，整理成 TRANSFER_PACKAGE 后交回 discover-innovation 统一 Idea。新源论文补 analyze-paper；新增证据改变 P/F/M/E/C 时增量更新 Mode C。实际运行仍经 record-experiment、squeeze-experiment，再回 Mode C。

验证同时看**条件是否建立、目标失败现象是否改善、机制预测是否出现、整体指标与代价**。只看到最终指标提高，不能断言迁移机制成立；只看到 A′ 改善而 B 无增量，不能把收益归给 B。源领域成功、找不到同题论文和简单 A+B 拼接都不等于目标任务有效或创新成立。

迁移报告是方案与证据，不能代替真实运行记录；默认到用户要求的阶段结束。缺少条件或证据时保留未知和最低成本检查，不为完成流程强拼模块。

## 10. 避免科研死循环的执行检查

依据 WF03“科研遇到问题后，怎样做实验才能少走弯路？”及“科研死循环的根本原因”。逐项出处见 [方法记录 DL01–DL09](research/standards/research_method_notes.md)。每次新增机制前在 Idea/计划中补：

1. **已有解决方案**：查类似底层问题的论文、适用条件和负结果，再说明为何做当前改动；来源缺项如实记录。
2. **本轮单一问题**：明确本轮改变的关键因素、想区分的解释及最低成本测量。强交互另做明确对照，不能归因于单因素。
3. **局部到整体**：同时预设目标现象、中间机制、整体去留指标、可能受损的约束、资源和复杂度代价。局部改善不自动意味着方案应保留。
4. **回退方案**：指定稳定代码/配置版本、可观测触发条件、恢复方式和需要单独保留的现象。回退不删除原始运行和用户修改；无 Git 时保存明确版本的文件快照，不假装有可用 commit。
5. **结果解释分支**：成功、无明显差异、局部改善但整体无收益、代价不可接受，分别产生什么判断与下一检查；噪声或功效不足不能硬判失败。
6. **新增知识与下一步**：上次结果经记录和深挖后，写清本轮减少了哪项不确定性、仍有哪些竞争解释。下一版本应有新证据或有区分力的检验；不能用运行次数、补丁数或已投入成本代替进展。

1a–2b 的诊断和 3a–4b 的替换/消融按 [完整记录](research/standards/research_method_notes.md) 嵌入主链。出现新问题先定位与归因，再决定继续、回退、补证据或停止，不自动在已有补丁上继续加模块。每轮的事实、推断与未知仍按统一证据标记保存。
