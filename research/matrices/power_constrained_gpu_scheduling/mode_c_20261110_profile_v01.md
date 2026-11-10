# 计算机侧探索：频率画像的适用条件

本轮从已有MC002:C02、MC004:C02收窄。此前泛称“动态预算下重新联合优化”不成立为创新；只开放C01的诊断设计，执行条件仍未满足。2P×3F共6格，不为凑数量扩展题目。

```json
{
  "package_type": "MODE_C_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "MC-20261110-001-v01",
  "matrix_id": "MC-20261110-001-v01",
  "created_at": "2026-11-10",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "AN-20261110-001-v01",
    "AN-20261105-036-v01",
    "AN-20261105-043-v01",
    "AN-20261110-002-v01"
  ],
  "upstream_matrices": [
    "MC-20261105-002-v01:C02",
    "MC-20261105-004-v01:C02"
  ],
  "evidence_status": "partial",
  "next_skill": "discover-innovation",
  "scope_and_inclusion_rules": "计算机/GPU执行与通信；四份有效来源包，Kareus新增正文/工件条件定位，DEFT补已有处理边界，旧矩阵不覆盖。本矩阵只支持有限诊断设计，不支持方法新颖性或部署有效性判断。",
  "source_map": [
    {
      "id": "LP06",
      "analysis_id": "AN-20261110-001-v01",
      "analysis_path": "../../papers/wu_2026_kareus/analysis_constraints_v01.md",
      "version": "OSDI26会议PDF；11-10定点增量"
    },
    {
      "id": "RNS02",
      "analysis_id": "AN-20261105-036-v01",
      "analysis_path": "../../papers/coppock_2025_lithos/analysis_broad_v01.md",
      "version": "SOSP25会议PDF；复用"
    },
    {
      "id": "LN08",
      "analysis_id": "AN-20261105-043-v01",
      "analysis_path": "../../papers/hu_2026_tessera/analysis_broad_v01.md",
      "version": "OSDI26会议PDF；复用"
    },
    {
      "id": "NN-D",
      "analysis_id": "AN-20261110-002-v01",
      "analysis_path": "../../papers/chen_2026_deft/analysis_v01.md",
      "version": "ICS2026正式身份已核；实际读arXiv2608.02122v1在线HTML §§3–6，未对读出版社终版"
    }
  ],
  "P": [
    {
      "id": "P01",
      "mark": "AI",
      "text": "实际限频行为能否被已有频率画像充分表示，以稳定选择计算通信时序？",
      "conditions": "固定工作量、正确依赖、相同候选集；cap实际生效；原画像已覆盖相关频率",
      "evidence_ids": [
        "LP06-E06",
        "LP06-E08"
      ],
      "strength": "待诊断，未观察本机失败"
    },
    {
      "id": "P02",
      "mark": "AI",
      "text": "若画像失配需要刷新，刷新成本是否能由后续有效工作收益摊销？",
      "conditions": "先有P01失配证据；明确使用时长与计量边界",
      "evidence_ids": [
        "LP06-E07",
        "LN08-E6"
      ],
      "strength": "原文有开销证据，目标净收益未知"
    }
  ],
  "F": [
    {
      "id": "F01",
      "mark": "DE",
      "text": "Kareus已有多频率前沿及运行时按目标时延选点。",
      "evidence_ids": [
        "LP06-E05",
        "LP06-E08"
      ],
      "conditions": "源系统设计，不是本机复现"
    },
    {
      "id": "F02",
      "mark": "DE",
      "text": "Kareus恒频定理显式假定相同平均频率带来相同完成时间，并讨论power-limit波动。",
      "evidence_ids": [
        "LP06-E06"
      ],
      "conditions": "条件定理，不是任意负载的实测普遍律"
    },
    {
      "id": "F03",
      "mark": "DE",
      "text": "画像与执行均有代价：Kareus画像占MBO主要开销；Tessera重叠有SM竞争/内存副作用；LithOS已有在线画像/DVFS；DEFT已处理争用、一次应用画像和切频成本。",
      "evidence_ids": [
        "LP06-E07",
        "LN08-E2",
        "LN08-E6",
        "RNS02-E2",
        "NN-D-E01",
        "NN-D-E02",
        "NN-D-E03"
      ],
      "conditions": "四个不同设置的设计/条件证据，不能合并效应量；DEFT只用来限制重复创新，未证实本项目cap失配。"
    }
  ],
  "M": [
    {
      "id": "M01",
      "mark": "AI",
      "related": [
        "P01",
        "F01",
        "F02"
      ],
      "chain": "外部cap触发限频→实际频率与执行阶段可能相关→平均频率或原画像是否充分预测时序排序需要核验",
      "support": [
        "LP06-E06",
        "LP06-E08",
        "LN08-E2"
      ],
      "conditions": "cap生效且改变执行路径；频率观测足够解释目标时尺度",
      "alternatives": [
        "旧画像重新按实际频率选点即可解释",
        "计时/能量噪声或热漂移",
        "通信后端/数据依赖错误",
        "只是绝对时间变慢但配置排序不变",
        "若争用、数据驻留或切频开销未匹配，已有运行时机制就可能解释差异（DEFT NN-D-E01/E03），不可直接归因于平均频率画像失效。"
      ],
      "status": "待诊断，无已确认机制"
    },
    {
      "id": "M02",
      "mark": "AI",
      "related": [
        "P02",
        "F03"
      ],
      "chain": "画像更细→测量/训练模型耗时耗能→只有后续工作节约足够时净收益为正",
      "support": [
        "LP06-E07",
        "LN08-E6",
        "RNS02-E2",
        "NN-D-E02",
        "NN-D-E03"
      ],
      "conditions": "相同有效工作和使用窗口，计入准备成本",
      "alternatives": [
        "缓存已有状态无需重画像",
        "小候选直接枚举足够",
        "短任务无摊销余地"
      ],
      "status": "代价路径已有，目标收益未知"
    }
  ],
  "E": [
    {
      "id": "LP06-E05",
      "location": "会议PDF p7 §4.1；p10 §5.1 Fig8",
      "analysis_path": "../../papers/wu_2026_kareus/analysis_constraints_v01.md",
      "scope": "原文局部证据，未独立复验"
    },
    {
      "id": "LP06-E06",
      "location": "会议PDF p19 Appendix A Theorem1及Implications",
      "analysis_path": "../../papers/wu_2026_kareus/analysis_constraints_v01.md",
      "scope": "原文局部证据，未独立复验"
    },
    {
      "id": "LP06-E07",
      "location": "会议PDF p10 §5.2–5.3；p14–15 §6.6–6.7",
      "analysis_path": "../../papers/wu_2026_kareus/analysis_constraints_v01.md",
      "scope": "原文局部证据，未独立复验"
    },
    {
      "id": "LP06-E08",
      "location": "会议PDF p20 Appendix C；p8–9 §4.4 Alg2",
      "analysis_path": "../../papers/wu_2026_kareus/analysis_constraints_v01.md",
      "scope": "原文局部证据，未独立复验"
    },
    {
      "id": "RNS02-E2",
      "location": "paper.pdf PDF7–9，§5.5–5.7",
      "analysis_path": "../../papers/coppock_2025_lithos/analysis_broad_v01.md",
      "scope": "原文局部证据，未独立复验"
    },
    {
      "id": "LN08-E2",
      "location": "§5，PDF p10（印刷p295）",
      "analysis_path": "../../papers/hu_2026_tessera/analysis_broad_v01.md",
      "scope": "原文局部证据，未独立复验"
    },
    {
      "id": "LN08-E6",
      "location": "§6.7，PDF pp14–15（印刷pp299–300）",
      "analysis_path": "../../papers/hu_2026_tessera/analysis_broad_v01.md",
      "scope": "原文局部证据，未独立复验"
    },
    {
      "id": "LP06-E09",
      "location": "作者osdi26ae分支工件README、GPU_CONFIGS及toy启动脚本；读取2026-11-10",
      "analysis_path": "../../papers/wu_2026_kareus/analysis_constraints_v01.md",
      "scope": "仅实现入口和硬件边界；未审完整调用链或执行"
    },
    {
      "id": "NN-D-E01",
      "location": "arXiv v1 HTML §3.2–3.3，Eq.1–10",
      "analysis_path": "../../papers/chen_2026_deft/analysis_v01.md",
      "scope": "方法/条件边界，不用于支持本项目现象或收益"
    },
    {
      "id": "NN-D-E02",
      "location": "arXiv v1 HTML §3.4，Table 1",
      "analysis_path": "../../papers/chen_2026_deft/analysis_v01.md",
      "scope": "方法/条件边界，不用于支持本项目现象或收益"
    },
    {
      "id": "NN-D-E03",
      "location": "arXiv v1 HTML §4.4、§5–6",
      "analysis_path": "../../papers/chen_2026_deft/analysis_v01.md",
      "scope": "方法/条件边界，不用于支持本项目现象或收益"
    }
  ],
  "P_by_F_matrix": [
    {
      "F_id": "F01",
      "P_id": "P01",
      "status": "diagnostic-needed",
      "reason": "已有前沿重新选点是强替代解释，不能只比默认配置",
      "evidence_ids": [
        "LP06-E05",
        "LP06-E08"
      ]
    },
    {
      "F_id": "F01",
      "P_id": "P02",
      "status": "plausible",
      "reason": "既有前沿可复用，重画像不必然发生",
      "evidence_ids": [
        "LP06-E05",
        "LP06-E08"
      ]
    },
    {
      "F_id": "F02",
      "P_id": "P01",
      "status": "diagnostic-needed",
      "reason": "明确假设可被检验，但无前提失效实测",
      "evidence_ids": [
        "LP06-E06"
      ]
    },
    {
      "F_id": "F02",
      "P_id": "P02",
      "status": "unsupported",
      "reason": "定理条件不提供刷新成本或使用时长",
      "evidence_ids": [
        "LP06-E06"
      ]
    },
    {
      "F_id": "F03",
      "P_id": "P01",
      "status": "plausible",
      "reason": "竞争/在线画像是替代机制，不能由它们推出cap失配",
      "evidence_ids": [
        "LN08-E2",
        "RNS02-E2",
        "NN-D-E01",
        "NN-D-E03"
      ]
    },
    {
      "F_id": "F03",
      "P_id": "P02",
      "status": "supported",
      "reason": "来源内画像开销确需计账；目标环境摊销未知",
      "evidence_ids": [
        "LP06-E07",
        "LN08-E6",
        "NN-D-E02",
        "NN-D-E03"
      ]
    }
  ],
  "C": [
    {
      "id": "C01",
      "P_ids": [
        "P01",
        "P02"
      ],
      "F_ids": [
        "F01",
        "F02",
        "F03"
      ],
      "M_ids": [
        "M01",
        "M02"
      ],
      "evidence_ids": [
        "LP06-E05",
        "LP06-E06",
        "LP06-E07",
        "LP06-E08",
        "RNS02-E2",
        "LN08-E2",
        "LN08-E6",
        "LP06-E09",
        "NN-D-E01",
        "NN-D-E02",
        "NN-D-E03"
      ],
      "focus": "先诊断按频率重索引的已有画像是否足够，再判断是否需要额外状态/刷新。",
      "status": "diagnostic-needed",
      "scores": {
        "mechanism": 1,
        "evidence": 2,
        "cross_setting": 1,
        "nontriviality": 1,
        "novelty": 0,
        "verifiability": 1
      },
      "score_reason": "方法与前提有定位，目标失效未知；联合优化/在线适应已有，未确认新意；两卡可做局部条件检验但权限未知。",
      "unknowns": [
        "同平均频率对不同执行阶段是否充分",
        "差异是否影响配置选择而非仅绝对时间",
        "近邻是否已有同样处理",
        "目标设备能力"
      ],
      "first_low_cost_check": "设计旧画像重索引/新状态候选的同候选集合对照，先冻结测量与执行门槛。"
    }
  ],
  "recommended_C": "C01，仅诊断探索，不是最终选题",
  "first_low_cost_check": "先检查硬件接口与同机两卡通信路径；通过后按单个Idea做受控诊断。",
  "evidence_locations": [
    "../../papers/wu_2026_kareus/analysis_constraints_v01.md",
    "../../papers/coppock_2025_lithos/analysis_broad_v01.md",
    "../../papers/hu_2026_tessera/analysis_broad_v01.md",
    "../../papers/chen_2026_deft/analysis_v01.md"
  ],
  "missing_evidence": [
    "3090功率/锁频权限、传感器有效刷新、拓扑及空闲设备",
    "原工件对power cap状态的缓存、重索引、重画像触发完整实现",
    "已有画像在cap生效后的误差和有限候选选择损失",
    "全领域最近邻尚未穷尽；无独立复验"
  ],
  "handoff_ready": true,
  "handoff_scope": "仅可证伪的诊断Idea；不可据此宣称方法创新/开始GPU运行",
  "execution_ready": false,
  "gate_review": {
    "previous_gate": "旧MC002/004 handoff_ready:false保持原样",
    "new_evidence": "核对Kareus实际前沿使用、定理前提、代价与工件入口，加入DEFT已有运行时处理边界；收窄原泛动态问题。",
    "why_diagnostic_allowed": "P/F/M/E可追溯；设计同时允许原画像足够和失配两种结果，不以未知当事实",
    "still_not_ready": "硬件和运行配置未核、完整近邻未穷尽、无目标现象"
  },
  "evidence_audit": {
    "independence": "四篇不同系统，非同一设置独立复验；Kareus章节/工件不计为独立效果验证；DEFT仅提供设计与条件边界。",
    "causal_status": "已知的是设计和条件；cap→阶段相关性→排序的箭头都是AI",
    "measurement": "功率API设置值与实际响应、平均频率与阶段频率必须区分"
  },
  "cross_domain_transfer": "不适用：不移植外领域B，只检验计算机系统前提"
}
```
