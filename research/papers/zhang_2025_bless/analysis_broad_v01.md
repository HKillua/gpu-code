# BLESS｜正文广读分析

BLESS 利用应用执行之间的空隙，把固定空间配额与阶段性的自由共享组合起来；它依赖离线画像，并让任务在 kernel squad 边界调整资源。配额参照、实际延迟和SLO是不同指标，作者也测到了倾斜负载下的延迟代价。

采用项目 analyze-paper 快照和 ANALYSIS_PACKAGE v1.0。批次/ID 沿用 2026-11-05；文件形成于 2026-11-06（Asia/Shanghai）。本文的页码均为本地 [PDF](paper.pdf) 的物理页序号（封面若存在也计页）。

SC＝作者陈述；DE＝本轮读到的论文证据（并非独立复现）；AI＝分析者推断；UN＝待核验。阅读覆盖和实现缺项见包内。未选择 baseline、设计新方法或运行实验。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-037-v01",
  "created_at": "2026-11-06",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "RNS03"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "RNS03-E1",
    "RNS03-E2",
    "RNS03-E3",
    "RNS03-E4",
    "RNS03-E5"
  ],
  "missing_evidence": [
    "实际工件/依赖版本和SM affinity接口兼容性",
    "每组实验的原始样本、随机性与统计区间",
    "动态DAG/LLM部署路径是否已经实现",
    "RTX3090上的MPS上下文资源控制和模型共存条件"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-037-v01",
  "source_identity_and_version": {
    "title": "Improving GPU Sharing Performance through Adaptive Bubbleless Spatial-Temporal Sharing",
    "short_name": "BLESS",
    "authors": [
      "Shulai Zhang",
      "Quan Chen",
      "Weihao Cui",
      "Han Zhao",
      "Chunyu Xue",
      "Zhen Zheng",
      "Wei Lin",
      "Minyi Guo"
    ],
    "year": 2025,
    "venue": "EuroSys 2025",
    "official_url": "https://doi.org/10.1145/3689031.3696070",
    "pdf_url": "https://jamesthez.github.io/files/bless-eurosys25.pdf",
    "doi": "10.1145/3689031.3696070",
    "version": "作者托管 EuroSys 2025 会议 PDF，首页含正式 DOI、会议日期与页码 573；16 页。",
    "local_pdf": "paper.pdf",
    "metadata_provenance": "../../surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/recent_systems_additions.json",
    "reading_coverage": "实际阅读 PDF5–14：§4 系统设计/画像/调度/配置搜索/执行，§5 实现，§6 设置、性能、资源倾斜、SLO、消融、开销、局限。未审计源码/工件和全部附录参考。",
    "first_public_date": "本轮不补查",
    "version_audit": "本轮只读所存本地 PDF；正式身份沿用既有核验，不代表已逐版对读。"
  },
  "research_question": "多应用共享GPU且各有计算配额时，怎样回收纯空间切分或纯时间切分留下的执行空隙，同时约束应用相对独占其配额时的性能退化？",
  "study_type_and_context": "单GPU轻量应用共享系统；离线画像与在线调度结合，不是功率配额控制或显存超订阅。",
  "design_units_samples_or_materials": {
    "hardware": "§6.1/PDF9：单A100 108SM、40GB，EPYC7302，CUDA11.8、Ubuntu20.04。",
    "workloads": "VGG11、ResNet50/101、NasNet、BERT推理/训练；kernel约3µs–3ms。A/B/C为不同请求间隔的闭环负载，D采用Twitter/Azure Functions轨迹映射到应用请求，不能称GPU作业原始轨迹；E为资源份额与负载错配。",
    "co_location": "2、4、8应用场景；4/8应用同时到达并使用预设不等份额。单应用FIFO，活跃请求受序列执行约束；模型共同驻留显存。",
    "comparisons": "ISO为应用独享其配额的参照；Temporal、MIG、GSlice、Unbound、Reef+（修改原实现的MPS版）、训练Zico。ISO不是所有应用同时达到的无代价可行最优。"
  },
  "intervention_input_or_procedure": [
    "§4.1–4.2/PDF5：先在同型号GPU上画像各配额下应用总时延及kernel时延；训练画像一轮iteration。Table1平均画像1.9s（不同模型0.38–6.88s），不能解释为任意工作负载的固定成本。",
    "§4.3/PDF6：把连续kernel组成kernel squad，以相对画像的进度选择任务。反馈可补偿显存/PCIe争用引起的落后；这不是单独控制这些资源。",
    "§4.4/PDF6–8：搜索空间份额，再在squad执行的后段解除SM限制（默认比例50%）回收空隙；用两类时延预测近似比较候选配置，含对memcpy重叠误差的简化。",
    "§4.5/PDF8：预建不同SM affinity的MPS上下文并映射预分配内存。切换须等待上一上下文已提交kernel完成；改变上下文不是checkpoint、模型offload或网络迁移。",
    "§6.7/PDF12：默认squad至多50个kernel，观测时长约0.7–10ms；squad越大，切换摊销越好但资源配置/响应粒度变粗。"
  ],
  "measurements_and_analysis": "§6.2：平均请求时延、训练epoch时延；latency deviation为相对ISO配额参照的正延迟增量之和，而非严格最大最小公平。§6.5另替换进度目标来测SLO违约。论文未测GPU/整机功率、能量或外部功率预算响应。",
  "results_with_uncertainty": [
    {
      "mark": "DE",
      "finding": "§6.3/PDF10：推理平均延迟相对Temporal/MIG/GSlice/Unbound/Reef+分别下降37.3%/34.2%/21.1%/16.5%/13.5%。连续饱和到达、没有bubble时，性能接近GSlice且额外开销低于3%。",
      "evidence": [
        "RNS03-E3"
      ],
      "uncertainty": "收益依赖可回收空隙；不是任何满负载都同样获益。未定位完整重复数/区间。"
    },
    {
      "mark": "DE",
      "finding": "§6.4/Fig16，PDF11：倾斜场景中，大配额低负载App1平均延迟相对ISO增加9%，另一应用吞吐改善。作者归因于懒等待squad结束。",
      "evidence": [
        "RNS03-E4"
      ],
      "uncertainty": "直接反例表明“配额保证”不能写成任何时刻/任何负载下零性能损失。"
    },
    {
      "mark": "DE",
      "finding": "§6.5，PDF11：指定的两应用SLO实验平均违约0.6%，对比GSlice50.1%、Unbound38.8%。",
      "evidence": [
        "RNS03-E4"
      ],
      "uncertainty": "SLO以所选倍数和负载定义；0.6%不是零违约，也不是形式化保证。"
    },
    {
      "mark": "DE",
      "finding": "§6.9/PDF13：每squad同步约20µs、MPS上下文切换约50µs、每kernel调度约6.7µs；额外MPS上下文约230MB。",
      "evidence": [
        "RNS03-E5"
      ],
      "uncertainty": "该A100环境的实测，不应作为3090常数。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "claim": "SC：空间—时间组合回收bubble并保护配额",
      "evidence": "RNS03-E1/E2/E3/E4：配置搜索、反馈进度、饱和与倾斜负载评估",
      "implementation": "UN：未核对MPS affinity封装和调度代码；正文已显示部分负载下ISO延迟仍上升。"
    },
    {
      "claim": "SC：可扩展到SLO约束",
      "evidence": "RNS03-E4：替换相对进度目标后，在所测两应用负载中违约率下降",
      "implementation": "AI：此结果不能直接替代任意动态容量/功率事件下的SLO可行性证明。"
    }
  ],
  "limitations_biases_and_ethics": {
    "author_stated": [
      "§6.10/PDF13–14：动态DAG需分别画像，CUDA Graph/自回归路径的兼容描述为可扩展方案，不能当成已完成全面LLM验证。",
      "§6.10：当前控制SM，寄存器/共享内存等更细资源控制留待扩展。",
      "§4.1：避免极短kernel应用与极长kernel应用搭配，并检查共置显存容量。"
    ],
    "analyst_inference": [
      "离线画像利用确定性/稳定性；功率或频率变化可能影响画像有效性，但本文没有评估，不能因此宣称领域无人处理。",
      "Twitter/Functions到达和模型kernel组合是构造负载；不能代表所有GPU生产服务。",
      "平均正延迟偏差、平均SLO违约与尾部饥饿是不同性质。",
      "借用资源可提高吞吐，同时增加高配额应用的等待；配额份额不是电功率额度。"
    ],
    "ethics": "本轮未审计多租户攻击面或轨迹授权；只沿用论文公开材料的研究设置。"
  },
  "reproducibility_and_transferability": {
    "status": "unknown：论文给出画像、环境及开销；本轮未打开工件或运行依赖。",
    "six_gpu_boundary": "单卡bubble、配额到进度映射和squad等待属于资源内可研究机制；必须核验3090的MPS上下文/SM affinity、显存共存和画像成本。MIG只是部分A100比较基线，不能默认3090具备。多节点通信和机架电力效果不在该证据内。",
    "cross_domain_transfer": "不适用；这里只建立邻接机制连接。"
  },
  "candidate_P": [
    {
      "id": "RNS03-P1",
      "mark": "AI",
      "text": "计算份额相同是否意味着服务进度相同，执行边界与画像误差何时让二者分离？",
      "evidence": [
        "RNS03-E1",
        "RNS03-E4"
      ],
      "status": "待Mode C；论文已用进度反馈处理部分干扰。"
    }
  ],
  "candidate_F": [
    {
      "id": "RNS03-F1",
      "mark": "DE",
      "text": "无bubble的饱和负载收益退化；倾斜负载下低负载大配额任务出现9%延迟增加。",
      "evidence": [
        "RNS03-E3",
        "RNS03-E4"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "RNS03-E1",
      "location": "paper.pdf PDF5–6，§4.1–4.3/表1",
      "type": "DE",
      "content": "离线画像、FIFO/相对进度与共置前提。"
    },
    {
      "id": "RNS03-E2",
      "location": "paper.pdf PDF6–8，§4.4–5",
      "type": "DE",
      "content": "配置预测、kernel squad、上下文切换与实现。"
    },
    {
      "id": "RNS03-E3",
      "location": "paper.pdf PDF9–11，§6.1–6.3/Fig13–14",
      "type": "DE",
      "content": "硬件、负载来源、ISO参照、均值与饱和情形。"
    },
    {
      "id": "RNS03-E4",
      "location": "paper.pdf PDF11–12，§6.4–6.5/Fig15–16",
      "type": "DE",
      "content": "多应用、倾斜负载延迟代价和SLO违约。"
    },
    {
      "id": "RNS03-E5",
      "location": "paper.pdf PDF12–14，§6.7–6.10",
      "type": "DE",
      "content": "粒度敏感性、消融、开销、动态图与资源控制局限。"
    }
  ],
  "unresolved_questions": [
    "正文画像在RTX3090具体运行时上是否稳定且可获得所需控制接口？未核验。",
    "相对进度定义与实际代码如何一致处理阻塞/排队？实现未审计。",
    "动态外部约束下哪些画像量失效、何时失效？仅为待理解问题，尚无本项目证据。"
  ]
}
```
