# 调研层

这里存放可追溯的外部证据：论文、精读、解析材料、产业案例与综合调研。候选题的当前判断在 [选题层](../topics/README.md)；真实运行记录在 [实验层](../experiments/README.md)。

每次推进按 [正式流程](../RESEARCH_WORKFLOW.md) 形成来源分析与 Mode C 交接；使用 [分析包模板](_templates/analysis_package.md)、[矩阵包模板](_templates/mode_c_package.md)。用户指定的原协议和五技能版本在 [standards/](standards/README.md)。

## 已有资料

- [2026-11-10近邻定点核查](surveys/gpu_power_scheduling_2026/exploration_2026-11-10/nearest_neighbor_check.md)与[计算机侧探索入口](../topics/exploration_2026-11-10.md)：补已有处理边界，进入一个诊断Idea；没有运行实验。

- [2025–2026第一轮广读：20篇问题/方法/局限、五类idea线索和优先顺序](surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/README.md)，完成于2026-11-06；15份新增主池分析包、5份复用与3个Mode C，均partial。另列1份待核验稿，未定baseline或题目。

- [近期论文与本地全文：2025–2026优先](surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/近期论文与本地全文.md)是当前入口。正式池42篇、已取得41篇全文；其中近期21篇、已取得20篇，版本与唯一缺项分别记录。
- [高水平正式论文广读池：32篇、首批8篇路线与BibTeX](surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/README.md)，核验日期2026-11-05。13篇新增正文定点分析、1篇既有正文包、18篇摘要初筛；没有全部精读或独立复现。
- [领域入门 01：研究对象、基础概念与探索地图](surveys/gpu_power_scheduling_2026/领域入门_01_研究对象与探索地图.md)，2026-11-05。
- [第一轮 Mode C 证据矩阵](matrices/power_constrained_gpu_scheduling/mode_c_v01.md)，含三个来源分析包入口；状态 partial，不代表选题已验证。
- [Acun 等：AI 数据中心需求响应论文、中文精读与中英阅读器](papers/acun_2026_dr/README.md)。
- [GPU 供电约束综合调研](surveys/gpu_power_scheduling_2026/供电约束下的GPU调度_产业与研究调研.md)，检索截止 2026-11-03。
- 调研底稿：[范围约定](surveys/gpu_power_scheduling_2026/research_brief.md)、[产业证据](surveys/gpu_power_scheduling_2026/industry_notes.md)、[GPU 系统](surveys/gpu_power_scheduling_2026/gpu_systems_notes.md)、[网络与评估方法](surveys/gpu_power_scheduling_2026/network_methods_notes.md)。

## 新材料怎么放

- 单篇论文：`papers/<first_author>_<year>_<short_name>/`。建议包含 `paper.pdf`、`README.md`；按需要加 `study/`、`bilingual/` 或 `extraction/`，不要求每篇都生成。
- 多篇综合：`surveys/<topic_or_date>/`。保留研究问题、来源链接、截止日期、方法条件与限制。
- 原始论文与解析/翻译分开。摘录、翻译、作者观点和项目推断注明身份；原始文件不直接改写。
- 新发现应回写相关综述或证据笔记，再到选题层更新判断。已被后续证据修正的历史材料仍保留其日期。

2026-11-05 的目录迁移见 [文件映射与内容哈希](file_migration_2026-11-05.json)。导师一页稿已放入 [选题层](../topics/导师讨论_一页选题建议.md)。
