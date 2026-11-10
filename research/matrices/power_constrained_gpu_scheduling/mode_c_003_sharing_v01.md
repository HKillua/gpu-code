# Mode C：GPU共享、状态就绪与容量保障

本批由6份有效 `partial ANALYSIS_PACKAGE` 进入证据整合。使用项目指定的 deep-read Mode C 原文，旧矩阵保持原样。`SC`为来源主张，`DE`为可定位证据，`AI`为分析者推断，`UN`为缺项。页码按各本地PDF物理页序。

本批最值得继续理解的是：**可分配资源与能及时兑现的服务之间隔着执行边界和状态；更长保障窗口也会带来等待代价。** 这是阅读连接，尚未产生正式Idea、baseline选择、迁移方案或实验结果。

## 结构化交接包

```json
{
  "package_type": "MODE_C_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "MC-20261105-003-v01",
  "created_at": "2026-11-06",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "RNS01",
    "RNS02",
    "RNS03",
    "RNS04",
    "RNS05",
    "LC02"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "RNS01-E1",
    "RNS01-E2",
    "RNS01-E4",
    "RNS02-E1",
    "RNS02-E2",
    "RNS02-E4",
    "RNS02-E5",
    "RNS03-E1",
    "RNS03-E2",
    "RNS03-E4",
    "RNS04-E2",
    "RNS04-E5",
    "RNS05-E2",
    "RNS05-E3",
    "RNS05-E5",
    "LC02-E1",
    "LC02-E2",
    "LC02-E3"
  ],
  "missing_evidence": [
    "跨设置独立复验与完整统计区间",
    "源码、原始日志及正式版版本差异审计",
    "GFS v1式9/11与窗口覆盖率问题",
    "本机状态/控制能力和真实功率事件证据",
    "完整最近邻检索，不能确认新颖性"
  ],
  "next_skill": "discover-innovation",
  "matrix_id": "MC-20261105-003-v01",
  "analysis_version": "Mode C+ v1.0",
  "scope_and_inclusion_rules": {
    "field": "GPU系统、资源调度与服务状态",
    "goals": [
      "区分可分配份额、执行边界和状态可用性",
      "识别细粒度响应的进度/能量代价边界",
      "解释保障时长、未来信息与等待损失的口径"
    ],
    "sources": "本轮6篇已下载2025–2026正式发表研究的有效partial分析包；GFS正文为arXiv v1，其余为会议公开PDF；不新增观察稿或摘要来源。",
    "batch_note": "沿用2026-11-05广读批次，包于2026-11-06形成；旧MC001保持不变。",
    "downstream_use": "阅读连接与后续问题诊断，不选baseline或最终题目，不设计新方法。"
  },
  "source_map": [
    {
      "source_id": "RNS01",
      "analysis_package_id": "AN-20261105-035-v01",
      "analysis_path": "../../papers/xu_2026_nixie/analysis_broad_v01.md",
      "title": "Nixie: Efficient, Transparent Temporal Multiplexing for Consumer GPUs",
      "venue": "OSDI 2026",
      "version": "作者主页托管的 OSDI 2026 正式会议 PDF，含 USENIX 封面及 ISBN 978-1-939133-55-7；共 18 个 PDF 页面。",
      "source_type": "论文方法与评估",
      "available_evidence_ids": [
        "RNS01-E1",
        "RNS01-E2",
        "RNS01-E4"
      ],
      "evidence_status": "partial",
      "needed": [
        "代码版本与驱动拦截的实现审计",
        "统计重复次数、完整原始样本和不确定性复算",
        "RTX3090/CUDA/驱动兼容性以及用户主机内存/PCIe条件",
        "跨节点、动态功率上限和整机能耗证据"
      ]
    },
    {
      "source_id": "RNS02",
      "analysis_package_id": "AN-20261105-036-v01",
      "analysis_path": "../../papers/coppock_2025_lithos/analysis_broad_v01.md",
      "title": "LithOS: An Operating System for Efficient Machine Learning on GPUs",
      "venue": "SOSP 2025",
      "version": "CMU PDL 托管的 SOSP 2025 会议版 PDF，首页含正式 DOI；17 页。采用会议版首页摘要，避免混入 arXiv v1 中不同的结果数字。",
      "source_type": "论文方法与评估",
      "available_evidence_ids": [
        "RNS02-E1",
        "RNS02-E2",
        "RNS02-E4",
        "RNS02-E5"
      ],
      "evidence_status": "partial",
      "needed": [
        "TPC/QMD逆向接口、CUDA兼容性及工件版本审计",
        "原始功率时间序列、重复次数和传感器有效刷新率",
        "RTX3090控制权限与兼容性",
        "多卡通信、动态cap事件和整机能量的实测证据"
      ]
    },
    {
      "source_id": "RNS03",
      "analysis_package_id": "AN-20261105-037-v01",
      "analysis_path": "../../papers/zhang_2025_bless/analysis_broad_v01.md",
      "title": "Improving GPU Sharing Performance through Adaptive Bubbleless Spatial-Temporal Sharing",
      "venue": "EuroSys 2025",
      "version": "作者托管 EuroSys 2025 会议 PDF，首页含正式 DOI、会议日期与页码 573；16 页。",
      "source_type": "论文方法与评估",
      "available_evidence_ids": [
        "RNS03-E1",
        "RNS03-E2",
        "RNS03-E4"
      ],
      "evidence_status": "partial",
      "needed": [
        "实际工件/依赖版本和SM affinity接口兼容性",
        "每组实验的原始样本、随机性与统计区间",
        "动态DAG/LLM部署路径是否已经实现",
        "RTX3090上的MPS上下文资源控制和模型共存条件"
      ]
    },
    {
      "source_id": "RNS04",
      "analysis_package_id": "AN-20261105-038-v01",
      "analysis_path": "../../papers/wu_2026_fastserve/analysis_broad_v01.md",
      "title": "FastServe: Iteration-Level Preemptive Scheduling for Large Language Model Inference",
      "venue": "NSDI 2026",
      "version": "USENIX NSDI 2026 正式会议 PDF，含封面、ISBN 978-1-939133-54-0，论文页码 57–74；19 个 PDF 页面。以正式版 2026 年计数，早期同系预印本不另计。",
      "source_type": "论文方法与评估",
      "available_evidence_ids": [
        "RNS04-E2",
        "RNS04-E5"
      ],
      "evidence_status": "partial",
      "needed": [
        "正式版相对2023预印本与不同代码版本的变更对照",
        "原始请求/随机种子、完整重复数和统计区间",
        "工件及更新后的服务栈/公平比较配置",
        "RTX3090上模型精度、显存和拓扑适配证据"
      ]
    },
    {
      "source_id": "RNS05",
      "analysis_package_id": "AN-20261105-039-v01",
      "analysis_path": "../../papers/duan_2026_gfs/analysis_broad_v01.md",
      "title": "GFS: A Preemption-aware Scheduling Framework for GPU Clusters with Predictive Spot Instance Management",
      "venue": "ASPLOS 2026, Volume 1",
      "version": "本地为 arXiv:2509.11134v1（2025-09-14），15 页；首页有 ASPLOS 2026 标记，但 DOI/ISBN 为占位符。ASPLOS 2026 正式身份由官方 program 与 Crossref 核验。ACM 正式 PDF 返回 HTTP 403，尚未下载，版本差异未审计。",
      "source_type": "论文方法与评估",
      "available_evidence_ids": [
        "RNS05-E2",
        "RNS05-E3",
        "RNS05-E5"
      ],
      "evidence_status": "partial",
      "needed": [
        "正式ASPLOS版与arXiv v1差异；尤其式9/11与实现对应",
        "OrgLinear训练/测试划分、预测区间校准及代码入口",
        "生产前后负载/集群变化与相同月份观察口径",
        "原始trace字段、checkpoint恢复成本、模拟器和随机种子",
        "本机执行动作和硬件权限"
      ]
    },
    {
      "source_id": "LC02",
      "analysis_package_id": "AN-20261105-023-v01",
      "analysis_path": "../../papers/xu_2025_green/analysis_breadth_v01.md",
      "title": "GREEN: Carbon-efficient Resource Scheduling for Machine Learning Clusters",
      "venue": "NSDI 2025，pp.999–1014",
      "version": "NSDI2025正式会议PDF，未独立复现",
      "source_type": "论文方法与评估",
      "available_evidence_ids": [
        "LC02-E1",
        "LC02-E2",
        "LC02-E3"
      ],
      "evidence_status": "partial",
      "needed": [
        "未运行Slurm插件、未审阅完整代码工件",
        "本机功率接口、真实碳/预算信号与业务合同未知",
        "未检验完整训练质量与每个配置的统计区间"
      ]
    }
  ],
  "P": [
    {
      "id": "MC003-P01",
      "text": "调度器判定资源可让出时，在已提交执行单元和存量状态约束下，何时真正能供另一个任务使用并恢复有效服务？",
      "conditions": "显存紧张的应用复用、请求级抢占或训练作业重启；这些对象不同，问题只是共同的分解框架。",
      "mark": "AI",
      "strength": "中；源内已有相关机制，跨对象联系待检验",
      "evidence_ids": [
        "RNS01-E1",
        "RNS01-E2",
        "RNS04-E2",
        "LC02-E2"
      ]
    },
    {
      "id": "MC003-P02",
      "text": "缩短高优先级等待所需的执行粒度，会在何种条件下抵消后台有效进度或能量收益？",
      "conditions": "LithOS原多任务设置与BLESS共置显存可容纳、画像可用的设置；不是任意GPU的统一粒度规律。",
      "mark": "AI",
      "strength": "中；性能取舍有直接证据，能量箭头不完整",
      "evidence_ids": [
        "RNS02-E1",
        "RNS02-E4",
        "RNS02-E5",
        "RNS03-E2",
        "RNS03-E4"
      ]
    },
    {
      "id": "MC003-P03",
      "text": "容量可用的保障时长与未来信号权限，如何改变等待及已完成进度损失，而不能仅用平均分配率解释？",
      "conditions": "GFS按组织需求售卖spot容量；GREEN使用历史碳信号做训练调度。保障合同与碳目标不能混同。",
      "mark": "AI",
      "strength": "中；保障时长效应只在GFS指定模拟有直接证据",
      "evidence_ids": [
        "RNS05-E2",
        "RNS05-E3",
        "RNS05-E5",
        "LC02-E1",
        "LC02-E2",
        "LC02-E3"
      ]
    }
  ],
  "F": [
    {
      "id": "MC003-F01",
      "text": "Nixie的后台吞吐相对同一nvshare W=4参照，随交互到达疏密出现改善与下降两种方向。",
      "conditions": "§7.2三个应用工作流；频繁交互情形低23.5%，两种较疏情形高90.6%和39.5%；不是仅改变单个到达参数的通用因果定律。",
      "mark": "DE",
      "strength": "中；图可定位，跨工作流含多项条件差异",
      "evidence_ids": [
        "RNS01-E4"
      ]
    },
    {
      "id": "MC003-F02",
      "text": "LithOS细分执行单元可缩短HP阻塞，但在指定多任务实验中付出吞吐代价。",
      "conditions": "§8.4/Fig20：仅TPC调度时HP时延约ideal的1.38倍，加入atomization约1.19倍，约10%吞吐代价；不与单任务atomization不足1%开销混用。",
      "mark": "DE",
      "strength": "中；源内消融可定位，未独立复测/未核齐区间",
      "evidence_ids": [
        "RNS02-E5"
      ]
    },
    {
      "id": "MC003-F03",
      "text": "GFS把保障窗口H从1h改到4h时，所测模拟中的spot累计排队明显增加，而驱逐率仅小幅降低。",
      "conditions": "§4.5/Table6：JQT 575.4s→3145.6s，驱逐率1.21%→1.16%；H=2h不全单调，不能推成所有负载的单调定律。",
      "mark": "DE",
      "strength": "中；单一模拟设置，模型/正式版仍有缺口",
      "evidence_ids": [
        "RNS05-E5"
      ]
    }
  ],
  "M": [
    {
      "id": "MC003-M01",
      "linked_ids": [
        "MC003-P01",
        "MC003-F01"
      ],
      "chain": "提交拦截后仍须等待已运行kernel，再完成所需状态迁移；交互到达改变切换/驻留机会，可影响后台有效进度。FastServe主动KV交换说明调度优先级与状态就绪应分开描述，GREEN也已计入checkpoint/重启。",
      "mark": "AI",
      "evidence_ids": [
        "RNS01-E1",
        "RNS01-E2",
        "RNS01-E4",
        "RNS04-E2",
        "RNS04-E5",
        "LC02-E2"
      ],
      "conditions": [
        "显存/主存与PCIe能力",
        "执行边界和任务到达",
        "保留/重算/搬移何种状态"
      ],
      "alternatives": [
        "Nixie跨工作流的模型与负载差异",
        "缓存及主机内存压力",
        "预取准确性与基线时间片设置"
      ],
      "status": "partial：原机制可定位，统一解释和跨对象复验未完成"
    },
    {
      "id": "MC003-M02",
      "linked_ids": [
        "MC003-P02",
        "MC003-F02"
      ],
      "chain": "缩小不可中断执行单元可减少HP等待，但增加调度/同步或影响并发效率；BLESS也要等待squad结束，计算份额不自动成为低延迟服务。LithOS已有TPC调度与DVFS，不把二者组合当新意。",
      "mark": "AI",
      "evidence_ids": [
        "RNS02-E1",
        "RNS02-E2",
        "RNS02-E4",
        "RNS02-E5",
        "RNS03-E1",
        "RNS03-E2",
        "RNS03-E4"
      ],
      "conditions": [
        "可细分kernel；persistent/cross-block同步为例外",
        "模型共存显存",
        "画像、stream优先级和频率切换代价"
      ],
      "alternatives": [
        "资源争用或TPC效率曲线变化",
        "MPS上下文与内存带宽干扰",
        "实现差异而非粒度本身"
      ],
      "status": "partial：源内取舍支持，动态电力上限下的能量/服务路径未知"
    },
    {
      "id": "MC003-M03",
      "linked_ids": [
        "MC003-P03",
        "MC003-F03"
      ],
      "chain": "更长保障窗口按未来需求保留容量，可能把风险转化成等待；GFS已预测需求分布并考虑checkpoint损失，GREEN已把排队/抢占/执行纳入JCT。是否获得所声称窗口可靠性，还取决于预测联合覆盖与信息可得性。",
      "mark": "AI",
      "evidence_ids": [
        "RNS05-E2",
        "RNS05-E3",
        "RNS05-E5",
        "LC02-E1",
        "LC02-E2",
        "LC02-E3"
      ],
      "conditions": [
        "需求分布及组织间相关性",
        "信号实际发布时间和所需lookahead",
        "任务可抢占合同及checkpoint状态"
      ],
      "alternatives": [
        "配额启发式、负载强度或默认参数造成排队变化",
        "v1公式与实际实现不一致",
        "生产前后负载变化"
      ],
      "status": "partial：排队取舍有直接证据，可靠性与供电合同迁移未知"
    }
  ],
  "E": [
    {
      "id": "RNS01-E1",
      "source_id": "RNS01",
      "analysis_package_id": "AN-20261105-035-v01",
      "source_location": "paper.pdf PDF5–6，§3–4",
      "mark": "DE",
      "content": "提交拦截、同步边界、虚拟地址保持与透明性。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS01-E2",
      "source_id": "RNS01",
      "analysis_package_id": "AN-20261105-035-v01",
      "source_location": "paper.pdf PDF6–8，§5–6",
      "mark": "DE",
      "content": "分层内存、双向搬移、预取、互动识别与防饥饿。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS01-E4",
      "source_id": "RNS01",
      "analysis_package_id": "AN-20261105-035-v01",
      "source_location": "paper.pdf PDF11–12，§7.2/Fig11–14",
      "mark": "DE",
      "content": "应用工作流、交互与后台取舍、预取消融和归一吞吐。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS02-E1",
      "source_id": "RNS02",
      "analysis_package_id": "AN-20261105-036-v01",
      "source_location": "paper.pdf PDF6–7、10，§5.1–5.4/§6",
      "mark": "DE",
      "content": "TPC队列、借用、atomization及例外。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS02-E2",
      "source_id": "RNS02",
      "analysis_package_id": "AN-20261105-036-v01",
      "source_location": "paper.pdf PDF7–9，§5.5–5.7",
      "mark": "DE",
      "content": "在线资源缩减、频率切换时间与预测。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS02-E4",
      "source_id": "RNS02",
      "analysis_package_id": "AN-20261105-036-v01",
      "source_location": "paper.pdf PDF12–13，§8.3/Fig19",
      "mark": "DE",
      "content": "固定工作量、100ms GPU功率采样和能量/时延代价。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS02-E5",
      "source_id": "RNS02",
      "analysis_package_id": "AN-20261105-036-v01",
      "source_location": "paper.pdf PDF13–14，§8.4–9/Fig20–21",
      "mark": "DE",
      "content": "atomization消融、开销、资源干扰及未来硬件。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS03-E1",
      "source_id": "RNS03",
      "analysis_package_id": "AN-20261105-037-v01",
      "source_location": "paper.pdf PDF5–6，§4.1–4.3/表1",
      "mark": "DE",
      "content": "离线画像、FIFO/相对进度与共置前提。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS03-E2",
      "source_id": "RNS03",
      "analysis_package_id": "AN-20261105-037-v01",
      "source_location": "paper.pdf PDF6–8，§4.4–5",
      "mark": "DE",
      "content": "配置预测、kernel squad、上下文切换与实现。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS03-E4",
      "source_id": "RNS03",
      "analysis_package_id": "AN-20261105-037-v01",
      "source_location": "paper.pdf PDF11–12，§6.4–6.5/Fig15–16",
      "mark": "DE",
      "content": "多应用、倾斜负载延迟代价和SLO违约。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS04-E2",
      "source_id": "RNS04",
      "analysis_package_id": "AN-20261105-038-v01",
      "source_location": "paper.pdf PDF7–8，§4.2/Fig8–9",
      "mark": "DE",
      "content": "KV容量膨胀、ENST、主动交换、突发预留。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS04-E5",
      "source_id": "RNS04",
      "analysis_package_id": "AN-20261105-038-v01",
      "source_location": "paper.pdf PDF12–13，§6.3/Fig16–18",
      "mark": "DE",
      "content": "MLFQ/主动交换/chunked-prefill设计消融及适用压力区间。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS05-E2",
      "source_id": "RNS05",
      "analysis_package_id": "AN-20261105-039-v01",
      "source_location": "paper.pdf PDF5–7，§3.1–3.2",
      "mark": "DE",
      "content": "GDE/OrgLinear组织特征、分布输出和分位需求。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS05-E3",
      "source_id": "RNS05",
      "analysis_package_id": "AN-20261105-039-v01",
      "source_location": "paper.pdf PDF7–9，§3.3–3.4/式9、11、17",
      "mark": "DE/UN",
      "content": "容量公式冲突、反馈阈值歧义、抢占启发式和checkpoint损失；PDF7另经渲染核对。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "RNS05-E5",
      "source_id": "RNS05",
      "analysis_package_id": "AN-20261105-039-v01",
      "source_location": "paper.pdf PDF11–13，§4.4–4.6/表5–8/§6",
      "mark": "DE",
      "content": "模拟/保障窗口敏感性、预测/模块消融和未来细粒度管理。",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "LC02-E1",
      "source_id": "LC02",
      "analysis_package_id": "AN-20261105-023-v01",
      "source_location": "§4、§8.1，PDF pp.6/10；功率计量、CIC与资源",
      "mark": "DE",
      "content": "§4、§8.1，PDF pp.6/10；功率计量、CIC与资源",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "LC02-E2",
      "source_id": "LC02",
      "analysis_package_id": "AN-20261105-023-v01",
      "source_location": "§§5–7，PDF pp.6–10；弹性与双层队列、保存宽限",
      "mark": "DE",
      "content": "§§5–7，PDF pp.6–10；弹性与双层队列、保存宽限",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    },
    {
      "id": "LC02-E3",
      "source_id": "LC02",
      "analysis_package_id": "AN-20261105-023-v01",
      "source_location": "§8.1–8.3，PDF pp.10–12；作业分母及结果",
      "mark": "DE",
      "content": "§8.1–8.3，PDF pp.10–12；作业分母及结果",
      "interpretation_boundary": "DE仅指原文设计/结果可定位，非本项目独立复测；RNS05-E3的冲突公式为UN，不作算法正确性证据。"
    }
  ],
  "P_by_F_matrix": {
    "rows": [
      "MC003-F01",
      "MC003-F02",
      "MC003-F03"
    ],
    "columns": [
      "MC003-P01",
      "MC003-P02",
      "MC003-P03"
    ],
    "cells": [
      {
        "P_id": "MC003-P01",
        "F_id": "MC003-F01",
        "status": "plausible",
        "reason": "交互到达与后台进度方向变化暴露状态就绪/驻留条件；跨工作流多因素，不能只归因于切换。",
        "evidence_ids": [
          "RNS01-E1",
          "RNS01-E2",
          "RNS01-E4"
        ],
        "mechanism_ids": [
          "MC003-M01"
        ]
      },
      {
        "P_id": "MC003-P02",
        "F_id": "MC003-F01",
        "status": "diagnostic-needed",
        "reason": "F01是应用级搬移，P02涉及kernel/squad粒度；对象和时间尺度不一致，需区分等待执行与状态搬移。",
        "evidence_ids": [
          "RNS01-E4",
          "RNS02-E5",
          "RNS03-E2"
        ],
        "mechanism_ids": [
          "MC003-M01",
          "MC003-M02"
        ]
      },
      {
        "P_id": "MC003-P03",
        "F_id": "MC003-F01",
        "status": "unsupported",
        "reason": "Nixie未提供容量保障窗口或未来供电信号；到达敏感性不足以推出窗口可靠性。",
        "evidence_ids": [
          "RNS01-E4",
          "RNS05-E2"
        ],
        "mechanism_ids": []
      },
      {
        "P_id": "MC003-P01",
        "F_id": "MC003-F02",
        "status": "plausible",
        "reason": "细分执行单元改变释放执行资源的时间，能解释份额与响应为何不同；不覆盖显存/模型状态恢复。",
        "evidence_ids": [
          "RNS02-E1",
          "RNS02-E5",
          "RNS04-E2"
        ],
        "mechanism_ids": [
          "MC003-M02"
        ]
      },
      {
        "P_id": "MC003-P02",
        "F_id": "MC003-F02",
        "status": "supported",
        "reason": "同源多任务消融直接显示HP等待和吞吐取舍；仅在原配置支持，尚不能推出供电事件能量取舍。",
        "evidence_ids": [
          "RNS02-E5",
          "RNS02-E4",
          "RNS03-E4"
        ],
        "mechanism_ids": [
          "MC003-M02"
        ]
      },
      {
        "P_id": "MC003-P03",
        "F_id": "MC003-F02",
        "status": "unsupported",
        "reason": "kernel级局部吞吐代价未建立小时级spot保障或历史碳信号下的排队/损失关系。",
        "evidence_ids": [
          "RNS02-E5",
          "RNS05-E5",
          "LC02-E1"
        ],
        "mechanism_ids": []
      },
      {
        "P_id": "MC003-P01",
        "F_id": "MC003-F03",
        "status": "diagnostic-needed",
        "reason": "较长保障窗口改变等待，但没有给出实际释放、恢复到首个有效结果的分解；只能提出需补哪段时间。",
        "evidence_ids": [
          "RNS05-E3",
          "RNS05-E5",
          "LC02-E2"
        ],
        "mechanism_ids": [
          "MC003-M03"
        ]
      },
      {
        "P_id": "MC003-P02",
        "F_id": "MC003-F03",
        "status": "unsupported",
        "reason": "GFS按整卡任务调度且未测kernel粒度；不能把窗口排队归因于细粒度执行代价。",
        "evidence_ids": [
          "RNS05-E3",
          "RNS05-E5",
          "RNS02-E1"
        ],
        "mechanism_ids": []
      },
      {
        "P_id": "MC003-P03",
        "F_id": "MC003-F03",
        "status": "plausible",
        "reason": "窗口敏感性直接支持保障时长有排队代价；联合覆盖率、公式与实现和真实状态恢复仍未核齐。",
        "evidence_ids": [
          "RNS05-E2",
          "RNS05-E3",
          "RNS05-E5",
          "LC02-E3"
        ],
        "mechanism_ids": [
          "MC003-M03"
        ]
      }
    ]
  },
  "C": [
    {
      "id": "MC003-C01",
      "title": "能分配的份额，何时成为可兑现的服务？",
      "P_ids": [
        "MC003-P01",
        "MC003-P02"
      ],
      "F_ids": [
        "MC003-F01",
        "MC003-F02"
      ],
      "M_ids": [
        "MC003-M01",
        "MC003-M02"
      ],
      "evidence_ids": [
        "RNS01-E1",
        "RNS01-E2",
        "RNS01-E4",
        "RNS02-E5",
        "RNS03-E4",
        "RNS04-E2",
        "RNS04-E5",
        "LC02-E2"
      ],
      "status": "diagnostic-needed",
      "inference_chain": "源内状态等待/执行粒度与吞吐取舍→可调资源和及时服务并非同量→在具体外部约束下是否改变可兑现容量尚无证据。",
      "scores": {
        "dimensions": [
          {
            "dimension": "mechanism_completeness",
            "score": 1,
            "reason": "执行/迁移边界已有证据，完整服务恢复与功率箭头缺失"
          },
          {
            "dimension": "evidence_quality",
            "score": 2,
            "reason": "Nixie/LithOS/FastServe方法和消融有稳定定位"
          },
          {
            "dimension": "cross_setting_potential",
            "score": 1,
            "reason": "跨RTX5090/A100和任务类型，需适配核验"
          },
          {
            "dimension": "non_triviality",
            "score": 2,
            "reason": "区分资源可分配与状态可用，暴露不同响应合同而非拼接组件"
          },
          {
            "dimension": "novelty_status",
            "score": 1,
            "reason": "上述系统已处理相关机制；限定到具体事件的最近邻仍待比较"
          },
          {
            "dimension": "verifiability",
            "score": 1,
            "reason": "先做统一时间线审计；本机机制诊断还需硬件核验"
          }
        ],
        "total": 8,
        "use": "仅用于本批阅读连接排序，不是发表前景或选题排名"
      },
      "main_unknowns": [
        "跨任务统一合同与完整服务恢复时间",
        "外部功率事件中资源释放是否真的降W",
        "新颖性：已读系统均已处理部分机制，不能泛称首次考虑抢占/恢复"
      ],
      "first_low_cost_check": "先对上述方法画同一格式的执行时间线，区分停提交、等待、状态搬移、首个有效结果和稳定服务；只核对原文能支持哪段，不生成实验结果。",
      "forbidden_claims": [
        "Nixie或BLESS已经证明节能/瞬时功率响应",
        "考虑恢复本身是新颖性",
        "六卡可直接验证原集群收益"
      ]
    },
    {
      "id": "MC003-C02",
      "title": "保障多久与等待多少，应怎样在同一合同中解释？",
      "P_ids": [
        "MC003-P03"
      ],
      "F_ids": [
        "MC003-F03"
      ],
      "M_ids": [
        "MC003-M03"
      ],
      "evidence_ids": [
        "RNS05-E2",
        "RNS05-E3",
        "RNS05-E5",
        "LC02-E1",
        "LC02-E2",
        "LC02-E3"
      ],
      "status": "plausible",
      "inference_chain": "GFS H敏感性中排队代价明显而驱逐率变化很小→保障时长与平均分配率不能代替彼此→未来信息权限和完整JCT/进度损失须一起核对。GREEN提供另一计账口径，不是同一容量保证的复验。",
      "scores": {
        "dimensions": [
          {
            "dimension": "mechanism_completeness",
            "score": 1,
            "reason": "窗口到排队路径部分支持，预测校准与状态损失不全"
          },
          {
            "dimension": "evidence_quality",
            "score": 2,
            "reason": "GFS Table6直接量化窗口敏感性，GREEN提供计账与信息定位"
          },
          {
            "dimension": "cross_setting_potential",
            "score": 1,
            "reason": "spot容量与碳信号不同，不能直接转换为W约束"
          },
          {
            "dimension": "non_triviality",
            "score": 2,
            "reason": "区分保障区间、信息权限和实际代价，可能暴露相同平均值下不同合同"
          },
          {
            "dimension": "novelty_status",
            "score": 1,
            "reason": "GFS已联合预测和保障；更具体问题需补近邻和正式版本"
          },
          {
            "dimension": "verifiability",
            "score": 2,
            "reason": "最低成本起点是已读材料的时间/分母/信息权限对照与正式版核查"
          }
        ],
        "total": 9,
        "use": "仅用于本批阅读连接排序，不是发表前景或选题排名"
      },
      "main_unknowns": [
        "GFS正式版式9/11及实现差异",
        "需求分位数聚合的联合窗口覆盖率",
        "碳信号的历史可得性不能当实时预测准确率",
        "真实功率预算中的适用性与最近邻"
      ],
      "first_low_cost_check": "先核对GFS正式版本/工件的配额定义，并把Table6每项分母、时间窗与信息权限列清；补不到则继续保留UN，不据v1歧义自行修复算法。",
      "forbidden_claims": [
        "更长窗口总会减少驱逐",
        "预测准确率直接证明窗口可靠性",
        "spot卡数合同等价于时变电功率合同"
      ]
    }
  ],
  "recommended_C": [
    "MC003-C01",
    "MC003-C02"
  ],
  "first_low_cost_check": "先完成C01的原文时间线及C02的合同/版本核查；不涉及GPU运行。",
  "cross_setting_audit": {
    "within_source": "P/F/E均回指方法、图、表或实现协议；DE表示来源可定位，不表示独立重现。",
    "cross_setting": "Nixie单用户单卡、LithOS/BLESS共驻留共享、FastServe请求KV、GFS组织容量、GREEN训练JCT，尚无统一设置的跨源复验。",
    "evidence_path": "F源内可定位；M为条件性解释；延伸到外部电力约束的箭头为AI/UN。",
    "independence": "六篇提供不同系统的互补材料，不是同一假设的六次独立复制。部分系统使用公开ML工作负载/Alibaba类trace，不能假定样本独立或将其合并算效应。",
    "conflicts": "外观相近的资源份额、分配率、归一时延、JCT和功率W分开；不汇总最大收益。"
  },
  "cross_domain_transfer": {
    "status": "not_applicable",
    "reason": "当前只做邻接文献的证据比较，未选择目标A/源方法B或提出迁移方案。",
    "transfer_id": "none"
  },
  "six_gpu_boundary": "阅读不因规模排除；两机六张3090仅在后续选题/实现/证据设计时筛关键机制，当前不声明接口兼容或可复现。",
  "handoff": {
    "innovation_modes": [
      "问题",
      "现象"
    ],
    "boundary": "用户要求先广读，实际下一阶段为补证据/讨论单个C；next_skill给协议下游，不自动进入方法或执行。",
    "no_method_design": true,
    "no_baseline_or_topic_selected": true
  }
}
```

