# GFS｜正文广读分析

GFS 把租户需求的分布预测、带持续时间的spot配额和抢占代价连接起来。当前读到的是 arXiv v1，正式ASPLOS2026身份已核验，但版本差异未审计。v1存在两处直接影响重建的公式歧义，须先澄清，不能替作者修正后当作原方法。

采用项目 analyze-paper 快照和 ANALYSIS_PACKAGE v1.0。批次/ID 沿用 2026-11-05；文件形成于 2026-11-06（Asia/Shanghai）。本文的页码均为本地 [PDF](paper.pdf) 的物理页序号（封面若存在也计页）。

SC＝作者陈述；DE＝本轮读到的论文证据（并非独立复现）；AI＝分析者推断；UN＝待核验。阅读覆盖和实现缺项见包内。未选择 baseline、设计新方法或运行实验。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-039-v01",
  "created_at": "2026-11-06",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "RNS05"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "RNS05-E1",
    "RNS05-E2",
    "RNS05-E3",
    "RNS05-E4",
    "RNS05-E5"
  ],
  "missing_evidence": [
    "正式ASPLOS版与arXiv v1差异；尤其式9/11与实现对应",
    "OrgLinear训练/测试划分、预测区间校准及代码入口",
    "生产前后负载/集群变化与相同月份观察口径",
    "原始trace字段、checkpoint恢复成本、模拟器和随机种子",
    "本机执行动作和硬件权限"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-039-v01",
  "source_identity_and_version": {
    "title": "GFS: A Preemption-aware Scheduling Framework for GPU Clusters with Predictive Spot Instance Management",
    "short_name": "GFS",
    "authors": [
      "Jiaang Duan",
      "Shenglin Xu",
      "Shiyou Qian",
      "Dingyu Yang",
      "Kangjin Wang",
      "Chenzhi Liao",
      "Yinghao Yu",
      "Qin Hua",
      "Hanwen Hu",
      "Qi Wang",
      "Wenchao Wu",
      "Dongqing Bao",
      "Tianyu Lu",
      "Jian Cao",
      "Guangtao Xue",
      "Guodong Yang",
      "Liping Zhang",
      "Gang Chen"
    ],
    "year": 2026,
    "venue": "ASPLOS 2026, Volume 1",
    "official_url": "https://www.asplos-conference.org/asplos2026/program/index.html",
    "pdf_url": "https://arxiv.org/pdf/2509.11134v1",
    "doi": "10.1145/3760250.3762231",
    "version": "本地为 arXiv:2509.11134v1（2025-09-14），15 页；首页有 ASPLOS 2026 标记，但 DOI/ISBN 为占位符。ASPLOS 2026 正式身份由官方 program 与 Crossref 核验。ACM 正式 PDF 返回 HTTP 403，尚未下载，版本差异未审计。",
    "local_pdf": "paper.pdf",
    "metadata_provenance": "../../surveys/gpu_power_scheduling_2026/reading_pool_2026-11-05/recent_systems_additions.json",
    "reading_coverage": "实际阅读本地 arXiv v1 PDF3–13：§2 生产观察、§3 GDE/OrgLinear、SQA、PTS和§4模拟/生产/消融、§5–6相关工作/结论。PDF7式9/11已渲染目视复核。未取得并逐页对读正式ACM修订版，未审计源码。",
    "first_public_date": "本轮不补查",
    "version_audit": "本轮只读所存本地 PDF；正式身份沿用既有核验，不代表已逐版对读。"
  },
  "research_question": "高优先级需求变化时，如何出售较稳定的spot GPU容量，并在必须抢占时控制低优先任务的等待、驱逐和丢失工作？",
  "study_type_and_context": "GPU集群调度：需求预测、spot容量/持续时间管理和放置/抢占；包含大集群生产观察及trace模拟。可用容量来自HP需求，不直接等于时变供电功率预算。",
  "design_units_samples_or_materials": {
    "observational_context": "§2/PDF3–4：生产环境涉及10,365 GPUs和A10/A100/A800/H800。重点报告allocation rate（已分配资源比例），不是GPU计算利用率。",
    "simulation": "§4.1/PDF10：Apr–Jun2024轨迹，2296 A100（287个8卡节点），HP138403项、spot26635项；有fractional/single/multi GPU及gang作业。spot到达强度按1/2/4倍构造，HP保持。",
    "production": "§4.3/PDF10：Jan2024启用前与Oct2024启用后对比；后者541207 HP、114226 spot。非随机同期对照。",
    "baselines": "YARN-CS、Chronus、Lyra、FGD；部分按目标环境重实现/改造，Chronus租约有特定设置。表4包含p=0.9、反馈更新300s、H=1/2/4h。"
  },
  "intervention_input_or_procedure": [
    "§3.2/PDF5–7：OrgLinear分解趋势/周期，并引入组织、时间与业务特征；输出需求的高斯分布参数，再取分位数。预测分布和组织信息已是作者方法，不是待添加空白。",
    "§3.3/PDF7：SQA试图在时间窗口H内按组织需求峰值分位数汇总剩余GPU库存，以η反馈调节spot配额，兼顾驱逐率与排队。该处两个公式与文字有冲突，见RNS05-E3。",
    "§3.4/PDF7–9：先给调度优化表达，再用放置启发式实施，先尝试不抢占；需要时只驱逐spot，结合历史反复驱逐与checkpoint丢失工作代价。不能将启发式称全局最优。",
    "§3.4.2/PDF9式17：丢失工作按GPU数量×距最近checkpoint的时间近似；保留多次驱逐历史并有保护机制。已处理checkpoint进度损失，未等于完整checkpoint写入/恢复、网络或能量模型。"
  ],
  "measurements_and_analysis": "§4：JCT、JQT（跨驱逐累计排队）、spot驱逐率、GPU allocation rate、预测MAE/MSE/RMSE/MAPE及分位误差；不含板卡/整机功率、能量或供电越限。生产前后对比是观察性证据；模拟采用记录负载与人为spot强度变化。未定位完整重复数/统计区间。",
  "results_with_uncertainty": [
    {
      "mark": "DE",
      "finding": "§4.5/表6，PDF11：H从1h增到4h时，spot JQT由575.4s增到3145.6s，JCT由10715.3s增到13252.8s，驱逐率由1.21%变为1.16%。",
      "evidence": [
        "RNS05-E5"
      ],
      "uncertainty": "限该中等负载模拟；H=2h结果不全单调，不能概括为保证窗口越长必然更少驱逐。"
    },
    {
      "mark": "SC",
      "finding": "§4.3/PDF10–11：作者报告生产上线后spot驱逐率低于10%、allocation rate提高。",
      "evidence": [
        "RNS05-E4"
      ],
      "uncertainty": "Jan/Oct前后对比，不能隔离日历、作业混合、集群变化等混杂；不作为随机对照因果估计。"
    },
    {
      "mark": "UN",
      "finding": "§3.3/PDF7式9打印为 f(p,H)=C−max(C,各组织预测峰值之和)，因此数学上恒≤0；正文又把它解释为剩余可售库存，且称需求超C时为0。",
      "evidence": [
        "RNS05-E3"
      ],
      "uncertainty": "PDF页面已目视核对，非纯OCR推断。不能猜测作者本意后改公式；待正式版与实现。"
    },
    {
      "mark": "UN",
      "finding": "§3.3式11用 e>1.5p 进入高驱逐分支，但p定义为保障率，表4默认0.9，若e为[0,1]驱逐比例则阈值1.35不可达。",
      "evidence": [
        "RNS05-E3",
        "RNS05-E4"
      ],
      "uncertainty": "变量/印刷/版本问题待确认；不据此断言整套生产实现错误。"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "claim": "SC：预测分布支持高保障持续时间的spot容量",
      "evidence": "RNS05-E2/E3/E5：分布参数、窗口分位数、配额反馈和H敏感性",
      "implementation": "UN：正文公式冲突阻止忠实重建；误差降低不等于预测区间校准或90%实际持续时间保证。"
    },
    {
      "claim": "SC：抢占感知调度减少损失",
      "evidence": "RNS05-E3：checkpoint丢失GPU时间、驱逐历史；RNS05-E5：消融",
      "implementation": "UN：代码、trace和模拟驱逐/重启实现尚未审计。"
    },
    {
      "claim": "SC：生产效果显著",
      "evidence": "RNS05-E4：不同月份前后对比",
      "implementation": "AI：有效部署观察不等于因果归因，allocation rate也不是GPU忙碌程度或节能量。"
    }
  ],
  "limitations_biases_and_ethics": {
    "author_stated": [
      "§6/PDF13：未来进一步面向SM/tensor等细粒度GPU资源管理；当前不是完整卡内共享系统。"
    ],
    "analyst_inference": [
      "v1公式/符号需修订对读，当前不应作为可直接复现算法规格。",
      "§2/PDF4的Oct2024周驱逐统计与§4.3上线后Oct2024低驱逐表述存在日期/样本口径待解释，不能默认同一总体。",
      "按组织峰值分位数相加不自动获得联合时间窗口的目标可靠性；正文预测误差指标不能替代校准/覆盖率检验。",
      "生产观察不能排除负载与集群变化；模拟也不能替代实测checkpoint恢复路径。",
      "时间保障、GPU卡数份额和W功率上限是不同合同，从前者迁移到后者需新证据。"
    ],
    "ethics": "组织业务特征和生产轨迹可能涉及访问条件；本轮未下载或审计私有数据，不声明全套数据公开可复现。"
  },
  "reproducibility_and_transferability": {
    "status": "unknown/partial：正式身份已核，但本地v1不足以无歧义实现，须先对正式修订/源码；公开仓库线索不等于可复现已验证。",
    "artifact_leads": [
      "正文列 https://github.com/Sjtucitlab/Spot 及 Alibaba clusterdata；仅记录线索，本轮未访问/执行。"
    ],
    "six_gpu_boundary": "六卡可在未来测局部抢占/checkpoint动作和小规模并发；不能直接验证2296卡排队分布、万卡组织聚合或生产保障概率。阅读不限规模，后续研究主张须与可取得证据相称。",
    "cross_domain_transfer": "不适用；从需求预留到供电约束的连接尚只是候选，不构成TRANSFER_PACKAGE。"
  },
  "candidate_P": [
    {
      "id": "RNS05-P1",
      "mark": "AI",
      "text": "可用容量的时间保障、抢占频率和已完成进度损失应如何被分别解释，何种证据足以证明所承诺可靠性？",
      "evidence": [
        "RNS05-E2",
        "RNS05-E3",
        "RNS05-E5"
      ],
      "status": "待Mode C；本文已结合预测分布、保障窗口和checkpoint代价。"
    }
  ],
  "candidate_F": [
    {
      "id": "RNS05-F1",
      "mark": "DE",
      "text": "从1h到4h的保障窗口在所测负载中显著提高排队，而驱逐率仅小幅降低；保障时长并非免费。",
      "evidence": [
        "RNS05-E5"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "RNS05-E1",
      "location": "paper.pdf PDF3–4，§2/表1/脚注4/Fig5",
      "type": "DE",
      "content": "GPU规模、allocation口径和生产驱逐观察。"
    },
    {
      "id": "RNS05-E2",
      "location": "paper.pdf PDF5–7，§3.1–3.2",
      "type": "DE",
      "content": "GDE/OrgLinear组织特征、分布输出和分位需求。"
    },
    {
      "id": "RNS05-E3",
      "location": "paper.pdf PDF7–9，§3.3–3.4/式9、11、17",
      "type": "DE/UN",
      "content": "容量公式冲突、反馈阈值歧义、抢占启发式和checkpoint损失；PDF7另经渲染核对。"
    },
    {
      "id": "RNS05-E4",
      "location": "paper.pdf PDF10–11，§4.1–4.3/表4/Fig9",
      "type": "DE",
      "content": "2296卡trace、默认参数和Jan/Oct生产前后对比。"
    },
    {
      "id": "RNS05-E5",
      "location": "paper.pdf PDF11–13，§4.4–4.6/表5–8/§6",
      "type": "DE",
      "content": "模拟/保障窗口敏感性、预测/模块消融和未来细粒度管理。"
    }
  ],
  "unresolved_questions": [
    "式9/11在正式版和代码中如何定义？目前不能修正后归给作者。",
    "预测分位数对多个组织和整个H窗口是否有实测覆盖率/失准分析？正文误差表不足以回答。",
    "Oct2024两组驱逐观察的样本、前后部署时间和聚合方式是否一致？待查。",
    "真实驱逐后的checkpoint读写、进度恢复、资源等待如何实现及计量？尚未工件审计。"
  ]
}
```
