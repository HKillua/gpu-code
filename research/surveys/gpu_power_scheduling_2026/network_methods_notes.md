# 动态供电上限与 GPU 调度：网络、跨地域与评估方法补充

检索截止：2026-11-03。按新约束收敛：两台服务器、每台 3 张 RTX 3090；供电上限是外生时变输入，不锁定风光；任务有紧急与可延迟两类；研究计算机系统与通信，不研究电网控制。以下为有界证据检索，不能证明文献穷尽或“首次”。论文事实、作者报告的结果与拟研究假设分别标注。主要来源为 USENIX、arXiv、作者仓库及官方数据页。

## 可以直接给主报告的判断

1. **跨地域追绿不宜作为这套硬件的首选题目。** 到 2026 年 10 月，缓存感知碳路由、排队/continuous batching 感知的碳路由、可再生窗口中的跨站训练均已有直接相关工作。两台本地机器能校准端点计算和状态传输开销，但不能验证真实 WAN、异地容量与区域电力的联合效应。
2. **更匹配硬件的候选是“动态 cap 下，功率分配与跨节点通信/任务放置的耦合”。** 例如，cap 改变计算阶段时长、同步等待及两个作业的通信重叠，原来最好的放置/相位安排可能失效。此处的“可能”是待检验机制，不是文献已经证明的事实。
3. **“恢复债务”“公平”“预测”本身都不足以主张新颖性。** OAK 已显式处理等待年龄与恢复开销；CASSINI 已处理通信相位错开；PowerSlider 已处理动态 cap、Flex SLO 与 KV/阶段控制；Beyond Scalar Flexibility 已讨论持续时间、可靠性与负荷反弹，Execution-Feasible Dispatch 已处理 checkpoint 与可行 3D 模式。要先找到这些方法在本地动态 cap 场景下可复现的具体失败现象。

## RQ1：行业需求能支持到哪里

