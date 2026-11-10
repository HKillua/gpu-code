# 计算、通信与资源重配置｜正式论文阅读卡

2026-11-05 · 领域入门的来源分析。下列八篇分别解释通信等待、弹性资源分配、重建训练状态及流水线的计算通信重叠。其优化目标主要是迭代时间、按期完工、利用率、模型精度或云费用。若要讨论可释放的电功率，还须另有同一边界下的功率观测。

**建议先读** CASSINI 的图 2、4、11，理解共享链路上究竟是谁等待谁；接着读 ElasticFlow 的图 5、6、12，看增加 GPU、完成期限与重配的关系；最后看 Sailor 的 §5.5，把算出新计划和真正恢复训练分开。

| 阅读层级 | 本轮覆盖 |
|---|---|
| 正文定点阅读 | LN01–LN03，核对方法、评估和适用条件，形成三个 partial ANALYSIS_PACKAGE。 |
| 摘要初筛 | LN04–LN08，正式发表和摘要已核，数值若出现均为作者摘要报告，等待对读正文和附录。 |

正式发表由 USENIX 论文页、ACM 的登记信息及会议录、会议程序或作者单位文库交叉核验。Optimus 的会议与摘要另查香港大学机构库。对部分 ACM 页面通过出版商存入 Crossref 的 DOI 记录和正式会议版 PDF 交叉核验。Sailor 作者名按会议录及 SOSP 接收表。PDF 可能位于作者或会议论文镜像。

## 八张阅读卡

### LN01 CASSINI · NSDI 2024

CASSINI: Network-Aware Job Scheduling in Machine Learning Clusters。Sudarsanan Rajasekaran、Manya Ghobadi、Aditya Akella。

- **问题**　多个训练作业共用网络链路时，何种放置会让它们的通信互相阻塞？
- **方法**　用单作业迭代内的通信周期描述链路需求，选择能错开通信的作业放置和起始时间偏移，并校正相位漂移。
- **来源内观察**　图 11 的 Poisson 到达实验中，Themis 加入 CASSINI 后较原 Themis 的平均迭代时间和 p99 迭代时间分别取得 1.6 倍、1.8 倍加速。结果针对训练迭代。
- **前提**　作业共享链路且存在可识别的通信周期。主体实验为 24 台单 A100、50 Gbps RoCEv2，GPU 由作业独占。
- **与供电问题的关系**　邻接。直接处理链路竞争与放置，只能为时变功率下的通信分析提供参照。
- **六卡可借鉴之处**　用两个跨机作业的迭代时间线辨认 NCCL 通信碰撞，需要先核实两机链路确实共享并可观测。
- **阅读深度**　正文定点对读：方法、评估和局限；未复现。

