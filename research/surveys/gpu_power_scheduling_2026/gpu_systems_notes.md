# GPU 功率调度与弹性训练：独立检索笔记

检索日期：2026-11-03。边界：计算机系统，电力侧只提供随时间变化的 GPU/服务器预算；不研究电网潮流、发电机控制或电气设备设计。最新用户条件：2 台服务器，每台 3 张 RTX 3090；任务紧急程度不同；供电有上限和波峰波谷，尚未决定是否使用风光场景。

## 主要判断

1. 必须区分 **少用电（J）**、**固定预算内分配功率（W）**、**外部预算本身变化**。Zeus/Perseus/DynamoLLM 的成功不意味着已解决第三类；但截至本次检索，PowerSlider、PctoDL 已明确处理时变 cap，不能再把“动态限电 + DVFS + SLO”写成研究空白。
2. 六张消费级卡适合验证具体机制：预算下降/恢复时，抢占、模型重载、训练 checkpoint、跨节点同步以及队列积压如何互相放大。规模小本身不是贡献，应证明一个可泛化的失败机制，并给出可复现的条件和反例。
3. 推荐从整卡调度原型开始：在线请求 + 可延迟 batch；随后加入一个 2–4 卡 DDP 训练作业。先确定真实失败机制，再选算法。混部、优先级、弹性、fairness、cache-aware admission 都有大量近邻，不能只叠加关键词。

## 两轮检索记录

第一轮广搜关键词：`Zeus Chase Perseus GPU energy power scheduling training 2025 2026`、`Dynamo GPU power management dynamic power budget inference`、`GPU power budget training dynamic power cap scheduling`、`LLM inference power cap demand response`、`power budget elastic training`。筛出训练能耗、推理预算、弹性资源三个分支。

第二轮沿作者与原文跟进：USENIX Zeus/Pollux；Zeus 项目 research overview；arXiv 全文 Chase、Perseus、DynamoLLM、TAPAS、RAPID、PowerSlider；SOSP Sailor 官方日程和作者 PDF、GitHub/Zenodo artifact；PctoDL ACM DOI 和作者仓库；NVIDIA MIG/nvidia-smi/3090 官方文档。搜索摘要仅用于发现，以下方法与条件以论文全文/官方元数据为依据。未确认正式发表的工作保留预印本身份；未验证开源不等于未开源。

## 10 篇核心可比工作

