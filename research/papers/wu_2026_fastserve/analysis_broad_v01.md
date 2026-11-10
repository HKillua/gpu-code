# FastServe｜正文广读分析

FastServe 在 token iteration 边界调度请求，并预取/换出 KV 状态以减轻抢占的显存压力。正式发表为 NSDI 2026，同系预印本于2023年已公开；不能把发表年份当作首次提出时间。

采用项目 analyze-paper 快照和 ANALYSIS_PACKAGE v1.0。批次/ID 沿用 2026-11-05；文件形成于 2026-11-06（Asia/Shanghai）。本文的页码均为本地 [PDF](paper.pdf) 的物理页序号（封面若存在也计页）。

SC＝作者陈述；DE＝本轮读到的论文证据（并非独立复现）；AI＝分析者推断；UN＝待核验。阅读覆盖和实现缺项见包内。未选择 baseline、设计新方法或运行实验。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-038-v01",
  "created_at": "2026-11-06",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "RNS04"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "RNS04-E1",
    "RNS04-E2",
    "RNS04-E3",
    "RNS04-E4",
    "RNS04-E5"
  ],
  "missing_evidence": [
    "正式版相对2023预印本与不同代码版本的变更对照",
    "原始请求/随机种子、完整重复数和统计区间",
    "工件及更新后的服务栈/公平比较配置",
    "RTX3090上模型精度、显存和拓扑适配证据"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-038-v01",
  "source_identity_and_version": {
    "title": "FastServe: Iteration-Level Preemptive Scheduling for Large Language Model Inference",
    "short_name": "FastServe",
    "authors": [
      "Bingyang Wu",
      "Yinmin Zhong",
      "Zili Zhang",
      "Shengyu Liu",
      "Fangyue Liu",
      "Yuanhang Sun",
      "Gang Huang",
      "Xuanzhe Liu",
      "Xin Jin"
    ],
    "year": 2026,
    "venue": "NSDI 2026",
    "official_url": "https://www.usenix.org/conference/nsdi26/presentation/wu-bingyang",
    "pdf_url": "https://www.usenix.org/system/files/nsdi26-wu-bingyang.pdf",
    "doi": null,
    "version": "USENIX NSDI 2026 正式会议 PDF，含封面、ISBN 978-1-939133-54-0，论文页码 57–74；19 个 PDF 页面。以正式版 2026 年计数，早期同系预印本不另计。",
    "local_pdf": "paper.pdf",
    "metadata_provenance": "../../surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/recent_systems_additions.json",
    "reading_coverage": "实际阅读 PDF5–13：§3 总览、§4 skip-join MLFQ/KV管理/分布式执行、§5 实现、§6 实验设置/端到端/消融、§7 相关工作。未审计源码、所有附录或逐版变更。",
    "first_public_date": "2023-05-10",
    "version_audit": "本轮只读所存本地 PDF；正式身份沿用既有核验，不代表已逐版对读。"
  },
  "research_question": "自回归生成中输出长度未知，如何利用已知输入长度减轻队首阻塞，并让逐iteration抢占与中间状态管理配合？",
  "study_type_and_context": "LLM单实例（可跨多GPU）推理服务系统，调度与主动KV交换联动；不是外部功率控制器。正式论文2026，早期研究2023公开，只计同一研究一项。",
  "design_units_samples_or_materials": {
    "hardware": "§6.1/PDF10：端到端用两台AWS p4d.24xlarge，每台8×A10040GB、NVLink、1152GB主存、PCIe4.0×16；不同模型用1/4/16 GPU。设计选择实验另用单A10040GB。",
    "models": "OPT13B/66B/175B、Llama3 8B（GQA），FP16。",
    "data_arrivals": "ShareGPT真实用户文本和Alpaca生成文本；两者没有到达时间，作者用Poisson过程生成到达。因此不是生产请求到达trace回放。",
    "comparisons": "FasterTransformer5.3、vLLM0.6.1（是实验开展时版本，不是2026最新事实）；同TP/PP和最大batch。FastServe-FCFS为同执行引擎改调度的消融，有助于隔离引擎优化。"
  },
  "intervention_input_or_procedure": [
    "§4.1/PDF6–7：skip-join MLFQ以已知输入长度与轻量性能画像估算prefill，直接加入量子足够的队列；低队列量子递增，等待超阈值的请求晋升以抑制饥饿。执行边界是iteration，不是正在运行kernel的任意抢占。",
    "§4.2/PDF7–8：抢占保留KV会增加活跃状态；用预计下次调度时间ENST排序，主动换出较远请求、预取将运行请求，让计算与主机/GPU搬移重叠；保留空间应对突发到达，已经包含历史到达信息。",
    "§4.2：ENST依赖队列顺序、量子、batch和晋升时间，近似假设更高优先队列不提前完成；不能把它写成准确未来信息。",
    "§4.3/PDF9：结合tensor/pipeline并行，以running/pending队列管理流水线，KV按layer分片；交换指令随阶段通信传播，避免每阶段独立作不一致决策。",
    "§5/PDF9–10：Ray actors、C++/CUDA算子融合、PagedAttention等实现；性能归因不能只用与不同引擎基线的headline。"
  ],
  "measurements_and_analysis": "§6.1：主指标是每个job端到端时延除以输出token数，再对jobs平均/P95；它不等于逐token间隔TPOT。SLO为0.3s/token（该配置profile所得decode iteration的10倍）。§6.2另单独报告同时满足prefill/decode阶段SLO的P95 goodput。没有功率、能量、板卡降载、设施响应测量。",
  "results_with_uncertainty": [
    {
      "mark": "DE",
      "finding": "§6.2/Fig13，PDF12：OPT13B、阶段SLO设为轻负载各阶段时延的5/10/20倍，P95 goodput比vLLM高1.66–1.82倍，比同引擎FastServe-FCFS高1.46–1.64倍。",
      "evidence": [
        "RNS04-E4"
      ],
      "uncertainty": "这一阶段goodput口径不能与Fig11的归一端到端平均时延headline混用；未定位完整置信区间。"
    },
    {
      "mark": "DE",
      "finding": "§6.3/Fig17，PDF12：高到达率、显存压力下，主动交换比丢弃KV重算高2.7倍、比被动交换高1.7倍；显存充足、低到达率时三者接近。",
      "evidence": [
        "RNS04-E5"
      ],
      "uncertainty": "说明有效区间依赖显存压力；不是任何负载固定收益。"
    },
    {
      "mark": "DE",
      "finding": "§6.3/Fig17(b)，PDF12：测试中暴露在端到端时延上的swapping time低于5%。",
      "evidence": [
        "RNS04-E5"
      ],
      "uncertainty": "通信与计算重叠隐藏的是关键路径时间，不代表搬运字节、PCIe占用或能量低于5%。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "claim": "SC：短请求优先提高吞吐/尾时延",
      "evidence": "RNS04-E1/E4：skip-join、晋升、端到端及同引擎FCFS消融",
      "implementation": "UN：未对调度源码与特定依赖版本做复核；无普遍尾时延保证。"
    },
    {
      "claim": "SC：主动KV交换降低抢占开销",
      "evidence": "RNS04-E2/E5：ENST/预留空间、显存压力消融及暴露交换时延",
      "implementation": "AI：已处理预测性预取和突发预留，不能以“加预测/考虑KV”本身提出新颖性。"
    }
  ],
  "limitations_biases_and_ethics": {
    "author_stated": [
      "§4.2：抢占产生的KV容量膨胀是设计问题，单纯重算可能与晋升互相作用而长期不完成；论文已处理，不是遗漏。",
      "§6.1：设计选择用单A100是预算限制；到达由Poisson合成，因为数据集无时间戳。"
    ],
    "analyst_inference": [
      "本轮未见独立limitations节；方法/评估边界作为分析者判断，不冒充作者承认。",
      "两类模型家族、FP16及旧服务栈的收益不能直接外推到所有GQA/MoE、量化或新版本chunked prefill。",
      "分布式执行覆盖最多16GPU，但不等于跨地域状态迁移、动态power cap或机架控制已验证。",
      "请求平均归一时延、P95归一时延、阶段SLO goodput需要分别保留。"
    ],
    "ethics": "本轮未审计ShareGPT请求内容与许可；未下载或处理原始用户记录。"
  },
  "reproducibility_and_transferability": {
    "status": "unknown：正式PDF和详细配置可读；代码可运行性与旧/新版本差异未检查。",
    "six_gpu_boundary": "单卡迭代抢占、KV压力/预取和两节点执行路径具有阅读价值；24GB显存、模型精度、主存、PCIe/网络均需独立确认。能运行一个小模型不代表能复现OPT175B/16卡结论或作为合适baseline。",
    "cross_domain_transfer": "不适用；未将请求调度直接映射为供电控制。"
  },
  "candidate_P": [
    {
      "id": "RNS04-P1",
      "mark": "AI",
      "text": "任务可调度时间和状态可驻留/可搬移时间不同时，抢占到底节省了多少关键路径时间，又增加了多少资源占用？",
      "evidence": [
        "RNS04-E2",
        "RNS04-E5"
      ],
      "status": "待Mode C；作者已有主动状态管理。"
    }
  ],
  "candidate_F": [
    {
      "id": "RNS04-F1",
      "mark": "DE",
      "text": "主动KV交换只在较大显存压力下明显获益；低到达率下与重算/被动交换接近，暴露交换时间又不等于搬运总成本。",
      "evidence": [
        "RNS04-E5"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "RNS04-E1",
      "location": "paper.pdf PDF5–7，§3–4.1",
      "type": "DE",
      "content": "架构、输入长度、MLFQ量子/晋升/iteration边界。"
    },
    {
      "id": "RNS04-E2",
      "location": "paper.pdf PDF7–8，§4.2/Fig8–9",
      "type": "DE",
      "content": "KV容量膨胀、ENST、主动交换、突发预留。"
    },
    {
      "id": "RNS04-E3",
      "location": "paper.pdf PDF9–10，§4.3–6.1",
      "type": "DE",
      "content": "并行执行、实现和硬件/模型/数据到达设置。"
    },
    {
      "id": "RNS04-E4",
      "location": "paper.pdf PDF10–12，§6.1–6.2/Fig11–15",
      "type": "DE",
      "content": "指标定义、0.3s/token、基线版本、阶段goodput。"
    },
    {
      "id": "RNS04-E5",
      "location": "paper.pdf PDF12–13，§6.3/Fig16–18",
      "type": "DE",
      "content": "MLFQ/主动交换/chunked-prefill设计消融及适用压力区间。"
    }
  ],
  "unresolved_questions": [
    "正式版/2023公开稿/工件的具体变更与当前引擎接口如何？未审计。",
    "对可变外部容量而言，ENST误差和状态搬移是否仍可隐藏？本文没有证据，尚不能形成新颖性结论。",
    "同服务质量、同模型精度、同资源和动作权限下能否构成比较对象？需要后续问题与条件确定，当前不选baseline。"
  ]
}
```
