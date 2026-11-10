# GPU/AI 可变功率调度：产业证据核验笔记

检索截止：2026-11-03。任务边界：计算机、通信、信息方向；当前可用实验条件为两台服务器、每台三张 RTX 3090。发电来源尚未确定，因此不预设离网、光伏直供或风电直供。

采用“先确认材料存在，再对照正文核验具体表述”的引用原则。下列十组证据均使用运营方、设备方、电力公司或研究作者的一手材料；官方新闻稿仍是当事方披露，不能自动视作独立验收。至少完成了三轮检索：国际部署初筛；2025—2026 年进展与原文反查；商业规模、国内实例及最新通信方向补查。

## 先回答产业需求

**需求确实存在，并且已经进入 AI 集群现场验证和商业试点。更稳妥的问题表述是“在外部给定、随时间变化的功率预算下，如何维持紧急任务服务并调度可延期任务”。** 它不要求研究者先证明某一种发电方式必然造成机房供电波动。Google 的最新材料把 ML 需求响应用于加快新机房接入；Emerald 已在真实 GPU 集群上执行降载；NVIDIA 已把接口纳入 DSX Flex 产品体系。证据见 E2—E5。

必须区分四种量，不能只说“电有波峰波谷”：

| 现象 | 随时间变化的量 | 计算系统应解决什么 | 不能自动推出什么 |
|---|---|---|---|
| 业务/训练自然峰谷 | 实际负载功率 P(t) | 平滑同步训练波动、回收闲置容量 | 供电上限也在同样波动 |
| 电网/园区要求限载 | 可用或合同允许功率上限 P_cap(t) | 在期限内达到上限、保护紧急任务、受控恢复 | 需要模拟电压、频率或发电机控制 |
| 分时电价 | 价格 c(t) | 在用户截止期内移时以降低费用 | 电价贵时物理上不能继续运行 |
| 低碳电力占比变化 | 碳强度或可再生可用性 | 碳感知调度；直供场景另需实际供能约束 | 碳强度曲线就是数据中心硬功率预算 |

前两类可以独立于绿电研究。只有外部合同、园区控制器或明确的供能系统把电力条件转成 P_cap(t)，才能把它作为调度的硬约束。研究中可重放该接口，不必把供电工程纳入贡献。

## 十组可核验产业证据

### E1：Google 早期碳感知系统已部署，但不能当作 GPU 训练限电证据