| 工作及发表状态 | 任务、控制动作 | 信号是否时变/不确定 | SLO / 训练质量 | 实验规模与复现材料 | 对本题的边界 |
|---|---|---|---|---|---|
| **Zeus**, NSDI 2023 [1] | 周期性 DNN 训练；batch size + GPU power limit；在线探索与即时 profiling | 工作与数据可变；不是外部移动 power budget | time/energy-to-target-accuracy | 6 类 DNN、4 代 NVIDIA GPU traces；Zeus 库与模拟器开源 | “调功率省电”“batch 与 power 联合优化”均已有；可作能效基线 |
| **Chase**, ICLR 2023 Climate Change workshop [2] | 训练中按短期碳强度预测调整 power limit | 碳强度时变、可预测；不是可用电功率硬约束 | 不迁移/延期，权衡 carbon-to-accuracy 与 time-to-accuracy | ResNet-50/ImageNet；论文报告碳减 13.6%、时间增 2.5%；独立 artifact 未核验 | 碳信号不是供电预算；不能仅把碳强度换成发电量就声称新问题 |
| **Perseus**, SOSP 2024 [3] | pipeline 训练每个前/反向操作的频率；图割求 time–energy frontier | 适应 straggler；目标是去掉不影响进度的能耗 | 比较 iteration time，不改训练计算 | 实机含 4×A100、8-stage A40 与 16-GPU 3D 设置；大模型至176B为仿真；Zeus 集成 | 已覆盖流水线不均衡与慢节点节能；不等于外部逐时限电调度 |
| **Pollux**, OSDI 2021 [4] | 训练资源量、batch、学习配置协同调整 | 动态 GPU 分配；不建模外部功率 cap | goodput结合吞吐与统计效率；兼顾fairness | 实作+trace-driven仿真；AdaptDL 开源 | 变GPU数不能只比samples/s，还应量有效训练进展；弹性本身已有 |
| **DynamoLLM**, HPCA 2025（预印本2024）[5] | LLM推理副本数、TP、频率；层次控制与请求/负载预测 | 负载时变；主目标能耗/成本，不是移动供电上限 | 延迟SLO | 8×H100/server；1h实验基线12服务器；更大设置含仿真；独立代码未核验 | 名称不同于 NVIDIA Dynamo；不能把它称为固定配置，也不能误称已做外部动态cap |
| **TAPAS**, ASPLOS 2025 [6] | 云中VM放置、请求路由、实例重配置；温度/功率协同 | 负载/热条件/故障导致约束变化；不研究风光预测分布 | 常态保持SLO与质量；紧急情景含配置权衡 | Azure历史数据跨区域；真实实验80服务器，IaaS用历史功率回放，另有仿真 | 不能把热故障/供电故障下的优先处理视为全新；历史观测不是生产上线证明 |
| **Sailor**, SOSP 2025 [7] | 异构/跨地域训练的资源量与并行计划；重分区、异步checkpoint、kill-free重配 | GPU可用性变化；不优化供电 cap | 固定global batch的训练设置；吞吐/成本目标 | 云A100/V100；最多32×4 GH200；另有含RTX3090的异构集群；GitHub/Zenodo | 可借实现；重配仍从checkpoint恢复，并非零代价/无回滚 |
| **RAPID**, arXiv 2026-01，未确认会议 [8] | P/D分离推理；GPU角色和功率重分配 | 总预算固定、请求动态；不预测外部供电 | TTFT+TPOT共同达标的goodput | 8×MI300X；Llama-3.1-8B，4800W GPU总cap；vLLM0.8.4改造，独立仓库未核验 | 已做非均匀power与角色联合调整；单节点高速XGMI不能外推普通以太网 |
| **PctoDL**, ACM TACO 2026，作者仓库标Just Accepted [9] | 单GPU多租户DNN推理；MPS份额+batch+频率，MPC内环/Shadow Optimizer | 明确测试时变cap；模型残差修正 | 核心是受限功率吞吐；不可直接等同在线LLM token SLO | RTX3080Ti、A100；作者代码与profiling脚本 | 已覆盖“MPS+DVFS+动态cap”；3090与3080Ti接近仍须重新profile |
| **PowerSlider**, arXiv 2026-08，未确认会议 [10] | LLM的prefill/think/answer、SLO类、频率/池/KV联合控制 | 明确移动cap；每次事件重优化，未发现未来cap误差分布优化 | LC/Flex/BE；Flex是改变服务合同后的允许延迟 | 实机小规模+64–512 GPU仿真；SGLang；独立artifact未核验 | 已含admission、KV防抖动、排空重分配和恢复队列讨论；泛泛加这些动作会撞题 |

### 对 PowerSlider 的额外审计（避免过度声称差异）

其 §IV-D/V 按观测长度调整 admission/KV，重分配先排空，不重算 KV；深cap有 consolidation 与 NVML backstop。论文承认频率执行要数十至数百毫秒、固件响应不透明。§VI 延迟统计排除 warmup/draining，因此可进一步审计**完整事件期间**损失，但这只是研究线索，尚不能断言有缺陷。摘要与正文动态回放 goodput 数字不完全一致，本笔记不推荐引用单一headline收益。以上基于 [10]，不要将其512卡仿真写成512卡实机。

## 2025–2026 Zeus 生态更新