## 来源入口

| 来源 | 上游包 | 已知版本边界 |
|---|---|---|
| RNS01 · Nixie: Efficient, Transparent Temporal Multiplexing for Consumer GPUs | [AN-20261105-035-v01](../../papers/xu_2026_nixie/analysis_broad_v01.md) | 作者主页托管的 OSDI 2026 正式会议 PDF，含 USENIX 封面及 ISBN 978-1-939133-55-7；共 18 个 PDF 页面。 |
| RNS02 · LithOS: An Operating System for Efficient Machine Learning on GPUs | [AN-20261105-036-v01](../../papers/coppock_2025_lithos/analysis_broad_v01.md) | CMU PDL 托管的 SOSP 2025 会议版 PDF，首页含正式 DOI；17 页。采用会议版首页摘要，避免混入 arXiv v1 中不同的结果数字。 |
| RNS03 · Improving GPU Sharing Performance through Adaptive Bubbleless Spatial-Temporal Sharing | [AN-20261105-037-v01](../../papers/zhang_2025_bless/analysis_broad_v01.md) | 作者托管 EuroSys 2025 会议 PDF，首页含正式 DOI、会议日期与页码 573；16 页。 |
| RNS04 · FastServe: Iteration-Level Preemptive Scheduling for Large Language Model Inference | [AN-20261105-038-v01](../../papers/wu_2026_fastserve/analysis_broad_v01.md) | USENIX NSDI 2026 正式会议 PDF，含封面、ISBN 978-1-939133-54-0，论文页码 57–74；19 个 PDF 页面。以正式版 2026 年计数，早期同系预印本不另计。 |
| RNS05 · GFS: A Preemption-aware Scheduling Framework for GPU Clusters with Predictive Spot Instance Management | [AN-20261105-039-v01](../../papers/duan_2026_gfs/analysis_broad_v01.md) | 本地为 arXiv:2509.11134v1（2025-09-14），15 页；首页有 ASPLOS 2026 标记，但 DOI/ISBN 为占位符。ASPLOS 2026 正式身份由官方 program 与 Crossref 核验。ACM 正式 PDF 返回 HTTP 403，尚未下载，版本差异未审计。 |
| LC02 · GREEN: Carbon-efficient Resource Scheduling for Machine Learning Clusters | [AN-20261105-023-v01](../../papers/xu_2025_green/analysis_breadth_v01.md) | NSDI2025正式会议PDF，未独立复现 |