- 来源：Google，Ana Radovanovic，**Our data centers now work harder when the sun shines and wind blows**，2020-04-22。[官方正文](https://blog.google/innovation-and-ai/infrastructure-and-cloud/global-network/data-centers-work-harder-sun-shines-wind-blows/)
- 状态：已设计并部署的超大规模数据中心系统，正文未给 GPU 数量。
- 负载：可延期任务，如视频处理、照片功能处理、翻译词汇处理；基于次日预测调整运行时机。
- 能证明：实际存在以时间灵活性改变用电时段的生产实践。
- 不能证明：这些任务均为 GPU 大模型训练、已经实现 GPU 在线迁移，或每天必须因供电不足停机。碳优化与硬功率限额是不同目标。
- 核验：VERIFIED；标题、日期和部署表述已对照官方正文。

### E2：Google 2025—2026 年把 ML 需求响应写入电力合同

- 来源一：Google，Michael Terrell，**How we’re making data centers more flexible to benefit power grids**，2025-08-04。[官方正文](https://blog.google/innovation-and-ai/infrastructure-and-cloud/global-network/how-were-making-data-centers-more-flexible-to-benefit-power-grids/)
- 来源二：Google，Michael Terrell，**A new milestone for smart, affordable electricity growth**，2026-03-19。[官方正文](https://blog.google/innovation-and-ai/infrastructure-and-cloud/global-network/demand-response-data-center-milestone/)
- 状态/规模：2025 年宣布 I&M、TVA 协议，并回顾 2024 年 OPPD 三次事件的 ML 降载演示；2026 年披露长期合同累计纳入 **1 GW 需求响应容量**，新增 Entergy Arkansas、Minnesota Power、DTE。
- 负载：可限制或移动的“一部分 ML 工作负载”，未披露 GPU/TPU 构成、模型、尾延迟或调度算法。
- 能证明：真实商业接入需求和合同承诺；灵活性服务于新负载更快接网。
- 不能证明：1 GW 已实际调用、每日持续调节、全部 ML 任务可延期或无性能代价。Google 明示仅特定地点可用，服务可靠性限制灵活程度。
- 核验：VERIFIED；将“已签约”与“已运行”分开。

### E3：Emerald Phoenix 是直接 GPU 现场证据，并有部分开放复现实物

- 来源：Philip Colangelo 等，**Turning AI Data Centers into Grid-Interactive Assets: Results from a Field Demonstration in Phoenix, Arizona**，2025-07-01；实验在 2025-05。[作者论文及全文](https://arxiv.org/html/2507.00909v1)
- 状态/规模：OCI 商业机房内 **256 张 A100** 的代表性训练、微调、推理混合实验；实现 **降功率 25%、持续 3 小时**，要求缓升缓降。
- 核心限定：Flex 0 不调；其他档允许平均吞吐下降 10%、25%、50%。因此“满足 SLA”不等于“全部任务性能不变”。训练暂停/改资源涉及 checkpoint；作者因事件低频将其开销视作可忽略。跨地域调度、整机房遥测验证仍列为后续工作。
- 开放材料：作者 [GitHub](https://github.com/ai-emerald/emerald-ai-demo-may-2025) 提供 SRP/CAISO 曲线、功率与性能 CSV、DVFS sweep、编排伪代码、MosaicML 修改补丁、Dockerfile。它是部分软件工件，不能称完整商业 Conductor 开源。
- 核验：VERIFIED；正式场地不等于未经设计的生产业务混合，不把实验 SLA 当成所有客户的现行合同。

### E4：英国现场试验拓展到更快、更长的响应，但信号是模拟事件

- 来源：National Grid，**UK-first trial of AI Grid Technology Successfully Demonstrates the Ability of Data Centres to Adjust Power Needs**，2026-03-03。[电力公司官方发布](https://nationalgrid-newsroom.prgloo.com/news/uk-first-trial-of-ai-grid-technology-successfully-demonstrates-the-ability-of-data-centres-to-adjust-power-needs)
- 状态/规模：2025-12 在 Nebius 伦敦机房进行五天实机试验，**96 张 Blackwell Ultra GPU**，注入 **200 多次模拟电网事件**。
- 结果：小于一分钟降载超过三分之一，最高约 40%；有持续至十小时的降载请求、约三十秒降低 30% 的压力事件。关键工作负载继续运行。
- 能证明：真实 GPU 系统对不同形状功率请求具有可执行的柔性。
- 不能证明：200 次实际电网事故；已在全国商业运营；所有任务不受损；96 卡结果可直接外推到百兆瓦场站。其 2 GW 潜力属推演。
- 核验：VERIFIED；保持“实机+模拟外部事件”的准确标签。

### E5：2026 年 DSX Flex 已有商业多兆瓦试点，96 MW 仍须单独核查

- 来源：NVIDIA，**NVIDIA DSX Gives Infrastructure Builders the Playbook for AI Factories**，2026-05-31。[官方发布](https://nvidianews.nvidia.com/news/dsx-infrastructure-ai-factory)
- 对照：Emerald AI，**Emerald AI Announces First DSX Flex Deployment With NVIDIA and Silicon Valley Power, and Applauds Texas Fast Track for Flexible AI Factories**，2026-06-01。[官方正文](https://www.emeraldai.co/blog/nvidia-dsx-pilot-framework)
- 状态/规模：NVIDIA 使用 **commercial, multi-megawatt pilot**；Emerald 称首个商业部署。两者共同支持“商业多兆瓦试点”，未披露明确 MW 数、GPU 数、长期调度日志与 SLA 分布。
- 能证明：功率柔性已经进入厂商系统接口和电力公司合作部署；不只是概念论文。
- 不能证明：成熟的大规模普及或长期无损运行。Emerald 同一文明确将 Manassas **96 MW** 项目标为当年稍后计划，不能把项目额定容量算作当时已兑现降载量。[SVP 2026-04-21 试点原始公告](https://www.siliconvalleypower.com/Home/Components/News/News/45589/6271)
- 核验：VERIFIED；按各方共同支持的较保守状态定级。

### E6：Microsoft 百万服务器功率封顶已生产部署，不能转写成 GPU 跟电运行

- 来源：Microsoft Research，**Power Efficiency and Sustainability**，页面未给统一发布日期，明确状态截至 **2023-06**。[官方项目页](https://www.microsoft.com/en-us/research/project/power-capping/)
- 状态/规模：全公司数据中心数百万服务器部署 power capping，释放数百 MW 原先搁置的供电容量；正文同时涉及 Bing、Bing Ads、VM 级控制与超配。
- 能证明：性能感知功率控制和电力容量回收具有生产价值。
- 不能证明：这些数百万节点都是 GPU；由可再生出力驱动；已经实现 AI 训练需求响应。材料针对机房容量约束和功率超配，不能移花接木。
- 核验：VERIFIED；页面无发布日期即如实注明，不把抓取时间当发布时间。

### E7：NVIDIA/Microsoft/OpenAI 已解决部分瞬时平滑问题，研究要避开硬件贡献

- 来源：NVIDIA，Rouslan Dimitrov 等，**How New GB300 NVL72 Features Provide Steady Power for AI**，2025-07-28。[技术正文](https://developer.nvidia.com/blog/how-new-gb300-nvl72-features-provide-steady-power-for-ai/)
- 对照：Esha Choukse 等，**Power Stabilization for AI Training Datacenters**；Microsoft 项目页标 2025-08。[作者单位页面](https://www.microsoft.com/en-us/research/publication/power-stabilization-for-ai-training-datacenters/)
- 状态：生产训练功率波动作为问题依据；方案结合真实硬件与仿真评估。GB300 电源方案给出机架测试，不是电网需求响应全量部署。
- 机制/尺度：同步计算/通信形成负载波动；使用功率 ramp、最低功率、延迟降载、电容储能和 power burner。NVIDIA 的特定 Megatron 机架测试报告峰值约降 30%。
- 能证明：实际功率峰谷可能来自计算本身；平滑瞬态有产品级执行机制。
- 不能证明：能耗下降 30%、供电短缺时可长时间让电，或 RTX 3090 具备同样硬件功能。power burner 甚至会主动消耗功率，目标并非节能。
- 核验：VERIFIED；技术正文已读，未只依赖页面自动摘要。

### E8：中国电信有百卡跨省迁移验证，公开材料仍不足以验证性能与经济性

- 来源：中国电信上海公司，**锚定“十五五”新质生产力发展 临港算力以算电协同激活数字经济新动能**，2026-03-03。[运营商官方页面](https://www.chinatelecom.com.cn/ct/news/yunjisuanjisuanli/165190.html)
- 状态/规模：官方确认沪闽百卡集群自动化迁移技术验证，并描述“市级虚拟电厂—电信虚拟电厂—算力调度平台—省间设施”的闭环；绿电出力预测用于另一扩展场景的邀约。
- 能证明：电网调度与算网调度跨域接口已有实际验证，国内算力运营方确有需求。
- 不能证明：万卡训练状态无损实时迁移；模型/KV/checkpoint 的转移开销；远端备用资源成本、端到端 p99 或全国规模部署。官方文中的其他“目标提升”指标不可混作此迁移实验结果。
- 核验：VERIFIED（官方搜索返回正文）；直接打开该站返回 412。定量补充仅作二手旁证，不混入一手结论：[《沪闽实现AI百卡算力800公里跨省迁移》](https://www.jswx.gov.cn/csj/sh/202601/t20260104_1183563.shtml)，江苏网信网转载《人民邮电报》，2026-01-04，称 104 张 910B、三分钟、当地下降 50 kW。910B 也不能称 NVIDIA GPU。

### E9：Crusoe/Lancium 的 AI 机房运营属实，不能据此认定 GPU 已随电价/风光弹性调度

- 来源：Crusoe，**Crusoe announces flagship Abilene data center is live**，2025-09-30。[运营方正文](https://www.crusoe.ai/resources/newsroom/crusoe-announces-flagship-abilene-data-center-is-live)
- 状态/负载：Abilene 首期已在 OCI 运行；NVIDIA GB200 机架交付，已有早期训练与推理。多期全园区规划与已运行首期需分开。
- 后续：**Crusoe and Lancium Announce 1.0 Gigawatt AI Data Center Campus in Childress, Texas**，2026-07-15，[官方正文](https://www.crusoe.ai/resources/newsroom/crusoe-and-lancium-announce-1-gigawatt-ai-data-center-campus-in-childress-texas)，1 GW 园区宣布、预计 Q3 开工；Lancium 负责接网/能源编排，Crusoe 负责机房建设运营。
- 能证明：电力接入与能源基础设施是 AI 园区建设关键约束。
- 不能证明：已披露 GPU 柔性控制效果；比特币矿场可中断负荷经验可直接迁移到分布式训练；规划 1 GW 都已在线且可调。
- 核验：VERIFIED；这是一条用于防止过度推断的产业对照证据。

### E10：最新通信侧合作正面对功率与网络的联合约束，但仍是探索试点

- 来源：Lumen，**Lumen is Redefining Networking for the AI and Quantum Eras**，2026-10-29。[公司一手公告](https://ir.lumen.com/news/news-details/2026/Lumen-is-Redefining-Networking-for-the-AI-and-Quantum-Eras/default.aspx)
- 状态：与 Emerald 开发基于电力可用性转移 AI 计算的能力，明确仍在探索使用其边缘与网络设施进行 pilot。
- 负载/约束：特别点名 inference request routing，同时考虑 network performance、latency、security、cost；没有公开 GPU/节点规模、功耗数据或尾延迟结果。
- 能证明：跨站点推理路由是产业正在投入的问题，通信约束不是可忽略的细节。
- 不能证明：该联合方案已完成试验或广泛商用；新闻稿中的预测是事实。
- 核验：VERIFIED；资料时间早于截止日期，保留“正在开发/探索试点”的成熟度。

## 对选题的直接含义

以下是由上述证据推导的研究判断，不是企业已证明的结论。

1. **动机应优先写容量约束及需求响应，碳/电价只作可选外部信号。** 明确系统收到的是 P_cap(t)、生效时间、持续时长/不确定区间和恢复限制。否则“供电上限有峰谷”会成为没有来源的核心假设。
2. **“紧急/不紧急”需要变成可检验的服务合同。** 在线请求给 TTFT、TPOT、端到端尾延迟或完成期限；可延期任务给截止期、最小进度、允许暂停次数等。优先级标签本身不说明可削减多少瓦。
3. **实际可兑现的降载量与 GPU TDP 不同。** 任务功耗依赖阶段、占用率和设备；暂停仍有空闲底功耗；GPU 传感器不包含 CPU、内存、网络、风扇等。若只有 NVML 测量，只能把结论限定为 GPU 域预算；整机预算须测墙上功率或有误差界的校准。
4. **恢复过程值得单独观察。** 延期任务形成进度债务；控制指令结束后抢跑可能引起业务排队和功率反弹。只报告限电窗口内“未超 cap”会漏掉长期成本。应报告降载前、期间、恢复后的完整轨迹。
5. **暂停、checkpoint、资源重配的时间和通信成本不能直接假定为零。** 两台三卡的网络和存储成本可能与大机房不同，应先测量哪个动作能在响应期限内真正生效，再决定算法。可以把两个服务器当两个功率域；若模拟远端网络，需声明其合成性质，不能称跨省实测。
6. **不要把整站能源最优化作为首篇核心。** 光伏预测、储能容量配置、电网潮流、频率控制、电源电路、接网法规都可作为环境背景；计算侧贡献应集中在作业状态、资源分配、控制开销、队列及服务保证。

## 面向六张 RTX 3090 的可落地研究切口

建议先验证这个问题，而不是先承诺算法新颖性：**当可用 GPU 功率预算突然降低且结束时间不确定时，调度器能否依据任务紧急程度与真实切换成本，在履约降载的同时减少关键任务超时和可延期任务积压，并限制恢复功率反弹？**

最小可复现实验由本机工作负载与外部约束解耦组成：两台服务器运行若干在线推理和批处理/微调任务；独立控制器重放阶跃、斜坡、多次降载和恢复事件；通过功率上限、并发度及可恢复暂停执行动作。先核验 3090 具体驱动支持的功率范围和接口，不能从 A100/Blackwell 直接假定所有特性相同。外部信号可以从 E3 公开工件获得形状，但缩放到六卡预算必须清楚标明，不能声称复现了电网物理过程。

至少比较固定配置、统一功率封顶、静态优先级抢占，以及只看当前窗口而不计恢复代价的调度。主要结果应含 cap 超限幅度/时长、控制响应时间、在线 SLO 达成率、批任务有效进度/截止期违约、恢复完成时间、额外能耗与控制开销。在设备数有限时，异质业务和真实动作成本比把调度器换成一种泛化强化学习算法更容易形成可解释证据。

**新颖性预警：阶段感知限电推理已被直接研究。** Microsoft Research 的 **PowerSlider: Exploiting Phase Asymmetry for LLM Serving under Demand Response**，2026-08，[作者单位一手页面](https://www.microsoft.com/en-us/research/publication/powerslider-exploiting-phase-asymmetry-for-llm-serving-under-demand-response/)，已涉及 prefill/think/answer 分阶段、Flex SLO、动态 cap、SGLang 和生产 trace。这里只确认其存在及摘要范围，详细实验和代码需要论文线进一步复核。因此“根据 prefill/decode 阶段分配功率”或“紧急与普通请求分层”本身不足以声称新贡献；可考虑与跨作业 checkpoint 成本、双节点资源约束、响应期限及恢复积压联合形成更具体的问题。

## 建议给用户的结论

这条方向有产业需求，而且真实 GPU 现场证据已经超过纯模拟阶段。研究不应从“风光不稳，所以 GPU 必须跟着停”出发。更扎实的起点是：运营方收到随时间变化的功率预算，同时要服务不同紧急程度的 AI 任务；现有执行动作与服务损失之间存在系统开销，且必须兑现响应和恢复约束。六张 RTX 3090 可以验证这个核心系统问题，但不能凭六卡实验直接宣称百兆瓦电网收益或万卡扩展性。