- **The ML.ENERGY Benchmark: Toward Automated Inference Energy Measurement and Optimization**，Jae-Won Chung、Jeff J. Ma、Ruofan Wu、Jiachen Liu、Oh Jun Kweon、Yuxuan Xia、Zhiyu Wu、Mosharaf Chowdhury；NeurIPS 2025 Datasets & Benchmarks Spotlight。40种模型、6类任务的推理能耗测量与优化工具，不是动态供电调度器。可作为测量方法和负载构建材料。[论文](https://arxiv.org/abs/2505.06371)；[作者项目总览](https://ml.energy/zeus/research_overview/)。
- **Where Do the Joules Go? Diagnosing Inference Energy Consumption**，Jae-Won Chung、Ruofan Wu、Jeff J. Ma、Mosharaf Chowdhury；arXiv 2026-01，未确认正式会议。测量H100/B200上46模型、7任务、1858配置，解释内存、利用率等中间机制。不能拿其中能耗倍数直接预测3090收益。[原文](https://arxiv.org/abs/2601.22076)。

## 对 2×3 张 RTX 3090 的具体可行性

官方产品页确认3090单卡24GB GDDR6X；六卡显存不是自动统一的144GB。不要因为GPU都是Ampere就假设MIG；官方MIG支持清单和“仅支持列出的设备”规则不包括3090。[3090规格](https://www.nvidia.com/en-us/geforce/graphics-cards/30-series/rtx-3090-3090ti/)；[MIG清单](https://docs.nvidia.com/datacenter/tesla/mig-user-guide/supported-gpus.html)；[MIG条件](https://docs.nvidia.com/datacenter/tesla/mig-user-guide/getting-started-with-mig.html)。

`nvidia-smi -pl`需要root，受设备Min/Max限制；不能先假定六卡同一最低功率或锁频能力。应先只读查询 `nvidia-smi -q -d POWER,CLOCK`、`nvidia-smi topo -m`，确认板卡、固件、PCIe/NVLink/跨节点网卡。NVIDIA文档说明Ampere非GA100的average功率是上一秒均值；高频轮询不等于高频独立测量，更不构成短时硬cap证据。[nvidia-smi官方说明](https://docs.nvidia.com/deploy/nvidia-smi/)。

建议固定推理模型/精度与质量，先整卡运行，避免将MPS干扰、量化质量、KV迁移、训练收敛同时引入。可用2张卡运行单卡可容纳的小模型在线推理，其余4张做可延迟批推理/小模型DDP；这是实验设计建议，具体模型长度/并发必须先验证24GB占用。跨节点以太网若较慢，优先同节点训练并把跨节点DDP作为受控压力项。没有外部电表时，严格称“GPU板卡功率预算”，不能把NVML总和称为服务器或数据中心整机功率。

## 候选问题与最小证伪实验（研究者推断，非已证明创新）

### A. 动态预算中的跨节点同步作业应如何让出与回收功率？

失败机理假设：急任务挤占某一节点预算后，DDP同步训练只要一个rank变慢就可能拖住所有rank；按GPU局部边际吞吐分配瓦数，可能给正在等通信的rank分配无效功率。恢复时直接扩容还叠加checkpoint/reload及通信重建代价。

最小实验：同一训练状态、global batch与数据顺序，比较同节点2卡、跨节点2卡、跨节点4卡；注入单节点/双节点cap下降、恢复和在线推理突发。记录每rank compute/collective/wait、迭代尾延迟、有效训练进度、checkpoint字节/时间、急任务SLO与全时段能量。基线至少有均分cap、急任务严格优先+后台暂停、Zeus/Perseus式profile能效、带切换成本的贪心。只有证实“局部供电分配→同步放大损失”在真实设备显著存在，才做job级gang与预算分配。需与Sailor、Perseus和父报告的混部近邻进一步对照；并行训练+限电不是自动新颖。

### B. 限电结束后的积压、重启与再次限电应如何共同决策？

失败机理假设：低预算期间严格优先急任务会积累后台债务；恢复时一股脑启动后台，导致模型重载/CPU数据管线/PCIe或网络竞争，急任务尾延迟反弹；若高预算窗口短于重启成本，反复启动浪费有效进度。

最小实验：相同cap谷值和总能量，但改变高预算窗口长度、再次降cap间隔、后台任务状态大小；计算完整事件+恢复窗口的累计有效工作，而非仅稳态吞吐。比较简单迟滞、最短剩余任务、截止期/aging、含切换成本的预测控制；再验证是否确实需要更复杂算法。新意必须落在“跨事件状态代价与高预算窗口利用”且证明已有策略失败，不能以priorities/fairness/admission为贡献。若长周期预算下简单迟滞已足够，停止该方向。

### C. 限电执行与测量的时间尺度是否使调度器的功率承诺失真？

机制假设：平均遥测、命令执行时延与同步尖峰叠加，造成调度账面满足cap而实际短窗超限。此题适合作为A/B必做验证，不建议在无高频外部仪表时独立做强保证论文。可贡献可复现的接口校准与安全余量机制；需与PowerSlider的NVML backstop、RAPID的先减后增控制以及PctoDL内环比较。

## 三种最容易撞题的“创新”

1. **“利用prefill/decode不同功率曲线，联合批次/频率以保SLO”**：DynamoLLM、RAPID和PowerSlider已经覆盖核心结构。把信号改成风光并不足够。
2. **“MPS切片+DVFS+MPC应对动态预算”**：PctoDL已直接覆盖，且有3080Ti代码；3090重实现通常只是工程复现。
3. **“紧急请求优先、后台训练弹性，加入KV/admission/fairness”**：Pollux/Sailor已有弹性；PowerSlider已有服务类别与admission；父调研另外发现Mosaic/LithOS混部近邻。需明确其预算/状态假设，找到可复现反例后再立题。

## 完整书目与原始URL

1. Jie You; Jae-Won Chung; Mosharaf Chowdhury. **Zeus: Understanding and Optimizing GPU Energy Consumption of DNN Training**. USENIX NSDI 2023, pp.119–139. [会议页](https://www.usenix.org/conference/nsdi23/presentation/you)；[论文PDF](https://www.usenix.org/system/files/nsdi23-you.pdf)；[代码](https://github.com/ml-energy/zeus)。
2. Zhenning Yang; Luoxi Meng; Jae-Won Chung; Mosharaf Chowdhury. **Chasing Low-Carbon Electricity for Practical and Sustainable DNN Training**. ICLR 2023 workshop “Tackling Climate Change with Machine Learning”. [元数据](https://arxiv.org/abs/2303.02508)；[全文](https://arxiv.org/html/2303.02508v2)；[作者PDF](https://symbioticlab.org/publications/files/chase%3Accai23/chase-ccai23.pdf)。
3. Jae-Won Chung; Yile Gu; Insu Jang; Luoxi Meng; Nikhil Bansal; Mosharaf Chowdhury. **Reducing Energy Bloat in Large Model Training** (Perseus). SOSP 2024. DOI 10.1145/3694715.3695970. [元数据](https://arxiv.org/abs/2312.06902)；[全文](https://arxiv.org/html/2312.06902v3)；[实现指南](https://ml.energy/zeus/optimize/pipeline_frequency_optimizer/)。
4. Aurick Qiao; Sang Keun Choe; Suhas Jayaram Subramanya; Willie Neiswanger; Qirong Ho; Hao Zhang; Gregory R. Ganger; Eric P. Xing. **Pollux: Co-adaptive Cluster Scheduling for Goodput-Optimized Deep Learning**. OSDI 2021. [作者书目](https://suhasjs.github.io/publication/pollux-osdi)；[USENIX原始论文集](https://www.usenix.org/system/files/osdi21_full_proceedings_interior.pdf)；[代码](https://github.com/petuum/adaptdl)。
5. Jovan Stojkovic; Chaojie Zhang; Íñigo Goiri; Josep Torrellas; Esha Choukse. **DynamoLLM: Designing LLM Inference Clusters for Performance and Energy Efficiency**. IEEE HPCA 2025. [正式版作者PDF](https://iacoma.cs.uiuc.edu/iacoma-papers/hpca25_2.pdf)；[预印本元数据](https://arxiv.org/abs/2408.00741)；[预印本全文](https://arxiv.org/html/2408.00741v1)。不要与[NVIDIA Dynamo产品](https://developer.nvidia.com/blog/?p=95274)混淆。
6. Jovan Stojkovic; Chaojie Zhang; Íñigo Goiri; Esha Choukse; Haoran Qiu; Rodrigo Fonseca; Josep Torrellas; Ricardo Bianchini. **TAPAS: Thermal- and Power-Aware Scheduling for LLM Inference in Cloud Platforms**. ASPLOS 2025, Volume 2. DOI 10.1145/3676641.3716025. [正式版作者PDF](https://iacomaweb.web.engr.illinois.edu/iacoma-papers/asplos25_2.pdf)；[全文](https://arxiv.org/html/2501.02600v1)。
7. Foteini Strati; Zhendong Zhang; George Manos; Ixeia Sánchez Périz; Qinghao Hu; Tiancheng Chen; Berk Buzcu; Song Han; Pamela Delgado; Ana Klimovic. **Sailor: Automating Distributed Training over Dynamic, Heterogeneous, and Geo-distributed Clusters**. SOSP 2025, pp.204–220. DOI 10.1145/3731569.3764839. [官方日程](https://sigops.org/s/conferences/sosp/2025/schedule.html)；[作者PDF](https://anakli.inf.ethz.ch/papers/sailor_sosp25.pdf)；[代码](https://github.com/eth-easl/sailor)；[归档artifact](https://zenodo.org/records/17007125)。
8. Yiwei Jiang; Sangeeta Chowdhary; Nathaniel Morris; Rutwik Jain; Srilatha Manne; Sam Bayliss. **Power Aware Dynamic Reallocation For Inference** (RAPID). arXiv 2601.12241, 2026-01-18. [元数据](https://arxiv.org/abs/2601.12241)；[全文](https://arxiv.org/html/2601.12241v1)。使用ICML模板不代表已被ICML录用。
9. Meng Hao; Zikun Wu; Xueyang Tian; Siyu Yang; Guotong Guo; Hongwei Yang; Hui He; Yiming Wang; Farui Wang; Desheng Wang; Weizhe Zhang. **PctoDL: Adaptive GPU Throughput Optimization for Deep Learning Inference with Power Constraints**. ACM Transactions on Architecture and Code Optimization, 2026. DOI 10.1145/3805802. [ACM](https://doi.org/10.1145/3805802)；[作者代码/书目](https://github.com/wuooo339/Pctodl)。ACM搜索正文可核验方法，直接DOI打开曾403；卷期页码未确定，不补写。
10. Yueying Li; Jiayang Chen; Yuanfan Chen; Leo Han; Haoran Qiu; Esha Choukse; Rodrigo Fonseca; Udit Gupta. **PowerSlider: Exploiting Phase Asymmetry for LLM Serving under Demand Response**. arXiv 2608.21719, 2026-08-22. [元数据](https://arxiv.org/abs/2608.21719)；[全文](https://arxiv.org/html/2608.21719v1)。

## 证据限制

本笔记未运行任何论文代码，开源仅表示发现对应仓库/归档；未验证六张3090实际驱动、权限、网络或功率接口。用户澄清之后，风光不再是默认必要条件。所有候选方向都是值得做先导实验的假设，不能作为“此前无人研究”的证明。行业实际需求、部署实例以及Mosaic/LithOS/FlexDC-Sim由其他并行调研覆盖，本笔记不重复据此声称生产收益。