## 交叉矩阵速读

完整理由与E编号见上方结构包；✅仅指原设置中的取舍证据，不表示跨设置或电力响应已证实。

| 现象＼问题 | P01：份额到服务就绪 | P02：粒度与后台代价 | P03：保障窗口与等待 |
|---|---|---|---|
| F01：Nixie交互疏密下后台收益方向不同 | ◐ 等待/搬移边界可解释，含工作流混杂 | △ 应用搬移与kernel粒度不同 | — 未测容量保障 |
| F02：LithOS响应改善伴随吞吐代价 | ◐ 释放执行资源不等于恢复状态 | ✅ 原多任务消融显示取舍 | — 无小时级保障证据 |
| F03：GFS更长窗口明显增加排队 | △ 缺首个有效结果分解 | — 整卡调度不识别kernel粒度 | ◐ 表6支持代价，可靠性尚缺证据 |

## 下游入口

- **MC003-C01**：先读执行时间线，区分提交等待、搬移与有效服务。Nixie/FastServe已处理预取和状态，LithOS/BLESS已处理粒度与进度，不能将“加入恢复/公平/预测”独立充作创新。
- **MC003-C02**：先核对GFS正式版及窗口定义，再看保障和等待的交换；GREEN历史碳信号与完整JCT计账只是互补证据，不是对GFS可靠性的独立复验。
- 暂不跨领域迁移，未选方法B。若后续要借外领域方法，先形成迁移核验包，再进入统一Idea；六卡可验证性到那一阶段再核对。

本包保存完整P/F/M/E、9个矩阵单元和2个连接点。下一项是补版本/合同证据和逐篇讨论，不能将本包当成新算法、已证实机制或可直接运行的实验计划。
