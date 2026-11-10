# LithOS｜正文广读分析

LithOS 把资源管理降到 TPC 和 kernel 内部，以 atomization 缩短长 kernel 对高优先任务的阻塞，并在线选择资源份额和频率。其能量收益来自单卡固定工作量测量，不能当作时变硬功率上限保证。

采用项目 analyze-paper 快照和 ANALYSIS_PACKAGE v1.0。批次/ID 沿用 2026-11-05；文件形成于 2026-11-06（Asia/Shanghai）。本文的页码均为本地 [PDF](paper.pdf) 的物理页序号（封面若存在也计页）。

SC＝作者陈述；DE＝本轮读到的论文证据（并非独立复现）；AI＝分析者推断；UN＝待核验。阅读覆盖和实现缺项见包内。未选择 baseline、设计新方法或运行实验。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-036-v01",
  "created_at": "2026-11-06",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "RNS02"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "RNS02-E1",
    "RNS02-E2",
    "RNS02-E3",
    "RNS02-E4",
    "RNS02-E5"
  ],
  "missing_evidence": [
    "TPC/QMD逆向接口、CUDA兼容性及工件版本审计",
    "原始功率时间序列、重复次数和传感器有效刷新率",
    "RTX3090控制权限与兼容性",
    "多卡通信、动态cap事件和整机能量的实测证据"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-036-v01",
  "source_identity_and_version": {
    "title": "LithOS: An Operating System for Efficient Machine Learning on GPUs",
    "short_name": "LithOS",
    "authors": [
      "Patrick H. Coppock",
      "Brian Zhang",
      "Eliot H. Solomon",
      "Vasilis Kypriotis",
      "Leon Yang",
      "Bikash Sharma",
      "Dan Schatzberg",
      "Todd C. Mowry",
      "Dimitrios Skarlatos"
    ],
    "year": 2025,
    "venue": "SOSP 2025",
    "official_url": "https://doi.org/10.1145/3731569.3764818",
    "pdf_url": "https://pdl.cmu.edu/ftp/BigLearning/lithos_sosp25.pdf",
    "doi": "10.1145/3731569.3764818",
    "version": "CMU PDL 托管的 SOSP 2025 会议版 PDF，首页含正式 DOI；17 页。采用会议版首页摘要，避免混入 arXiv v1 中不同的结果数字。",
    "local_pdf": "paper.pdf",
    "metadata_provenance": "../../surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/recent_systems_additions.json",
    "reading_coverage": "实际阅读 PDF5–14：§4 抽象、§5 调度/atomization/right-sizing/DVFS、§6 实现、§7 实验设置、§8 核心评估/消融/开销、§9 讨论。未审计技术报告、源码和全部参考文献。",
    "first_public_date": "本轮不补查",
    "version_audit": "本轮只读所存本地 PDF；正式身份沿用既有核验，不代表已逐版对读。"
  },
  "research_question": "怎样在GPU上以更细资源/执行粒度共置时延敏感与尽力而为作业，减少长kernel阻塞并回收冗余算力和能量？",
  "study_type_and_context": "单卡GPU操作系统原型；TPC空间共享+时间调度，另有在线right-sizing与DVFS。后两项的测量和调度主比较分开。",
  "design_units_samples_or_materials": {
    "hardware": "§7/PDF10：A100 SXM4 40GB、108SM；30 CPU、216GB主存，Ubuntu22.04、CUDA12.6。论文称在A30/A100/H100/L4验证功能，不列RTX3090。",
    "workloads": "§7/表1：HP Triton/ONNX/TensorRT推理（ResNet50、RetinaNet、YOLOv4、BERT、GPT-J6B、Llama3 8B等），BE训练/推理（ResNet50、MobileNetV2、VGG19、DLRM、BERT等）。训练batch受限以让共置模型常驻显存。",
    "comparisons": "§7：TGS原版；REEF/Orion在LithOS拦截层重实现并扩展多HP，非直接运行原始工件。§8.1关闭right-sizing和DVFS以隔离调度效果；MIG切片受到其配置限制。",
    "arrivals": "§8.1/PDF11–12：Poisson HP请求、BE闭环；推理训练混合时HP负载设置为约80% GPU利用率。"
  },
  "intervention_input_or_procedure": [
    "§5.1–5.3/PDF6：CUDA提交队列、TPC配额、借用空闲TPC；以kernel时延预测与较低stream优先级控制借用作业阻塞。",
    "§5.4/PDF7：将长kernel分为线程块子集（atoms），每块仅执行一次；不要求应用源码/PTX重写，通过prelude和QMD改变执行范围。目标atom约250–500µs，仍不在已运行atom内部抢占。",
    "§5.5/PDF7–8：用满TPC/单TPC两点和occupancy近似kernel资源—时延曲线，按slip容忍范围选择较小TPC份额。",
    "§5.6/PDF9：频率切换约50ms，按kernel序列敏感性与运行历史选择设备级DVFS；冷启动/未见工作保守选高频，不是每个微秒kernel立即独立改频。",
    "§6/PDF10：MPS及逆向libsmctrl/QMD实现；遇跨block同步或persistent kernel禁用atomization/stealing。内存须可容纳，未实现显存超订阅迁移。"
  ],
  "measurements_and_analysis": "§8：HP吞吐/P99、BE吞吐、满足SLO的goodput；right-sizing以时间加权TPC占用衡量，不等于减少同等比例实体GPU。§8.3/PDF13：固定请求数/epoch，平均GPU功率×完成时间计能量，nvidia-smi每100ms采样；未证明传感器每100ms独立刷新，也不含整机/网络/机房用能。",
  "results_with_uncertainty": [
    {
      "mark": "DE",
      "finding": "§8.2/Fig18，PDF12–13：slip=1.1时，时间加权TPC容量节约均值26%、最大51%；P99/吞吐平均代价约4%。",
      "evidence": [
        "RNS02-E3"
      ],
      "uncertainty": "是特定模型配置的平均，不是GPU卡数节约或功率下降比例。"
    },
    {
      "mark": "DE",
      "finding": "§8.3/Fig19，PDF13：与默认频率相比，GPU能量均值节约26%、最大46%；slip=1.1时推理P99平均增加7%。",
      "evidence": [
        "RNS02-E4"
      ],
      "uncertainty": "固定工作量和单GPU计量；本轮未定位置信区间/重复数，不外推到所有工作负载。"
    },
    {
      "mark": "DE",
      "finding": "§8.4/Fig20，PDF13：只做TPC调度时HP时延约为ideal的1.38倍，加入atomization约1.19倍，伴随约10%吞吐开销。",
      "evidence": [
        "RNS02-E5"
      ],
      "uncertainty": "多租户调度权衡；不能与§8.5单任务atomization不足1%开销混为同一口径。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "claim": "SC：细粒度执行提高响应性",
      "evidence": "RNS02-E1/E5：atom机制和拆分消融；persistent/cross-block同步例外",
      "implementation": "UN：逆向驱动接口与重实现基线尚未源码复核。"
    },
    {
      "claim": "SC：透明在线节能",
      "evidence": "RNS02-E2/E4：保守在线频率学习和固定工作量GPU能量",
      "implementation": "AI：未评估硬功率约束、事件窗口越限或整机功率，不能写成此类保证。"
    }
  ],
  "limitations_biases_and_ethics": {
    "author_stated": [
      "§6：跨block同步/persistent kernel的stealing和atomization受限；低层细节另见技术报告。",
      "§8.5–9/PDF14：显存带宽等资源尚不能完整隔离；更细DVFS/每SM功率控制为未来硬件建议。"
    ],
    "analyst_inference": [
      "模型和训练batch被挑到可共存显存，不能据结果声称超订阅时仍保证SLO。",
      "控制接口硬件/驱动依赖强；与3090同属某架构不足以证明能移植。",
      "重实现基线有助于相同框架比较，也引入实现差异，需要工件审计判断公平性。",
      "性能容忍度和平均能量节约不直接等于瞬时功率上限可兑现。"
    ],
    "ethics": "不同进程地址空间并非完整故障隔离；不可恢复GPU错误仍可能影响共享设备，见§4。"
  },
  "reproducibility_and_transferability": {
    "status": "unknown：环境和关键机制清楚，未审计作者工件、技术报告或运行支持矩阵。",
    "six_gpu_boundary": "单卡kernel阻塞、动作粒度和GPU能量是可讨论的局部机制；3090低层接口、DVFS权限、显存可共存条件要先核验。不能从六卡推断大型集群配额兑现或机架功率响应。",
    "cross_domain_transfer": "不适用；尚无源方法B及适配设计。"
  },
  "candidate_P": [
    {
      "id": "RNS02-P1",
      "mark": "AI",
      "text": "共享任务响应需求变化时，已运行执行单元的粒度与资源调整的生效时间怎样限制服务可兑现性？",
      "evidence": [
        "RNS02-E1",
        "RNS02-E2",
        "RNS02-E5"
      ],
      "status": "待Mode C；作者已处理kernel阻塞，不能只加“动态”即称新。"
    }
  ],
  "candidate_F": [
    {
      "id": "RNS02-F1",
      "mark": "DE",
      "text": "atomization缩短HP阻塞但有吞吐代价；DVFS节能也允许P99增加。局部性能、总吞吐和能量并非同一方向。",
      "evidence": [
        "RNS02-E4",
        "RNS02-E5"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "RNS02-E1",
      "location": "paper.pdf PDF6–7、10，§5.1–5.4/§6",
      "type": "DE",
      "content": "TPC队列、借用、atomization及例外。"
    },
    {
      "id": "RNS02-E2",
      "location": "paper.pdf PDF7–9，§5.5–5.7",
      "type": "DE",
      "content": "在线资源缩减、频率切换时间与预测。"
    },
    {
      "id": "RNS02-E3",
      "location": "paper.pdf PDF10–13，§7–8.2/表1/Fig18",
      "type": "DE",
      "content": "硬件/模型/基线、关闭优化的调度比较、TPC容量。"
    },
    {
      "id": "RNS02-E4",
      "location": "paper.pdf PDF12–13，§8.3/Fig19",
      "type": "DE",
      "content": "固定工作量、100ms GPU功率采样和能量/时延代价。"
    },
    {
      "id": "RNS02-E5",
      "location": "paper.pdf PDF13–14，§8.4–9/Fig20–21",
      "type": "DE",
      "content": "atomization消融、开销、资源干扰及未来硬件。"
    }
  ],
  "unresolved_questions": [
    "RTX3090与具体CUDA/驱动版本能否支持所需TPC/QMD路径？未知。",
    "100ms采样所得GPU能量与更细瞬态/整机测量有何偏差？本文不足以确定。",
    "在线模型在非稳定kernel序列和外部供电事件下的适用范围是什么？未评估不等于既有领域空白。"
  ]
}
```