[正式出版入口](https://www.usenix.org/conference/nsdi24/presentation/rajasekaran) · [论文 PDF](https://www.usenix.org/system/files/nsdi24-rajasekaran.pdf) · [AN-20261105-017-v01](../../../papers/rajasekaran_2024_cassini/analysis_v01.md)

### LN02 ElasticFlow · ASPLOS 2023

ElasticFlow: An Elastic Serverless Training Platform for Distributed Deep Learning。Diandian Gu、Yihao Zhao、Yinmin Zhong、Yifan Xiong、Zhenhua Han、Peng Cheng、Fan Yang、Gang Huang、Xin Jin、Xuanzhe Liu。

- **问题**　训练作业给定截止时间时，如何判断还能接多少作业，并选择随时间变化的 GPU 数量？
- **方法**　用不同卡数下的吞吐预估计算最低可满足份额，做准入与边际收益分配，用 buddy 放置保持通信局部性。
- **来源内观察**　图 6b 的 128 卡、195 作业比较中，作者报告按期完成的作业数是 Chronus 的 1.62 倍。论文把可伸缩性与拒绝不可按期完成作业的贡献分别比较。
- **前提**　调度器掌握目标工作量与测量所得吞吐。原始 trace 缺 deadline，作者合成；测试床回放可跳过稳态迭代。
- **与供电问题的关系**　邻接。作业可调的是 GPU 数、资源份额与放置，实际电功率须另行计量。
- **六卡可借鉴之处**　观察同一全局 batch 在不同卡数和放置上的吞吐变化，同时记录等待与重配置；每机三卡与论文的整齐分组有差别。
- **阅读深度**　正文定点对读：方法、评估和局限；未复现。

[正式出版入口](https://doi.org/10.1145/3575693.3575721) · [论文 PDF](https://gudiandian.github.io/attaches/asplos23/asplosb23main-p360.pdf) · [发表核验](https://asplos-conference.org/asplos2023/index.html%3Fp=3602.html) · [AN-20261105-018-v01](../../../papers/gu_2023_elasticflow/analysis_v01.md)

### LN03 Sailor · SOSP 2025

Sailor: Automating Distributed Training over Dynamic, Heterogeneous, and Geo-distributed Clusters。Foteini Strati、Zhendong Zhang、George Manos、Ixeia Sánchez Périz、Qinghao Hu、Tiancheng Chen、Berk Buzcu、Song Han、Pamela Delgado、Ana Klimovic。

- **问题**　GPU 类型、链路与可用机器变化后，训练作业如何选出可放入显存、且吞吐/费用合适的并行方案？
- **方法**　用实测模型与链路 profile 驱动运行时间/显存估计，再搜索机器组合、数据/流水线/张量并行度和 microbatch，运行中资源变化可重新部署。
- **来源内观察**　§5.5 的 OPT-350M 从 16 张 V100 增加 4 张时，规划用 0.1 秒、清理用 3 秒、重建 NCCL 组用 4.5 秒。搜索完成与恢复训练是不同时间点。
- **前提**　需要模型计算、网络带宽和内存 profile；原实现支持 1F1B 流水线。部分大规模结果来自仿真。
- **与供电问题的关系**　邻接。文中的预算指货币/迭代，动态输入是资源可用性；本文可支撑重配置代价分析。
- **六卡可借鉴之处**　在能运行的固定小模型上区分参数/优化器、通信组与数据加载器的恢复时间，并确定何时出现第一步有效工作。
- **阅读深度**　正文定点对读：方法、评估和局限；未复现。

[正式出版入口](https://doi.org/10.1145/3731569.3764839) · [论文 PDF](https://anakli.inf.ethz.ch/papers/sailor_sosp25.pdf) · [发表核验](https://sigops.org/s/conferences/sosp/2025/accepted.html) · [AN-20261105-019-v01](../../../papers/strati_2025_sailor/analysis_v01.md)

### LN04 Optimus · EuroSys 2018

Optimus: An Efficient Dynamic Resource Scheduler for Deep Learning Clusters。Yanghua Peng、Yixin Bao、Yangrui Chen、Chuan Wu、Chuanxiong Guo。

- **问题**　训练作业进度与收益随资源配置变化，调度器能否在线估计并重新分配资源？
- **方法**　拟合收敛与资源—训练速度模型，用边际进度判断不同作业的分配和放置。
- **来源内观察**　作者报告在 7 台 CPU 服务器、6 台 GPU 服务器上运行 9 个 MXNet 训练作业，考察 JCT 与整批总完工时间。
- **前提**　需要作业进度与配置的可观测性。原工作采用参数服务器式训练，其参数放置与集体通信训练有不同约束。
- **与供电问题的关系**　邻接。研究以训练进度为目标的资源调度。
- **六卡可借鉴之处**　分别记录每秒训练步数、到目标质量所需步数和等待时间，理解为什么 GPU 数量不足以估算完工时间。
- **阅读深度**　摘要初筛：发表信息与摘要已核；方法细节与评估边界待正文核对。

[正式出版入口](https://doi.org/10.1145/3190508.3190517) · [论文 PDF](https://i.cs.hku.hk/~cwu/papers/yhpeng-eurosys18.pdf) · [发表核验](https://hub.hku.hk/handle/10722/259647)

### LN05 AntMan · OSDI 2020

AntMan: Dynamic Scaling on GPU Clusters for Deep Learning。Wencong Xiao、Shiru Ren、Yong Li、Yang Zhang、Pengyang Hou、Zhi Li、Yihui Feng、Wei Lin、Yangqing Jia。

- **问题**　单作业内显存和计算需求波动时，如何把空闲资源分给其他作业并处理干扰？
- **方法**　集群调度器与训练框架协同动态调节同卡作业的显存与计算资源。
- **来源内观察**　摘要报告该系统已在 Alibaba 部署，并将集群显存与计算单元利用率分别作为评估对象。
- **前提**　需要框架内动态调节显存和计算的操作能力，以及同卡作业的进度或干扰信号。
- **与供电问题的关系**　邻接。直接处理作业间资源共享，供电是另一个控制量。
- **六卡可借鉴之处**　同卡并发与独占的对照有助于区分计算单元空闲、显存受限与作业等待。能否实现论文机制需另查软件栈。
- **阅读深度**　摘要初筛：发表信息与摘要已核；方法细节与评估边界待正文核对。

[正式出版入口](https://www.usenix.org/conference/osdi20/presentation/xiao) · [论文 PDF](https://www.usenix.org/system/files/osdi20-xiao.pdf)

### LN06 StellaTrain · SIGCOMM 2024

Accelerating Model Training in Multi-cluster Environments with Consumer-grade GPUs。Hwijoon Lim、Juncheol Ye、Sangeetha Abdu Jyothi、Dongsu Han。

- **问题**　消费级 GPU 跨集群训练遇到低带宽与不同计算速度时，如何缩短抵达目标精度的时间？
- **方法**　结合梯度压缩、CPU 稀疏优化器与随带宽变化的训练配置选择，协调计算与传输。
- **来源内观察**　作者在受限带宽跨集群设置对照 PyTorch DDP，报告网络瓶颈下调整压缩和训练配置可缩短训练时间。
- **前提**　收益与跨站带宽、CPU 处理能力和压缩对收敛的影响有关。
- **与供电问题的关系**　邻接。重点是跨集群通信量和训练质量。
- **六卡可借鉴之处**　硬件类别与实验室接近，可先理解为什么带宽优化应比较 time-to-accuracy；本地两台机器实际跨接的是实验室网络。
- **阅读深度**　摘要初筛：发表信息与摘要已核；方法细节与评估边界待正文核对。

[正式出版入口](https://doi.org/10.1145/3651890.3672228) · [论文 PDF](https://cs.stanford.edu/~keithw/sigcomm2024/sigcomm24-final168-acmpaginated.pdf) · [发表核验](https://conferences.sigcomm.org/sigcomm/2024/accepted-papers/)

### LN07 Crux · SIGCOMM 2024

Crux: GPU-Efficient Communication Scheduling for Deep Learning Training。Jiamin Cao、Yu Guan、Kun Qian、Jiaqi Gao、Wencong Xiao、Jianbo Dong、Binzhang Fu、Dennis Cai、Ennan Zhai。

- **问题**　多项训练任务同时争用链路时，优先传哪条流可以减少 GPU 等待？
- **方法**　用通信流对应的 GPU 计算强度决定优先级，以提高整个集群实际计算利用率。
- **来源内观察**　摘要区分了 96-GPU 测试床与基于生产 trace 的大规模仿真。作者报告两者在流调度改变后观察到计算利用率收益。
- **前提**　需要能将网络流关联到作业并估计计算强度，执行优先级还需要对应的网络控制能力。
- **与供电问题的关系**　邻接。研究流调度对 GPU 计算利用率的影响。
- **六卡可借鉴之处**　可以辨认谁在等哪段流，先分开一次传输的完成时间与整项训练作业的等待。
- **阅读深度**　摘要初筛：发表信息与摘要已核；方法细节与评估边界待正文核对。

[正式出版入口](https://doi.org/10.1145/3651890.3672239) · [论文 PDF](https://www-cs.stanford.edu/~keithw/sigcomm2024/sigcomm24-final380-acmpaginated.pdf) · [发表核验](https://conferences.sigcomm.org/sigcomm/2024/accepted-papers/)

### LN08 Tessera · OSDI 2026

Tessera: A Holistic Pipeline Parallelism Framework for Trillion-Parameter Heterogeneous MoE Training (Operational Systems)。Weifang Hu、Langshi Chen、Man Yuan、Youyang Yao、Xiulong Yuan、Li Tian、Yong Li、Wei Lin、Xuanhua Shi、Zhengping Qian、Jingren Zhou。

- **问题**　不同层组合能掩盖的通信时间各异时，怎样划分流水线，避免串行估计看似平衡、实跑后却有等待？
- **方法**　用重叠后的实测成本划分流水线，并把可移动任务填入 MoE 路由变化造成的运行时空隙。
- **来源内观察**　这是 OSDI 的正式 Operational Systems 论文。作者对 4,096 至 12,288 卡的五组生产工作负载报告了相对自身生产基线的吞吐变化。
- **前提**　硬件、Qwen 系列混合架构和千至万卡并行规模决定了发生哪些等待。
- **与供电问题的关系**　邻接。研究重叠计算后的真实关键路径。
- **六卡可借鉴之处**　六卡可学习如何区分串行总和与实际重叠后的暴露通信时间；如此规模的生产吞吐和路由变动无法由六卡验证。
- **阅读深度**　摘要初筛：发表信息与摘要已核；方法细节与评估边界待正文核对。

[正式出版入口](https://www.usenix.org/conference/osdi26/presentation/hu-weifang) · [论文 PDF](https://www.usenix.org/system/files/osdi26-hu-weifang.pdf)

这批论文共有三个可供 Mode C 核验的观察：共享链路上的通信相位可能互补；固定截止时间下多给一张卡的收益取决于伸缩曲线与放置；新配置算出以后，运行时还要重新建立可执行状态。继续阅读时分别核对观测单位、基线和作者实际可控制的动作。八篇都没有提供用户两台机器上的功率上限响应证据。

本组未运行论文工件。没有将预印本、研讨会论文或未核实发表状态的工作计入八篇；独立观察表为空。
