# 时变 GPU 配置与画像有效性：近邻定点核查

日期：2026-11-10，Asia/Shanghai。阶段：`analyze-paper` 补证据，供本轮 Mode C 边界审计使用。状态：`partial`。

上游：[Kareus AN-20261105-032-v01](../../../papers/wu_2026_kareus/analysis_broad_v01.md)；执行 [analyze-paper 项目快照](../../../standards/sources/2026-11-05/skills/analyze-paper/SKILL.md) 与 [Mode C 项目快照](../../../standards/sources/2026-11-05/skills/deep-read/SKILL.md)。本记录只补近邻与实现入口，不是完整 ANALYSIS_PACKAGE、创新判定或实验结果；新来源进入矩阵前仍需由主任务检查交接字段和证据覆盖。

问题限定为：外部 GPU 功率额度变化并实际触发限频时，已有计算—通信重叠画像能否继续选择合适策略，重画像成本是否值得。供电仅作外部输入，研究对象是运行时、性能模型与通信调度。未访问服务器，未克隆、安装或运行论文代码；未做任何校验哈希。

## 检索与阅读边界

检索词包括 `Kareus osdi26ae artifact`, `Perseus energy straggler profiling`, `PowerFlow GPU online profiling`, `GPU scheduling DVFS runtime contention profiling 2026`, `"DEFT" "Joint Task Placement" ICS 2026`。以下只引用会议/作者论文、作者代码与作者单位记录。检索非穷尽，不能由未检出推出不存在。

| 来源 | 本轮实际阅读 | 未完成 |
|---|---|---|
| Kareus，OSDI 2026 | 复用既有正文包，补核本地 PDF §4.5、§6.6–6.7、Appendix A；读取 `osdi26ae` 分支三份 README、`model_config.py` 和 `run_kareus.sh` | 未审完整调度器/频率控制调用链、未运行工件 |
| Perseus，SOSP 2024 | 作者 arXiv v3 正文 §2.3、§3.1–3.2、§5、§6.5；当前官方 Zeus PFO 文档入口 | 未审核心源码；当前文档与 2024 论文实现的一致性待核 |
| DEFT，ICS 2026 | 作者 arXiv v1 正文 §3–6 定点核验；作者单位发表记录 | 作者工件地址未知；未读出版社排版版、未独立复现 |
| PowerFlow，2023 作者稿（补充反证） | arXiv v1 正文 §1、§4、§5.1 定点核验 | 本轮未核正式发表身份与作者代码，不补写为正式成果 |

本表四项均已超出摘要初筛，但都不是全文逐式审计。DEFT 是本轮新增近邻，另已形成 [AN-20261110-002-v01](../../../papers/chen_2026_deft/analysis_v01.md)，仅向矩阵交付已有处理和条件边界。Kareus工件核查另纳入 [AN-20261110-001-v01](../../../papers/wu_2026_kareus/analysis_constraints_v01.md) 的E09；本检索记录本身不替代标准包。

## 已有处理及对本项目的约束

### NN-K：Kareus

