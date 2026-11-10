# 2025–2026 GPU 功率与能效新增阅读

本轮新增正式主池 4 篇，3 篇发表于 2025 年，1 篇发表于 2026 年。四篇 PDF 均已下载、可解析并核对首页。全部停留在摘要初筛，具体实验规模、效果数字和适用条件需后续逐篇读正文。

| 来源 | 正式发表 | 先读它可以理解什么 | 本地版本 |
|---|---|---|---|
| throttLL’eM | IEEE HPCA 2025 | 在线 LLM 推理的批量与 KV 缓存随请求变化时，怎样选择频率和实例规模以节约能量，同时满足响应目标？ | [PDF](../../../papers/kakolyris_2025_throttllem/paper_hpca2025.pdf) |
| MLPerf Power | IEEE HPCA 2025 | 怎样跨设备、跨任务测量机器学习系统能效，并保证比较口径清楚？ | [PDF](../../../papers/tschand_2025_mlperf_power/paper_arxiv_v2_hpca2025.pdf) |
| VAHRM | IEEE Transactions on Parallel and Distributed Systems, 36(8), 1713–1727 | 制造差异使相同型号计算资源的耗电不同，作业应该分给哪批资源才能兼顾能效与周转时间？ | [PDF](../../../papers/yoshida_2025_vahrm/paper_tpds2025.pdf) |
| FlipFlop | ACM/IEEE ICSE 2026, Research Track | 开发者怎样从 GPU 内核代码判断线程块配置对能耗和执行时间的影响？ | [PDF](../../../papers/rajput_2026_flipflop/paper_author_icse2026.pdf) |

## throttLL’eM

先预测后续 KV 缓存占用与批量，再用性能预测指导逐迭代调频和实例缩放。直接研究在线 GPU 推理能效。摘要的约束是服务质量；外部总瓦数时变的输入、可行性与越限保证需要后续正文核验。

研究项目网站公开的 IEEE 排版正式版；首页标 HPCA 2025 和完整 DOI。 [正式记录](https://ieeexplore.ieee.org/document/10946751/)

需要运行期状态信息、性能预测以及实例与频率控制能力；六卡机器的驱动和动作权限待设备核验。

## MLPerf Power

建立覆盖多个功率量级的机器学习能效基准方法，兼顾硬件、负载和系统相互作用。供电研究的测量近邻。重点是测量与比较能耗，调度器的功率响应需要另行评价。

论文已发表于 HPCA 2025；本地为作者公开 arXiv:2410.12032v2（2025-02-06），首页标 HPCA 2025。未逐字比对出版社终版。 [正式记录](https://ieeexplore.ieee.org/document/10946778/)

阅读时须继续查每种测试的系统边界、测量设备和精度；是否适配两机 3090 的整机计量条件待核验。

## VAHRM

先建立考虑制造差异的 GPU 功率模型，再将较节能的资源优先分给耗能大的作业，并考虑排队与周转时间。GPU 资源分配和能效的邻接工作。摘要目标含总能效和作业周转；外部动态功率额度尚待正文核验。

作者大学网站保存的 IEEE 期刊正式排版版，首页标 36卷8期、2025年8月、1713页，DOI 和出版日期齐全。 [正式记录](https://ieeexplore.ieee.org/document/11031465)

需要不同设备在各负载下的能效画像。六张卡是否存在对决策有意义的差异，需要测量才能判断。

## FlipFlop

静态分析 PTX 特征，建立性能与耗电模型，推荐时间–能耗折中较好的线程块配置。GPU 软件能效的邻接工作，控制对象为内核启动配置。集群级功率预算与请求优先级需要其他层级研究。

作者主页公开的 13 页版本，由 ICSE 2026 Research Track 官方条目链接；首页署名 Alex Brandt，官方会议作者名为 Alexander Brandt。本地版本未标出版社 DOI/版权页，不能当作出版社排版终版。 [正式记录](https://conf.researchr.org/details/icse-2026/icse-2026-research-track/265/FlipFlop-A-Static-Analysis-based-Energy-Optimization-Framework-for-GPU-Kernels)

需要获得内核 PTX 并校准硬件相关模型；对现有训练/推理框架中可修改内核的范围需后续确认。

## 待核验与阅读范围

GreenLLM 的 EPFL 机构库条目和公开稿均注明 DAC 2026，已取得 7 页作者稿。首页 DOI 与 ISBN 为占位内容，此轮未核实出版社条目，因此保留在观察列表。 [机构库记录](https://infoscience.epfl.ch/entities/publication/5a812f00-c322-4ff8-a5b4-a890db0bbf93) · [本地 PDF](../../../papers/liu_2026_greenllm/paper_author_dac2026.pdf)

PowerTrip 涉及跨站供电与通信开销，本轮核验到 arXiv 2025 预印本。 [原始条目](https://arxiv.org/abs/2507.17904)

初筛还识别出两项范围差异。细粒度 DVFS 分析模型发表于 ASPLOS 2025，但实测主体为 Ascend NPU；Revisiting Disaggregated 的会议归属指向 EuroMLSys 配套 workshop。完整记录见本组 JSON。

对两机六张 RTX 3090，throttLL’eM 提供在线状态与调频的阅读入口，MLPerf Power 提供测量口径，VAHRM 帮助理解设备能效差异，FlipFlop 提供内核配置的能效视角。上述关联是本轮分析判断，设备权限、计量和框架适配尚待核验。
