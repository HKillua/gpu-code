# 广读检索与纳入说明 v01

检索/核验日期：2026-11-05，Asia/Shanghai。阶段：`analyze-paper`。上游是已有领域入门、历史综述及 MC-20261105-001-v01；本轮扩展正式发表阅读池，不执行选题或实验。

## 纳入问题与范围

围绕四条问题链定向检索：

1. GPU 功率、训练能量和在线推理服务质量如何权衡？
2. 多作业调度如何处理排队、公平、异构、有效进度和重配？
3. GPU 计算、通信与资源放置/重配置如何相互影响？
4. 发电/能源信息如何进入资源供给与在线决策，其收益边界是什么？

优先使用正式系统、网络、体系结构、性能分析会议及相关期刊记录。主要覆盖 2018–2026；GreenHadoop 2012 仅作为历史源头。没有将 CCF 分级、引用量或影响因子作为已核验的质量指标，不承诺穷尽覆盖所有会议年份。

## 检索路径

- 从已有来源和各问题链代表作扩展。使用论文名与 power / energy / carbon-aware / GPU cluster scheduling / elastic training / communication scheduling / pause and resume 等主题词。
- 核验 USENIX 独立 presentation 页及其 BibTeX，ACM 官方程序/OpenTOC/DOI，IEEE 正式条目，NeurIPS 正式 proceedings，Nature 正式书目和作者所属机构记录。
- 出版社正文不可直接取得时，使用作者或作者机构公开 PDF；同时记录正式发表出处和实际阅读版本。作者仓库声明不代替出版社刊期记录。
- 四组来源链接分别保存在 power_energy.json、cluster_scheduling.json、network_elasticity.json、energy_signals.json；manifest_v01.json 汇总。本说明记录检索方法，不伪造穷尽数据库搜索日志或 PRISMA 流程计数。

## 筛选与去重

- 主池共32项正式研究：28篇会议论文、4篇期刊论文。会议中含 NeurIPS Datasets and Benchmarks 与 OSDI Operational Systems 类型，逐项注明。
- 仅预印本不纳入主池。已有 PowerSlider 笔记保留原身份；CarbonFlex、PCAPS 相关版本关系暂作核查线索。
- POMACS 全文与次年 SIGMETRICS 展示/两页摘要属于同一研究，不重复计数。Nature 正式版与早期 Phoenix 预印本同理。
- 直接能源调度与邻接的公平、通信、弹性工作分别标明。邻接论文的学习价值不等于已在供电场景验证。

## 阅读深度

- 13篇新正文定点阅读，关注方法、评估和边界，分别保存 partial ANALYSIS_PACKAGE。
- 1篇复用既有 Phoenix 预印本正文/部分工件分析，正式版仅书目核验，正式版修订差异仍待查。
- 18篇摘要/书目初筛，未将其升级为完整正文分析包或直接机制证据。
- 尚无代码复现、逐行证明核验、本机 GPU 测量或真实供电轨迹验证。

## 下游入口

本轮停留在来源分析阶段。后续先按导读逐篇讨论，针对相近问题选择3–8篇已读正文的来源，复核 P/F/M/E，再新增 Mode C 矩阵版本。初筛来源先补正文，不自动进入机制推断。当前没有目标失败机制与源方法 B，跨领域迁移阶段不适用；不据阅读池直接设计新算法。

## 资料限制

这是定向广读池，遗漏其他高质量来源是可能的。出版社访问限制、作者稿修订、工件可获得性以及原实验的统计与测量细节仍有待核验项。主池“正式发表”只表示发表身份已核对，不表示证据无缺陷或适合在六卡平台原样复现。
