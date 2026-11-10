# GPU 调度研究工作区

围绕数据中心供电约束与 GPU 调度开展调研、选题和实验。研究贡献限定在电子信息与计算机系统：GPU、运行时调度、分布式计算/通信及信息处理。供电预算作为外部约束，不做电网、储能、逆变器或发电机理等电气课题；具体场景与论文题目尚未确定。

用户已说明的实验资源：**2 台服务器，每台 3 张 RTX 3090，共 6 张卡**。服务器系统、网卡、功率控制权限和整机计量能力待核验。当前尚无实现代码或真实实验运行记录。

**每次科研推进必须遵循用户指定的 [科研流程](RESEARCH_WORKFLOW.md)。** 先定位当前阶段与上游包，按五技能主链交接；涉及跨领域借鉴时，在 Mode C 与 Idea 之间加入迁移核验。每次更新 [推进台账](topics/progress.md)。原文与版本保存在 [流程依据](research/standards/README.md)。

## 四层目录

```text
GPU/
├── AGENTS.md                 # AI 协作与仓库工作规范
├── RESEARCH_WORKFLOW.md      # 用户指定的科研流程、阶段与交接要求
├── README.md                 # 总入口、当前状态
├── .gitignore
├── research/                 # 调研：原文、精读、产业证据、综述
│   ├── papers/acun_2026_dr/
│   │   ├── paper.pdf
│   │   ├── study/            # 中文学习报告与提取文本
│   │   └── bilingual/        # 中英对照 HTML、图片与解析结果
│   └── surveys/gpu_power_scheduling_2026/
├── topics/                   # 选题：候选、差异、取舍、导师讨论
│   ├── candidates.md
│   ├── decisions.md
│   └── 导师讨论_一页选题建议.md
├── src/                      # 代码：可复用实现，当前只有说明
└── experiments/              # 实验试错：计划、配置、运行、分析
    └── _template/README.md   # 未运行的实验记录模板
```

每层的使用方法：[调研](research/README.md) · [选题](topics/README.md) · [代码](src/README.md) · [实验试错](experiments/README.md)。

## 从这里开始

| 需要做的事 | 入口 |
|---|---|
| 当前计算机侧探索与诊断 | [2026-11-10探索入口](topics/exploration_2026-11-10.md) |
| 第一步了解领域与可探索问题 | [领域入门 01](research/surveys/gpu_power_scheduling_2026/领域入门_01_研究对象与探索地图.md) |
| 看近期20篇广读、idea线索和优先顺序 | [2025–2026第一轮广读](research/surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/README.md) |
| 优先读2025–2026论文、打开本地PDF | [近期论文与本地全文](research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/近期论文与本地全文.md) |
| 广读高水平正式论文 | [32篇阅读池与首批8篇路线](research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/README.md) |
| 了解产业需求与研究版图 | [完整调研报告](research/surveys/gpu_power_scheduling_2026/供电约束下的GPU调度_产业与研究调研.md) |
| 和导师讨论 | [一页选题建议](topics/导师讨论_一页选题建议.md) |
| 看当前候选与待验证问题 | [候选题台账](topics/candidates.md) |
| 看已经确认的决定 | [决策记录](topics/decisions.md) |
| 开始本次科研推进 | [流程规范](RESEARCH_WORKFLOW.md) 与 [推进台账](topics/progress.md) |
| 查科研死循环、迁移和问题清单的全部想法 | [逐条方法记录](research/standards/research_method_notes.md) 与 [原文索引](research/standards/README.md) |
| 读已有论文 | [论文资料入口](research/papers/acun_2026_dr/README.md) |
| 准备第一次实验 | [实验约定](experiments/README.md) 与 [记录模板](experiments/_template/README.md) |

## 当前进度

- **调研**：已完成一轮产业与文献调研，历史报告检索截止为 2026-11-03；后续更新须另记覆盖日期。
- **近期论文广读**：2026-11-06完成已下载20项2025–2026正式研究的正文定点广读，新增15份partial分析包、复核复用5份，另单列1份待核验稿；形成3个分批Mode C包、五类问题线索和六篇优先路线。[逐篇问题/方法/局限与阅读范围](research/surveys/gpu_power_scheduling_2026/broad_read_2026-11-05/README.md)为当前入口，未选baseline或题目。此前[32篇阅读池](research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/README.md)保留当时状态。
- **近期论文与下载**：按用户新要求优先2025–2026，正式池扩至42篇，其中21篇近期论文；已取得41篇全文，近期20篇。另有1份待核验会议稿。PctoDL的PDF受出版社访问限制尚缺；[本地PDF与版本说明](research/surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/近期论文与本地全文.md)为当前阅读入口。资料下载不等于正文精读。
- **简洁原则**：已通过项目规则与[本地Ponytail技能](.agents/skills/ponytail/SKILL.md)常驻贯彻最小完整实现；按用户要求不再做哈希/SHA校验。
- **科研探索**：2026-11-10明确电子信息/计算机学科边界，补Kareus条件与DEFT近邻分析，新增受限范围Mode C及[第一份诊断Idea](topics/ideas/diag01_profile_validity_v01.md)。已写假设与对照，硬件/运行参数未核，尚不具备执行条件；完整近邻、新颖性与实际效果均未确定。旧Mode C保留。
- **跨领域迁移**：已接入第六技能分支与迁移模板；尚未选择源方法或验证迁移效果。
- **选题**：保留四个候选，尚未正式选定。候选、推荐和已确认决定分别记录。
- **代码**：尚未建立运行环境或实现调度器。
- **实验**：尚未核验服务器环境、下发功率控制或运行实验；模板和调研中的方案均不是实测结果。

工作链路为：**来源分析 → Mode C 证据矩阵 → 需要时跨领域迁移核验 → Idea 与最小验证 → 实际执行 → 实验记录 → 深挖 → Mode C 回流**。可复用实现整理进代码层；小规模诊断也有对应 Idea/计划与证据记录。

## 维护约定

新内容先确定所属层，不再在根目录堆放 PDF、截图和临时脚本。目录与代码文件优先使用英文小写下划线；中文报告名保留。仓库内链接使用相对路径。详细规则见 [AGENTS.md](AGENTS.md)。

2026-11-05 已整理原有文件，PDF、HTML、解析数据和图片均保留在本地；仅修改导师讨论稿中的跨目录链接。[迁移清单及前后 SHA-256](research/file_migration_2026-11-05.json) 可用于定位旧文件。GitHub 仓库不上传论文 PDF、全文解析文本、中英对照页及其图片等生成附件；这些文件仍可从本地目录打开，GitHub 上的相应本地链接不会提供文件。论文分析、学习报告、调研和选题记录照常上传。
