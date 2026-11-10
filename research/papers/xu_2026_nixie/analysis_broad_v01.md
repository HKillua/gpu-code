# Nixie｜正文广读分析

Nixie 把接近占满显存的应用按时间共享一张消费 GPU：关键是应用调度与显存迁移一起安排。作者已经处理双向迁移、预取、交互优先级与防饥饿；交互改善仍可能牺牲后台吞吐。

采用项目 analyze-paper 快照和 ANALYSIS_PACKAGE v1.0。批次/ID 沿用 2026-11-05；文件形成于 2026-11-06（Asia/Shanghai）。本文的页码均为本地 [PDF](paper.pdf) 的物理页序号（封面若存在也计页）。

SC＝作者陈述；DE＝本轮读到的论文证据（并非独立复现）；AI＝分析者推断；UN＝待核验。阅读覆盖和实现缺项见包内。未选择 baseline、设计新方法或运行实验。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-035-v01",
  "created_at": "2026-11-06",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "RNS01"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "RNS01-E1",
    "RNS01-E2",
    "RNS01-E3",
    "RNS01-E4",
    "RNS01-E5"
  ],
  "missing_evidence": [
    "代码版本与驱动拦截的实现审计",
    "统计重复次数、完整原始样本和不确定性复算",
    "RTX3090/CUDA/驱动兼容性以及用户主机内存/PCIe条件",
    "跨节点、动态功率上限和整机能耗证据"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-035-v01",
  "source_identity_and_version": {
    "title": "Nixie: Efficient, Transparent Temporal Multiplexing for Consumer GPUs",
    "short_name": "Nixie",
    "authors": [
      "Yechen Xu",
      "Yifei Wang",
      "Nathanael Ren",
      "Yiran Chen",
      "Danyang Zhuo"
    ],
    "year": 2026,
    "venue": "OSDI 2026",
    "official_url": "https://www.usenix.org/conference/osdi26/presentation/xu-yechen",
    "pdf_url": "https://danyangz.github.io/papers/OSDI26-Nixie.pdf",
    "doi": null,
    "version": "作者主页托管的 OSDI 2026 正式会议 PDF，含 USENIX 封面及 ISBN 978-1-939133-55-7；共 18 个 PDF 页面。",
    "local_pdf": "paper.pdf",
    "metadata_provenance": "../../surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/recent_systems_additions.json",
    "reading_coverage": "实际阅读 PDF 4–13：§2.2 背景限制、§3 架构、§4 透明性、§5 内存管理、§6 调度、§7 方法/微基准/应用评估、§8 讨论。首页与正式身份复用 RNS01 既有记录。未逐条审计参考文献、代码或工件。",
    "first_public_date": "2026-01-16",
    "version_audit": "本轮只读所存本地 PDF；正式身份沿用既有核验，不代表已逐版对读。"
  },
  "research_question": "当多个应用各自几乎占满单卡显存时，如何透明地减少时间复用中的换页与切换代价，并区分交互响应和长任务进度？",
  "study_type_and_context": "单用户消费 GPU 系统原型和受控应用评估；应用级时间复用，不是多租户安全隔离、集群调度或供电控制论文。",
  "design_units_samples_or_materials": {
    "hardware": "§7/PDF9：Ryzen 9950X、96GB 双通道 DDR5、RTX5090 32GB，PCIe5.0×8；除 dedicated 2GPUs 参照外，Nixie 与主要基线均用单卡。§7.3/PDF12–13 另评 A5000 24GB/PCIe4.0×16。",
    "software": "§7/PDF9：Debian12、CUDA12.9、驱动580.95.05；Ollama0.12.11、SGLang0.5.4.post1、llama.cpp b7027、ComfyUI。",
    "workloads": "§7.2：文本扩展+图像生成、KVCOMM 多代理 GSM8K/HumanEvalFIM、代码补全+持续生成、三类长任务共置；Qwen3/Gemma3/Z-Image/Qwen-Image 等量化配置。不是集群生产到达轨迹。",
    "comparison": "UVM、nvshare（包括4s/30s窗口）、TGS（部分人工优先级配置）、无预取/轮转消融；dedicated 2GPUs 消除竞争但硬件数量更多，不作同资源收益归因。"
  },
  "intervention_input_or_procedure": [
    "§3–4/PDF5–6：LD_PRELOAD 拦截 CUDA Runtime；切换时停止新 kernel 提交，等待已提交 kernel 完成并同步，再搬移所需显存、恢复应用。不是任意时刻中断运行中的 kernel。CUDA VMM 保持虚拟地址，维护图捕获兼容路径。",
    "§5/PDF6–7：GPU、锁页/普通主存、磁盘分层，数据总体保留一份；以至多128MB chunk 管理，2MB block 迁移。全局迁移计划和双向队列协调 PCIe，预留 staging 空间以避免迁移相互阻塞。",
    "§6/PDF8：根据 CUDA 交互估计 idle/互动性，MLFQ 类软优先级及恢复规则抑制饥饿；队列 allotment/同级抢占阈值分别从8s/4s起，低层翻倍；预取预计下一应用。100ms 空闲判定是该策略设置，不是功率响应合同。"
  ],
  "measurements_and_analysis": "§7：切换 TTFT、平均端到端时延、双向迁移吞吐、锁页内存需求、按各自单独运行性能归一的后台吞吐。Fig6/11–13/15 给标准差误差棒；本轮未定位完整重复次数。没有板卡或整机功率、能量、供电越限指标。",
  "results_with_uncertainty": [
    {
      "mark": "DE",
      "finding": "§7.2 Case3/Fig13，PDF12：代码补全请求频繁到达时，Nixie 的长任务吞吐比 nvshare W=4 低23.5%；到达较疏两种情形反而高90.6%和39.5%。因此交互优先不等于后台进度无损。",
      "evidence": [
        "RNS01-E4"
      ],
      "uncertainty": "作者受控场景；比例不可外推到所有到达模式。"
    },
    {
      "mark": "DE",
      "finding": "§7.2 Case4/Fig14，PDF12：三种长任务运行300s，按各自独占吞吐归一，Nixie 为作者 ideal 的85%，nvshare W=4 为49.5%；自动预取比无预取消融增加5%吞吐。",
      "evidence": [
        "RNS01-E4"
      ],
      "uncertainty": "ideal 是论文定义的归一参照，不是独立最优性上界；未见此图的统计区间。"
    },
    {
      "mark": "DE",
      "finding": "§7.1/Fig9，PDF10：测试配置下，用 UVM 所需锁页内存的33.2%–40.2%达到相近切换TTFT。",
      "evidence": [
        "RNS01-E3"
      ],
      "uncertainty": "限该模型、主存与 PCIe 配置；不是所有模型内存恒降比例。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "claim": "SC：应用透明迁移且低切换成本",
      "evidence": "RNS01-E1/E2/E3：提交拦截、虚拟地址保持、分层双向传输及微基准",
      "implementation": "UN：未打开源码或执行工件；CUDA兼容性只以论文已展示范围为据。"
    },
    {
      "claim": "SC：兼顾响应与公平",
      "evidence": "RNS01-E4：混合应用及优先级/预取消融，频繁交互下后台吞吐有损失",
      "implementation": "AI：受测工作负载的归一吞吐公平不构成任意负载的尾时延或严格最大最小公平保证。"
    }
  ],
  "limitations_biases_and_ethics": {
    "author_stated": [
      "§8/PDF13：Linux 原型；Windows/AMD 属未来方向。",
      "§8：不理解张量语义，不能据此判断不可变权重而省去所有搬运；空间共存小模型仍属扩展。",
      "§8：同一用户共享锁页内存，同UID恶意进程不在威胁模型内。"
    ],
    "analyst_inference": [
      "测试的是单卡复用；A5000 主机含多卡不等于测试了多卡联合调度。",
      "等待运行中 kernel 完成使响应下界依赖未完成工作；不可直接承诺瞬时降功率。",
      "低显存/低锁页占用或更低TTFT不能推出整机节能；迁移会使用主机内存、PCIe和CPU。"
    ],
    "ethics": "本轮未审计数据许可或安全工件；论文单用户假设须保留，不把透明性写成多租户隔离。"
  },
  "reproducibility_and_transferability": {
    "status": "unknown：PDF可读且方法/环境明确；工件可运行性未审计，不给高可复现评级。",
    "six_gpu_boundary": "单卡状态迁移与交互/后台取舍是六卡资源内可继续理解的机制；3090兼容性、24GB显存、主存/PCIe及权限仍未知。六卡不能直接支持大集群公平、跨域迁移或设施级供电效果。",
    "cross_domain_transfer": "不适用；尚未选择源方法B或提出适配方案。"
  },
  "candidate_P": [
    {
      "id": "RNS01-P1",
      "mark": "AI",
      "text": "不同响应要求的应用共享显存紧张的GPU时，服务优先级与状态切换成本如何共同限定有效进度？",
      "evidence": [
        "RNS01-E1",
        "RNS01-E4"
      ],
      "status": "待Mode C；不是新问题或最终Idea。"
    }
  ],
  "candidate_F": [
    {
      "id": "RNS01-F1",
      "mark": "DE",
      "text": "频繁交互情形中，后台吞吐下降23.5%，而较疏到达情形改善；性能方向依赖到达频率。",
      "evidence": [
        "RNS01-E4"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "RNS01-E1",
      "location": "paper.pdf PDF5–6，§3–4",
      "type": "DE",
      "content": "提交拦截、同步边界、虚拟地址保持与透明性。"
    },
    {
      "id": "RNS01-E2",
      "location": "paper.pdf PDF6–8，§5–6",
      "type": "DE",
      "content": "分层内存、双向搬移、预取、互动识别与防饥饿。"
    },
    {
      "id": "RNS01-E3",
      "location": "paper.pdf PDF9–10，§7–7.1/Fig6–10",
      "type": "DE",
      "content": "单卡硬件环境、切换和锁页内存微基准。"
    },
    {
      "id": "RNS01-E4",
      "location": "paper.pdf PDF11–12，§7.2/Fig11–14",
      "type": "DE",
      "content": "应用工作流、交互与后台取舍、预取消融和归一吞吐。"
    },
    {
      "id": "RNS01-E5",
      "location": "paper.pdf PDF12–13，§7.3–8",
      "type": "DE",
      "content": "A5000补充测量、跨平台/语义/安全范围。"
    }
  ],
  "unresolved_questions": [
    "在RTX3090当前驱动上哪些拦截与迁移功能可复用？未验证。",
    "请求时延及后台吞吐的原始样本、重复数和长时间饥饿尾部怎样？未审计。",
    "供电额度变化是否会改变迁移、等待与恢复代价？本论文未评估；不能据此称该机制无人研究。"
  ]
}
```
