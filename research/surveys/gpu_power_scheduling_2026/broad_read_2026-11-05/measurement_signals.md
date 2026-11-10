# 计量、碳信号与现场降载：五篇正文广读及一项观察稿

阅读批次开始2026-11-05，完成2026-11-06（Asia/Shanghai）。新增2份正式来源包，复核复用3份；另新增1份待核验稿分析包。全部partial。没有选择baseline、题目或运行实验。

SC为来源主张，DE为可定位证据，AI为分析推断，UN为未知。本文不把各论文不同分母的收益作横向排名。

## LC02 · GREEN · NSDI 2025

[分析包](../../../papers/xu_2025_green/analysis_breadth_v01.md) · [本地PDF](../../../papers/xu_2025_green/paper.pdf) · [来源](https://www.usenix.org/conference/nsdi25/presentation/xu-kaiqiang)

**问题。** 固定GPU容量下，怎样利用训练作业的时间弹性减少碳排放，并控制完成时间损失？

**方法（SC）。** 按单位能量进度调节卡数，用双层队列、历史碳强度和功率估计安排任务；保存与重启计入JCT。

**边界。** 优化碳排放/JCT，不是外部时变硬功率控制；791个作业与Pollux/Zeus对照的150个兼容子集须分开。

**证据定位（非本项目实测）。**
- LC02-E1：§4、§8.1，PDF pp.6/10；功率计量、CIC与资源
- LC02-E2：§§5–7，PDF pp.6–10；弹性与双层队列、保存宽限
- LC02-E3：§8.1–8.3，PDF pp.10–12；作业分母及结果

**实际阅读。** 复用AN023；本轮复核§§4–8（本地PDF pp6–12）：计量、弹性、双层队列、checkpoint、实验集合和局限。

**继续追问（AI）。** 固定容量中的延期与积压怎样共同限定可调空间？已有状态开销计账不能当新颖性。

## LC03 · SPADE · OSDI 2026

[分析包](../../../papers/lechowicz_2026_spade/analysis_breadth_v01.md) · [本地PDF](../../../papers/lechowicz_2026_spade/paper.pdf) · [来源](https://www.usenix.org/conference/osdi26/presentation/lechowicz)

**问题。** 外部信号变化时，DAG里哪些任务值得等待，哪些必须推进以免阻塞下游？

**方法（SC）。** 相对任务重要性与信号阈值联合决定调度/暂缓，并动态供给executor；用SAP分离任务选择与供给的贡献。

**边界。** CPU Spark DAG；信号感知与硬执行分开。51VM/100executor，时间压缩与利用率能量模型；整体makespan接近仍可牺牲尾JCT。

**证据定位（非本项目实测）。**
- LC03-E1：§2.1，PDF p.4；软目标与硬执行层
- LC03-E2：§3/Alg.1、§4，PDF pp.6–8；相对重要性与SAP
- LC03-E3：§5.1、§5.3/Table2/Fig6，PDF pp.9–11；51VM/100executor、计量、重复、尾部

**实际阅读。** 复用AN024；本轮复核§2.1、§§3–5，PDF pp4、6–11：硬/软语义、原型、lookahead与尾部。

**继续追问（AI）。** 平均/整体指标接近时，等待损失是否集中于某些依赖位置或服务等级？GPU迁移尚未开展。

## S02 · Phoenix现场演示 · Nature Energy 2026；在线2025

[分析包](../../../papers/colangelo_2025_grid_interactive/analysis_v01.md) · [本地PDF](../../../papers/colangelo_2025_grid_interactive/paper_preprint.pdf) · [来源](https://www.nature.com/articles/s41560-025-01927-1)

**问题。** 电力公司指定降载窗口与斜坡后，AI作业能否按事先约定的退化等级参与？

**方法（SC）。** 功率—吞吐画像、Flex等级、预知事件窗口与控制动作，结合限功率、暂停和卡数调整。

**边界。** 本地读arXiv v1，期刊版仅核书目；允许3–6h窗口内平均吞吐退化，不等于在线请求尾延迟无损。GPU功率不等于设施进线功率。

**证据定位（非本项目实测）。**
- S02-E1：arXiv v1，§§2.1、4.1，表 1，作业组合与 Flex 合约
- S02-E2：arXiv v1，§§2.2–2.3，图 2–5，事件、基线与性能
- S02-E3：arXiv v1，§§3.3–3.5，图 6，动作代价、假设与场地边界
- S02-E4：arXiv v1，§§4.2–4.4、§5，策略、测量与实现
- S02-E5：作者工件 README 的 Data、AI Orchestration Usage、Emerald AI Conductor Pseudocode
- S02-E6：data/SRP_exp_performance.csv 的 pt_mpt_13b_sm 记录及 Flexibility 列，图 2 图源
- S02-E7：控制端的 checkpoint 与 dvfs_set_power_cap；补丁中的 EmeraldCallback.batch_end、save_interval_fn 和 control_power.py

**实际阅读。** 复用AN002；本轮复核预印本§§2–5与服务档位、事件/计量、checkpoint低频假设；沿用既有部分工件证据。

**继续追问（AI）。** 短于原演示的窗口或更严格服务组合下，动作可行空间怎样变化？这是待检验联系，不是实测缺陷。

## LP07 · ML.ENERGY Benchmark · NeurIPS 2025 Datasets and Benchmarks

[分析包](../../../papers/chung_2025_ml_energy_benchmark/analysis_broad_v01.md) · [本地PDF](../../../papers/chung_2025_ml_energy_benchmark/paper.pdf) · [来源](https://proceedings.neurips.cc/paper_files/paper/2025/hash/9dc510e3d7b0b3b2a58ffed7a3ad6b0f-Abstract-Datasets_and_Benchmarks_Track.html)

**问题。** 怎样对生成式服务测GPU能量，并按应用延迟选择配置？

**方法（SC）。** 扫描模型、设备、并行和batch，测稳定期每token能量并归账，构建时间—能量前沿；附录比较换出/重算、TP与PD。

**边界。** 默认稳定饱和区间、GPU计量；平均TPOT不等于请求尾部合同。附录已研究抢占与通信能量。

**证据定位（非本项目实测）。**
- LP07-E1：§§2.1–2.4、§3.1–3.3，PDF pp.2–5：边界、稳定期及配置前沿
- LP07-E2：§4.1–4.3，PDF pp.6–10，Fig3–7：设备、结果与解释
- LP07-E3：Appendix B.1–B.4，PDF pp.23–25，Fig8–9、Table4–5：抢占/通信/PD/chunk
- LP07-E4：Appendix E，PDF p.28：作者局限

**实际阅读。** 正文§§2–6，PDF pp2–10；Appendix B/C/E重点pp23–28；未审完整checklist、源码或日志。

**继续追问（AI）。** 稳定期配置排序在包含启动、低负载、排空和恢复的完整有限窗口里是否仍成立？

## RP02 · MLPerf Power · HPCA 2025

[分析包](../../../papers/tschand_2025_mlperf_power/analysis_broad_v01.md) · [本地PDF](../../../papers/tschand_2025_mlperf_power/paper_arxiv_v2_hpca2025.pdf) · [来源](https://ieeexplore.ieee.org/document/10946778/)

**问题。** 不同规模ML系统的性能、功率和能效应怎样统一边界与报告？

**方法（SC）。** 按执行窗口对齐性能与功率；不同场景用外部分析仪或节点遥测，配合质量标准；分析行业提交结果。

**边界。** 执行期排除部分初始化等；整系统仍不完全包含冷却/存储。1841项提交是观察样本，不能当单机制随机因果实验。

**证据定位（非本项目实测）。**
- RP02-E1：§III-C、§IV-A，PDF pp.5–6：误区、窗口和边界
- RP02-E2：§IV-B–D，PDF pp.6–8：分析仪、遥测、估计及报告
- RP02-E3：§V-B/C/F，PDF pp.9–12：规模、长度换算和非随机提交结果
- RP02-E4：§III、§V-C、§VI，PDF pp.4/10/12–13：覆盖与未来方向

**实际阅读。** 正文§§II–VII，PDF pp3–13：边界、仪器、提交比较、局限；本地arXiv v2带HPCA标识，未逐字比终版。

**继续追问（AI）。** GPU/整机与不同窗口会不会改变策略排序？至少60s聚合不能证明短时硬功率履约。

## RPW01 · GreenLLM（观察项） · 作者机构库标DAC 2026，出版待核

[分析包](../../../papers/liu_2026_greenllm/analysis_broad_v01.md) · [本地PDF](../../../papers/liu_2026_greenllm/paper_author_dac2026.pdf) · [来源](https://infoscience.epfl.ch/entities/publication/5a812f00-c322-4ff8-a5b4-a890db0bbf93)

**问题。** 按prefill/decode的不同服务要求，在变化流量中怎样调频节能？

**方法（SC）。** prefill队列/长度模型，decode粗细双环反馈、滞回与限速。

**边界。** 仅作者稿；8×A100；DOI/ISBN占位、能量边界与模型公式待澄清。高负载SLO达标率下降，不是全设置零违约。

**证据定位（非本项目实测）。**
- RPW01-E1：§3.1–3.2，PDF pp.3–4：phase-aware建模、双环与滞回
- RPW01-E2：§4/Table2–3，PDF pp.4–5：8卡与负载
- RPW01-E3：§5.1–5.3/Table4–5，PDF pp.5–6：通过率和负载边界

**实际阅读。** 作者稿§§2–6，PDF pp2–6；元数据未完整核验，不计正式20篇主池。

**继续追问（AI）。** 已有phase-aware和双环DVFS，不能把分阶段/滞回本身当成待添加的新机制。

## 对读后的限定

ML.ENERGY选择软件GPU边界以便可扩展部署，MLPerf Power强调系统边界；目标不同，不构成谁“测错了”的证据。稳态、执行期和完整事件窗口各回答不同问题。要判断动态调度，须先明确要解释哪个窗口、哪些组件和何种同等服务。

GREEN已计入保存/恢复，Phoenix在其低频长训练条件下认为开销可忽略；这不是直接矛盾。SPADE明确软目标与硬执行分开。跨设置是否出现新的失败机制仍未知。GreenLLM的phase-aware与滞回已存在，正式身份待补，不并入主矩阵的正式来源计数。