- **DE，既有包 LP06-E01/E02/E04**：频率、通信 SM 分配和启动时序的联合搜索已经是核心方法；§4.5 已含顺序执行回退；§6.7 已评估热稳定、冷却与测量窗口。因此“联合优化”“加温度处理”“考虑 profiling 成本”不能直接当新意。
- **DE，NN-K01，Appendix A，PDF 页序 19**：恒频节能论证的假设 3 是相同平均频率对应相同完成时间。其成立范围值得核查，不能把条件定理扩展成所有功率限频轨迹的事实，也不能先断言它已被本项目推翻。
- **DE，NN-K02，源码定点**：[工件 README](https://github.com/ml-energy/kareus/blob/osdi26ae/tests/artifact/README.md) 面向两机 16 张 A100；[toy README](https://github.com/ml-energy/kareus/blob/osdi26ae/tests/toy/README.md) 面向单机四张 A40、PP2/TP2。两者都不是两机各三张 3090 的直接执行配方。
- **DE，NN-K03，源码定点**：[GPU_CONFIGS](https://github.com/ml-energy/kareus/blob/osdi26ae/tests/bayesian/common/model_config.py) 只登记 A40/A100，含频率范围与阻塞 P2P 静态功率参数。RTX 3090 的参数、控制权限与软件兼容性均为 **UN**。
- **DE，NN-K04，源码定点**：[toy/run_kareus.sh](https://github.com/ml-energy/kareus/blob/osdi26ae/tests/toy/run_kareus.sh) 根据输出文件存在性跳过 BO/CSV/优化阶段，再加载已生成方案。由此只能确认此脚本的复用路径，不能断言整个运行时没有在线适应。核心分区/调度路径见 [kareus README](https://github.com/ml-energy/kareus/blob/osdi26ae/kareus/README.md)，本轮未逐文件审查。

来源：[官方论文](https://www.usenix.org/system/files/osdi26-wu-ruofan.pdf)、[正式记录](https://www.usenix.org/conference/osdi26/presentation/wu-ruofan)。代码版本采用作者 `osdi26ae` 分支，读取日期见文首；本轮没有固定本地源码快照。

### NN-P：Perseus

**SC/DE，NN-P01**：§3.2 在训练初始阶段现场画像并异步构建时间—能量前沿；§3.1、Table 1 在收到 straggler 延迟/程度通知后换频率计划，恢复正常也有接口。§2.3 明确包括功率/温度限速等 straggler 成因。§5、§6.5 讨论画像与求解开销。因此不能称其“纯离线且无法应对运行变化”。当前读到的是时间约束/straggler 适应；外部时变瓦特额度下完整画像失效检测的覆盖为 **UN**。

定位：[作者 v3 正文](https://arxiv.org/html/2312.06902v3) §2.3、§3.1–3.2、§5、§6.5；[官方 PFO 文档](https://ml.energy/zeus/optimize/pipeline_frequency_optimizer/)，可追到 [Zeus 作者仓库](https://github.com/ml-energy/zeus)。本轮仅核文档，未以当前工具接口替代论文版本实现。

### NN-D：DEFT

**SC/DE，NN-D01**：§3.2 联合考虑任务放置、数据搬运和逐任务 DVFS，并处理就绪任务争用；§4.4 显式建模非对称切频代价。§3.4 每应用做一次 Nsight 画像并跨执行摊销，输入相关任务行为/动态生成 DAG 留作未来工作；§6 已含均一频率及逐任务频率对照。不能把“运行期联合调度/切换成本/画像摊销”概括成空白。外部时变硬功率额度并非已读公式的明确输入，是否有其他路径覆盖为 **UN**。

定位：[作者 v1 正文](https://arxiv.org/html/2608.02122v1) §3.2–3.4、§4.4、§5–6。正式身份由 [Chalmers 官方记录](https://research.chalmers.se/en/publication/553401) 确认为 ICS 2026，pp.1115–1127，DOI `10.1145/3797905.3807847`。实际阅读版本是作者 v1，不应误标为只存在预印本；本轮 DOI 页面未能打开。未找到可确认的作者实现地址，**不等于没有开源**。

### NN-F：PowerFlow 对“不会更新画像”的反证

**SC/DE，NN-F01**：§5.1 已在训练期间逐迭代收集吞吐和能量，用在线与离线数据持续拟合模型；§4 显式包含频率、GPU 数与通信重叠的性能关系。§1 把目标预算定义为一段时间的总能量，文中另有 power limit 措辞；本轮不把两者混同，也未核出面向外部时变硬功率曲线的保证。单纯增加“在线重新拟合”不足以建立差异。

定位：[作者 v1 正文](https://arxiv.org/html/2304.06381v1) §1、§4、§5.1；正式版本与代码位置仍为 **UN**。

## 给本轮诊断的边界提示（AI，尚非 Idea）

1. 首先区分“控制器选错目标频率”与“在实际频率已知后，旧画像仍选错重叠策略”。固定默认方案是弱对照；至少应审查按实测频率重新查询/插值旧画像能否解释差异。外部 cap 没有实际触发限频时，不能解释为功率约束下的失配。
2. 不能只比较同平均频率后报告一次时延差：热状态、任务输入、通信争用、频率观测窗口、切频开销与传感器刷新均可能混杂。哪些量可控、可测尚待本机核验。
3. 即使旧画像排序错误，也还缺“重画像获得的选择改进能覆盖测量、探索与切换成本”的证据。这里仅标出可区分的问题，具体对照与停止阈值交给本轮 Mode C → discover-innovation。
4. 六卡条件应限制证据主张：单分区/小训练机制诊断可作为起点；不能把四 A40 toy 跨机搬到 3090 后称为原规模完整复现。当前没有确认 baseline、最终题目或新颖性。

下游入口：主任务增量来源包/Mode C；本文件不单独更新推进台账，不产生实验记录。跨领域迁移本轮不适用：只核计算系统近邻，未选外领域方法 B。