- **跨地域 GPU/数据协同放置确有生产动机。** MAST（Arnab Choudhury 等，OSDI 2024）是 Meta 的全球训练调度系统，处理区域 GPU 供需不均和数据共置；这证明地域协调有实际价值，但 MAST 的目标不是低碳，也不证明频繁迁移训练一定划算。[USENIX 正式页面](https://www.usenix.org/conference/osdi24/presentation/choudhury)
- **动态功率预算已进入厂商系统接口。** NVIDIA 2026 年的 Dynamic Power Software 文档区分域预算、节点预算及 GPU 功率上限，明确域预算是上界而非必须用满的目标；这可支撑“计算系统需要应对变化的功率约束”，不等于其功能在 3090 上可直接复现。[官方文档](https://docs.nvidia.com/datacenter/dps/versions/latest/guides/concepts/power-reservation-steering/)
- **网络放置与功率约束有合理工程联系。** NVIDIA Topograph 官方技术文指出放置不佳会增加共享链路流量、让 GPU 等待数据；这是产业动机，不能代替受控实验或独立性能证据。[官方技术文，2026-09-22](https://developer.nvidia.com/blog/topology-aware-workload-scheduling-with-nvidia-topograph)

## RQ2：优先阅读的 10 项及边界

| 文献/资源（元数据已核验） | 已经做到什么 | 对选题的限制与可用性 |
|---|---|---|
| **PowerSlider: Exploiting Phase Asymmetry for LLM Serving under Demand Response**；Yueying Li、Jiayang Chen、Yuanfan Chen、Leo Han、Haoran Qiu、Esha Choukse、Rodrigo Fonseca、Udit Gupta；2026-08-22 预印本。[论文](https://arxiv.org/abs/2608.21719) | 动态 cap 下把 prefill/think/answer 的性能功率差异、Flex SLO、KV 控制纳入在线决策。 | 已覆盖许多“按紧急度牺牲非紧急请求”的直观方案。全文检索未找到 forecast 一词，但这不是其没有任何前瞻机制的充分证明；必须以具体控制状态和目标比较，不能仅凭关键词宣布空白。 |
| **OAK: Restart- and Age-Aware Scheduling for Distributed Machine Learning on Shared GPU Clusters**；Khaled Aljbab、Amine Barrak；2026，arXiv v2 2026-09-22，页面列 ICPADS 2026。[论文](https://arxiv.org/abs/2609.19024) | goodput 之外显式加入累计等待与分解后的 checkpoint/restart 因子；作者用 12-GPU 仿真及 4-V100 硬件验证。 | “恢复+公平”已经存在。其故障注入场景不等于外生连续 cap 事件，但后者的差异必须表现为新的失败机制或约束，不能只改背景。 |
| **CASSINI: Network-Aware Job Scheduling in Machine Learning Clusters**；Sudarsanan Rajasekaran、Manya Ghobadi、Aditya Akella；NSDI 2024。[正式页面](https://www.usenix.org/conference/nsdi24/presentation/rajasekaran) | 根据通信周期的兼容性进行放置，并用时间偏移错开共享链路上的通信阶段。 | 通信感知 placement、phase shifting 已有强基线。论文主要假设 GPU 不被多个作业共享；动态 cap 改变周期后的适配是否造成问题，需实测。 |
| **Context Is Not Free: Carbon-Aware LLM Routing for Multi-Turn LLM Web Services across Geo-Distributed Data Centers**；Xiaoyang Zhang、Yuejun Yan、Qingang Zhang、Yonggang Wen；2026-10-26 预印本，系统 CarbonRouter。[论文](https://arxiv.org/abs/2610.35230) | 多轮会话跨模型/GPU/区域切换时计入历史重算碳成本，并避免来回切换。 | 作者明确承认：trace-driven simulation，能量/延迟来自文献构造的分析模型，回答质量是预测；网络延迟、排队及跨区域 KV 转移尚待建模。故“带缓存跨区路由”不新，但实测通信/排队与状态搬运仍是可辨别差异。[限制段](https://arxiv.org/html/2610.35230v1#S6) |
| **Dissecting the Carbon-SLO Tension in Geo-Distributed LLM Serving**；Burak Topcu、Musa Cim、Zuhal Ozturk、Mahmut Taylan Kandemir；2026-10-10 预印本。[论文](https://arxiv.org/abs/2610.14571)；[作者代码](https://github.com/topcuburak/carbon-slo-at-geoscale) | 实测校准的推理仿真，覆盖连续批处理、KV 容量导致的排队、阶段功率、时延 SLO 与 ILP/MILP。 | 比仅用“每请求固定功率+固定延迟”可信，但作者仓库采用 A100-40GB/vLLM 0.6.3 的拟合系数；原始实测日志未随仓库公开，RTT 使用 CloudPing 的区域 p50。不能直接套作 3090 实测结论。 |
| **Distributed LLM Pretraining During Renewable Curtailment Windows: A Feasibility Study**；Philipp Wiesner、Soeren Becker、Brett Cornick、Dominik Scheinert、Alexander Acker、Odej Kao；2026-02-26 technical report。[论文](https://arxiv.org/abs/2602.22760)；[代码](https://github.com/exalsius/curtail-llm) | 三个站点、每站四 A100，561M nanochat，Flower 协调本地训练与多站同步。 | 地域碳 trace 不一定对应机器物理位置；用 MOER<100 gCO2/kWh 作为弃电代理，并选择较有利地区。窗口不是直接观测到的站点弃电。作者报告每轮约 115 秒非训练开销，说明窗口长短很关键；不能把减排百分比外推至 6×3090。[全文](https://arxiv.org/html/2602.22760v1) |
| **The Effect of the Network in Cutting Carbon for Geo-shifted Workloads**；Yibo Guo、Amanda Tomlinson、Runlong Su、George Porter；2025-04-18 预印本。[论文](https://arxiv.org/abs/2504.14022) | 用物理网络路径估计 WAN 数据迁移碳成本，联合计算与传输时机。 | 网络功耗按带宽份额归因，非直接测得的边际耗电；作者明确未计 WAN 带宽约束及目标站点剩余容量。可以复用为“迁移代价不能省略”的依据，不能把估计当物理实测。[限制段](https://arxiv.org/html/2504.14022v1#S7) |
| **On the Limitations of Carbon-Aware Temporal and Spatial Workload Shifting in the Cloud**；Thanathorn Sukprasert、Abel Souza、Noman Bashir、David Irwin、Prashant Shenoy；EuroSys 2024。[论文](https://doi.org/10.1145/3627703.3650079)；[复现包](https://zenodo.org/records/10790855) | 123 区域、三年碳强度，分析 batch/interactive 的时空迁移收益上界；简单方法常已获得主要收益。 | 应先做 oracle 上界和静态低碳/简单贪心基线，避免为了复杂算法人为制造高收益。不是 GPU 端动态 cap 的完备模型。 |
| **Uncertainty-Aware Decarbonization for Datacenters**；Amy Li、Sihang Liu、Yi Ding；HotCarbon 2024。[正式 PDF](https://hotcarbon.org/assets/2024/pdf/hotcarbon24-final84.pdf)；[arXiv](https://arxiv.org/abs/2407.02390) | 用 conformal prediction 处理碳强度预测不确定性，并作时空移载案例研究。 | “把点预测变成区间”不是新贡献。论文自身脚注承认案例不能量化真实系统实现收益；将预测区间转成 GPU 准入/恢复决策仍需系统实验。 |
| **公开 workload + forecast 资源包**；Alibaba、Microsoft、NESO，详见下节。[Alibaba](https://github.com/alibaba/clusterdata)、[Philly](https://github.com/msr-fiddle/philly-traces)、[NESO 历史预测](https://www.neso.energy/data-portal/day-ahead-wind-forecast/historic_day_ahead_wind_forecasts) | 可构建可公开复现的工作负载、cap 波形和预测误差实验。 | 没有一个现成组合同时提供“同一 GPU 集群真实电力 cap、预测发布时间、逐请求 SLO、checkpoint/通信/功率实测”。拼接资源必须公开转换规则，不能称联合生产 trace。 |

额外的近邻避让：

- **Beyond Scalar Flexibility: From Eligible AI Workloads to Dependable Load Relief**；Meiyi Li；2026-09-04 预印本。从 GPU trace 重建功率和可调节性，已讨论持续时间、可靠性、组合相关性。它不是公开的生产 GPU 实测功率 trace。[论文](https://arxiv.org/abs/2609.05406)
- **Execution-Feasible Dispatch of LLM Pre-training Loads for Renewable-Dominant AI Data Centers**；Xi Tong、Ershun Du、Haiyang Jiang、Long Chen、Qin Wang；2026-10-09 预印本。将 checkpoint 域工作量映射为可行 3D 并行模式和日前调度；它偏电力调度，不宜照搬为本课题，但“checkpoint 约束+灵活性”已经有明确先例。[论文](https://arxiv.org/abs/2610.13047)
- **GreenAccounter: A toolkit for carbon-aware orchestration of deep learning workloads in geo-distributed clouds**；Jeonghyeon Park、Jaekyeong Kim、Wonseok Son、Sejin Chun；SoftwareX 34 (2026), 102550。已有 checkpoint 跨区迁移工具；仓库标注 demonstration purposes，依赖外部 API/凭证，不能当开箱即用的完整 benchmark。[论文](https://doi.org/10.1016/j.softx.2026.102550)、[作者仓库](https://github.com/datascience-labs/GreenAccounter)
- 通信选题另可对照 **Carbon-Aware Temporal Data Transfer Scheduling Across Cloud Datacenters**（Elvis Rodrigues、Jacob Goldverg、Tevfik Kosar，2025，LinTS），避免把“按碳强度延后传输”本身作为创新。[论文](https://arxiv.org/abs/2506.04117)

## RQ3：适合两节点六卡的可复现候选

### 主候选：动态 cap 引发的计算—通信相位变化与放置/功率协同

**可检验问题：** 在同样的 cap 波形和 urgent/nonurgent 到达流下，分别优化 GPU 功率、任务放置和通信相位，是否会相互抵消？例如给等待通信的 GPU 更多功率可能没有收益，而限制某一同步 worker 的功率可能拖慢整个训练组。后两项为合理机制假设，应先测，不应先写为结论。

**最小实验：** 两台机器各三卡。一个延迟敏感单卡推理服务和一个/两个可延迟训练作业；对比同节点 2 卡与跨节点 1+1 卡的训练放置，逐步扩展到 3/6 卡。先固定模型、并行策略、全局 batch 和训练目标，记录 cap×placement 下的计算/通信时间、实际功率、有效 token 或 training step 进度。不要一开始混入 TP/PP/DP 全组合。

**决策可限定为：** 节点预算和每 GPU cap 分配、是否维持当前放置、何时暂停/恢复一个可延迟作业。若跨节点迁移只在很长窗口才可摊销，算法应能选择“不迁移”。节点级非对称预算可以形成真正的 placement 权衡；只有全局均匀 cap 且通信很慢时，最优可能始终本地放置，必须接受这一负结果。

**必须比的基线：** 固定放置+均匀 cap；固定放置+按实测边际吞吐分配 cap；只做网络/局部性优化；只按紧急度抢占；带明确改造说明的 CASSINI 思路；小动作空间的离线 oracle 上界。若不存在多作业共享网络的实际冲突，不要硬套 CASSINI。

**候选贡献边界：** 新意需要来自可重复的耦合现象、可测状态估计和低开销决策的联合证据，而不是把已有两个算法相加。对照 PowerSlider、CASSINI、OAK，逐项说明哪些状态/约束不一样；不声称首次。

### 次候选：连续 cap 事件下，恢复顺序与紧急请求保护

**可检验问题：** 短恢复窗口结束前，恢复一个暂停训练作业或批处理任务会不会只付出启动/加载成本，却没有产生足够有效工作；多个作业同时恢复时，会不会因共享 CPU、存储或 NIC 干扰紧急服务？这些都必须在用户机器上确认。如果模型/checkpoint 完全驻留内存、没有共享瓶颈，问题可能很弱。

可比较“所有后台作业立即恢复”“只按年龄/优先级恢复”“恢复开销+预计窗口长度”“使用窗口长度预测分布的风险约束”。指标是紧急请求 goodput、后台 time-to-target/完成时间、每类 normalized slowdown、实际有效进度和恢复成本，不能仅用 GPU 利用率。OAK/Beyond Scalar Flexibility/PowerSlider 都是必须审查的 closest work；仅添加一个 debt 变量不足以构成贡献。

**两者共同的 go/no-go：** 先完成少量模型的 cap×placement 或 cap×恢复窗口测量。如果简单基线已经接近 oracle，或耦合效应小于测量方差，则收敛为测量/评估论文或换题，不继续堆复杂优化/RL。

## 数据与复现资源：哪些能用，哪些不能冒充真实 trace

| 资源 | 能提供什么 | 使用边界 |
|---|---|---|
| [Alibaba GPU v2020 / v2023 / v2025 / v2026](https://github.com/alibaba/clusterdata) | v2020 有两个月 GPU 工作负载；v2025 是 DLRM serving，不能称通用 LLM trace；v2026 是约六个月异构生产集群 workload/priority/utilization。 | v2026 公开表为小时粒度，网络只有 day109–115 的小时样本；时间是相对 day，没有日历日期。不能和真实气象/电力日期做“同地同刻”关联，也不能恢复逐 iteration 通信。其表并不直接提供 GPU 电功率。[v2026 官方 README](https://raw.githubusercontent.com/alibaba/clusterdata/master/cluster-trace-gpu-v2026/README.md) |
| [Microsoft Philly traces](https://github.com/msr-fiddle/philly-traces) | DNN 训练作业的公开匿名子集，适合 arrival/job-size 基线。 | 历史训练负载，与当前 LLM 推理及电力事件不配对；需要自己测服务曲线与恢复成本。 |
| [NESO Historic Day Ahead Wind Forecasts](https://www.neso.energy/data-portal/day-ahead-wind-forecast/historic_day_ahead_wind_forecasts) | 2018 至今的 day-ahead wind forecast；含目标 Datetime_GMT 与 **Forecast_Timestamp（预测实际制作时间）**，半小时 MW。 | 是真实发布的预测，比“给实际值加高斯噪声”更有说服力。预测对象为 transmission-connected wind，配对实际值时要匹配同一口径，不能与 embedded wind 任意相减。将全国 MW 归一化成 GPU cap 是实验映射，不是真实实验室供电记录。 |
| [NESO Historic Demand Data](https://www.neso.energy/data-portal/historic-demand-data/historic_demand_data_2023)；[官方 Carbon Intensity API](https://carbon-intensity.github.io/api-definitions/) | 官方风光 outturn/负荷；碳 API 有 national forecast/actual 字段、半小时数据。 | 不同字段可能是估计而非表计；API 历史 forecast 字段不自动代表任意历史决策时刻可获得的完整 forecast vintage。真实回测应保存 issue time，或用明确的日前版本。 |
| [Open Power System Data](https://www.data.open-power-system-data.org/time_series/) | CSV/元数据齐全的欧洲负荷、风光实际发电、容量等，可作公开波形。 | 不要把 load_forecast 列当 wind_forecast；逐列核对。数据包年代较早，适合可复现历史案例，不是“最新生产”证据。 |
| [NREL/NLR WIND Toolkit](https://nrel.sitefinity.cloud/grid/wind-toolkit) | 2007–2013 模型气象/计算风功率，包含多个 forecast horizon。 | 官方说明本身就是模拟气象与计算 turbine power，不应称实测风场输出。可作多地点相关性/敏感性数据。 |
| [carbon-slo-at-geoscale](https://github.com/topcuburak/carbon-slo-at-geoscale) | 推理仿真、ILP/MILP、配置、聚合结果和拟合参数。 | 先重新校准 3090。仓库是按真实时间运行的多进程仿真，作者称完整实验使用 64 核 CPU；小服务器并发过多可能使宿主机调度影响模拟时延。碳数据另受 Electricity Maps 条款约束。 |

对本课题，**优先使用直接设计的阶跃、斜坡、重复短事件、异步双节点 cap 波形**来识别因果机制，再用一两条公开电力波形测试外部有效性。手工生成的波形要明确标注为合成；它完全可以是有效系统实验，无需伪装成现实风光数据。若题目不做预测，就不必强行引入预测器。

## 评估方法与否证要求

1. **功率、能量、排放分开。** cap 是 W；能量是功率时间积分 Wh；碳是能量与碳强度积分。少用电或低平均功率不保证完成相同工作更省能，也不保证合规瞬时 cap。比较必须完成相同工作/达到相同模型质量，并包含暂停、checkpoint、恢复和排队拖延。
2. **cap 边界要一致。** NVML 只说明 GPU 板卡侧观测，不能自动代表两台服务器总输入功率。若论文声称 host/cluster cap，需墙插/PDU 或可验证的整机计量，计入 CPU、内存、NIC、风扇及未运行 GPU 的基线功耗；如果只能测 GPU，就把约束和结论明确限定在 GPU power budget。
3. **硬约束不要用平均值遮掩。** 报告峰值超限、超限持续时间/面积及控制响应时间；测量采样率应与控制周期匹配。功率 cap 指令写入成功不等于实际功率已在该时刻收敛。不要假设 3090 支持论文依赖的频率、MIG、power-gating 能力；先在实际机器查询。
4. **时序回测避免未来泄漏。** 决策只能使用当时已发布的 cap/预测/任务状态；train/validation/test 按时间切分；oracle 只作为上界。若有预测，报告偏差、相关误差、突降和持续时间误差，不只报告均方误差。直接给实际 trace 加独立噪声只是一个合成敏感性实验。
5. **恢复成本必须实测并有事件后观察期。** cap 恢复时立即结束实验，会漏掉后台积压、checkpoint 重载、缓存预热和随后紧急服务排队。观察到完成同样工作或明确稳定状态，报告总成本与服务分层结果。
6. **公平用完成的有效工作定义。** 同 GPU-seconds 或同 W 不意味着同训练进度。采用每类 SLO、相对独占基线的 slowdown、等待尾部/饥饿概率等；保持工作量和质量可比，不用总吞吐掩盖非紧急任务长期不完成。
7. **ACI 与 marginal emissions 不混同。** 平均碳强度适合描述归因足迹；如果声称某次移载“真实避免了多少排放”，需边际响应与反事实。WattTime 明确区分 AOER 与 MOER，且承认边际排放不是直接可测的量。不能把一种信号的下降称另一种因果效应。[数据定义](https://watttime.org/data-science/data-signals/)、[方法与验证](https://watttime.org/data-science/methodology-validation/)
8. **网络归因碳不是网络边际耗电。** 按字节×固定 kWh/GB 或 Pmax/链路容量分摊，可用于一致的归因模型，但“少传这些字节就省下该数值电能”还需要证明设备功耗随负载如何变化。[Guo 等方法](https://arxiv.org/html/2504.14022v1#S5.S2)
9. **避免固定服务器集合中的 sunk-carbon 优化误用。** 短期调度中把已制造硬件的全部隐含碳重新按使用量计入目标，可能改变调度却不改变该项实际排放；长期扩容/退役则是另一问题。[Bashir 等，SoCC 2024](https://arxiv.org/abs/2410.15087)
10. **跨域追绿何时无效需要主动展示。** 碳差小或区域排序稳定、目的地容量不足、迁移状态大、有效窗口短、WAN 带宽低/RTT 高、目标区排队、预测误差大时，收益可消失。固定在一个低碳区可能已经很好；应允许“维持原地”及“只调功率”的强基线获胜。[EuroSys 2024](https://doi.org/10.1145/3627703.3650079)、[CarbonRouter](https://arxiv.org/abs/2610.35230)

## 检索记录与证据状态

- 第 1 轮：`carbon aware geo distributed GPU training scheduling renewable energy WAN checkpoint migration 2025 2026 paper`；`carbon aware LLM inference geographically distributed routing KV cache 2025 paper`；发现跨地域/缓存相关候选。
- 第 2 轮：`carbon-aware training migration checkpoint 2025 2026`；`carbon WAN migration datacenter HotCarbon`；`carbon aware scheduling average marginal emissions pitfalls`；核验 2025–2026 论文、反例和方法限制。
- 第 3 轮：`Alibaba cluster trace GPU 2020 2025 github`；`NREL WIND Toolkit power forecasts actual`；`NESO historic wind forecast`；核验公开数据粒度、预测发布时间和仿真/实测区别。
- 用户收敛硬件后增补：`GPU dynamic power budget scheduling checkpoint restart fairness inference training 2025 2026`、`GPU power cap communication distributed training placement scheduling`，补查 PowerSlider、OAK、CASSINI。
- 已打开主要 arXiv/正式出版页及多篇 HTML 的方法/限制段；不是全部 PDF 的逐页精读，也未运行任何开源 artifact，不声称复现成功。
- 使用 research-lookup 技能的证据追踪与反证核查方法；本机无 parallel-cli，实际检索使用会话内置网页检索。方法工具出处：Timothy Kassis、Vinayak Agarwal、Yuhuan He、Darshil Patel、Aubrey M. Brueckner，*Scientific Agent Skills: A Library of Procedural Knowledge for Research Agents*，2026（元数据核验，非课题相关工作）。[arXiv](https://arxiv.org/abs/2609.00065)
