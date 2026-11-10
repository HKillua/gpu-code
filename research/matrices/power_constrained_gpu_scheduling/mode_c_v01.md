# 第一轮 Mode C｜供电约束下的 GPU 调度

矩阵 ID：MC-20261105-001-v01；日期：2026-11-05；状态：**partial**。本轮用于理解领域和定位补证据问题，尚未形成最终研究方案。

三个来源分别说明：允许让多少性能是业务合同问题；同样让电的性能损失是系统问题；动作何时能执行及何时恢复是状态与时间尺度问题。它们不能相互代替，也不证明目前候选题有新颖性。

来源分析：[S01：批处理仿真](../../papers/acun_2026_dr/analysis_v01.md) · [S02：GPU 现场演示](../../papers/colangelo_2025_grid_interactive/analysis_v01.md) · [S03：在线推理系统](../../papers/li_2026_powerslider/analysis_v01.md)。

## P × F 概览

| 现象 | P01 业务合同与可调空间 | P02 等量让电的不同代价 | P03 状态与事件时间尺度 |
|---|---|---|---|
| F01 服务等级与履约样例 | supported，S02-E1/E2/E6 | plausible，S02-E1 | diagnostic-needed，S02-E1/E3 |
| F02 任务/阶段敏感性不同 | plausible，S01-E01/E02 | plausible，敏感性有证据，等量实测让电待核验 | diagnostic-needed，稳态与切换证据缺口 |
| F03 各系统动作与状态处理不同 | plausible，S02-E7 | unsupported，协议差异不足以解释敏感性 | diagnostic-needed，S02-E3/E7、S03:E3 |

supported 表示来源条件内的联系有证据，不表示本项目发现了创新。下方为带理由、条件、替代解释及证据索引的完整交接包。六维分数仅为当前证据缺口的粗标记，不是发表潜力评分。

```json
{
  "package_type": "MODE_C_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "MC-20261105-001-v01",
  "matrix_id": "MC-20261105-001-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "AN-20261105-001-v01",
    "AN-20261105-002-v01",
    "AN-20261105-003-v01"
  ],
  "scope_and_inclusion_rules": "领域入门的三个种子来源：GPU需求响应仿真、GPU现场演示、时变预算推理系统。并非穷尽检索；只做问题/现象/机制定位，未设计最终方法。",
  "source_map": [
    {
      "id": "S01",
      "analysis_id": "AN-20261105-001-v01",
      "source": "Acun et al., e-Energy 2026",
      "analysis_path": "../../papers/acun_2026_dr/analysis_v01.md",
      "coverage": "五页正文/表格定义，硬件profile+千节点仿真；未复现",
      "key_evidence": [
        "S01-E01",
        "S01-E02",
        "S01-E03"
      ]
    },
    {
      "id": "S02",
      "analysis_id": "AN-20261105-002-v01",
      "source": "Colangelo et al., Phoenix, arXiv:2507.00909v1",
      "analysis_path": "../../papers/colangelo_2025_grid_interactive/analysis_v01.md",
      "coverage": "正文/方法/部分工件；GPU计量现场试验；未独立复现",
      "key_evidence": [
        "S02-E1",
        "S02-E2",
        "S02-E3",
        "S02-E7"
      ]
    },
    {
      "id": "S03",
      "analysis_id": "AN-20261105-003-v01",
      "source": "Li et al., PowerSlider, arXiv:2608.21719v1",
      "analysis_path": "../../papers/li_2026_powerslider/analysis_v01.md",
      "coverage": "方法/评估/局限，硬件校准+仿真；部分合同/动态数值口径待澄清",
      "key_evidence": [
        "S03:E1",
        "S03:E2",
        "S03:E3",
        "S03:E4",
        "S03:E6"
      ]
    }
  ],
  "P": [
    {
      "id": "P01",
      "text": "给定降载需求，在什么业务约定与任务组合下存在可兑现调节空间？",
      "mark": "DE",
      "conditions": "S02现场指定Flex组合；S01合成任务条件",
      "evidence": [
        "S02-E1",
        "S02-E2",
        "S01-E02"
      ],
      "boundary": "这是已研究的问题框架，不是空白声明。"
    },
    {
      "id": "P02",
      "text": "同样收回功率时，不同任务/执行阶段的服务损失如何不同？",
      "mark": "AI",
      "conditions": "S01任务profile，S03特定模型/设备/阶段",
      "evidence": [
        "S01-E01",
        "S03:E1"
      ],
      "boundary": "由已测敏感性引出的研究问题；阶段感知方案已存在。来源分别测量不同频率或功率档位，尚未由本矩阵核验同一计量边界、相同实测让电量下的损失差异；3090适用性未知。"
    },
    {
      "id": "P03",
      "text": "事件时间尺度缩短或反复变化时，作业状态、切换和恢复代价会不会改变动作优劣？",
      "mark": "AI",
      "conditions": "S01不抢占；S02低频假定开销可忽略；S03含排空/切换",
      "evidence": [
        "S01-E03",
        "S02-E3",
        "S02-E7",
        "S03:E3"
      ],
      "boundary": "目标场景现象、实际事件频率、最近邻差异均未验证。"
    }
  ],
  "F": [
    {
      "id": "F01",
      "text": "同类任务可被分配不同允许退化等级；现场有吞吐低于全速仍满足其门槛的作业。",
      "mark": "DE",
      "conditions": "S02选定工作组合与平均吞吐窗口",
      "evidence": [
        "S02-E1",
        "S02-E6"
      ],
      "strength": "来源内可定位，外推需核验"
    },
    {
      "id": "F02",
      "text": "作者测量中，功率/频率变化对任务和推理阶段的性能影响不同。",
      "mark": "DE",
      "conditions": "S01 P100 profiles；S03图4对应模型/设备/batch",
      "evidence": [
        "S01-E01",
        "S03:E1"
      ],
      "strength": "有硬件定位；不能把不同条件的曲线视为同一实验"
    },
    {
      "id": "F03",
      "text": "所选系统的执行动作依赖作业状态：S01长训练不抢占，S02支持checkpoint请求，S03换池处理在途请求。",
      "mark": "DE",
      "conditions": "各论文设计与控制代码不同",
      "evidence": [
        "S01-E03",
        "S02-E7",
        "S03:E3"
      ],
      "strength": "实现/协议层事实，不是恢复成本大小的实测规律"
    }
  ],
  "M": [
    {
      "id": "M01",
      "related": [
        "P01",
        "F01"
      ],
      "chain": "业务可容忍延期或降速 → 调度动作有可用性能余量 → 可能换取可释放功率",
      "mark": "AI",
      "support": [
        "S02-E1",
        "S02-E2"
      ],
      "conditions": "可行的工作组合、合同窗口和动作响应",
      "alternatives": "原有过度配置也可能形成余量；不代表所有客户可降速",
      "status": "部分支持"
    },
    {
      "id": "M02",
      "related": [
        "P02",
        "F02"
      ],
      "chain": "执行瓶颈不同 → 对核心频率/功率的敏感性不同 → 等量让电的性能代价可能不同",
      "mark": "AI",
      "support": [
        "S01-E01",
        "S03:E1"
      ],
      "conditions": "模型、batch、GPU代际和输入长度不变或重新测量",
      "alternatives": "访存、热节流、并发与量化可改变曲线；原对照不是纯单因素机制证明",
      "status": "来源设置部分支持，本机未知"
    },
    {
      "id": "M03",
      "related": [
        "P03",
        "F03"
      ],
      "chain": "状态保存/释放/排空/重载占时间和资源 → 高预算窗口中可产生的有效工作变化 → 可能改变暂停与慢跑的选择",
      "mark": "AI",
      "support": [
        "S02-E3",
        "S02-E7",
        "S03:E3"
      ],
      "conditions": "窗口与真实动作成本接近且有资源争用",
      "alternatives": "驻留暂停可能很快；长间隔时开销可能可忽略；简单阈值可能足够",
      "status": "待检验，未视作根因"
    }
  ],
  "E": [
    {
      "id": "S01-E01",
      "location": "见 ../../papers/acun_2026_dr/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S01-E02",
      "location": "见 ../../papers/acun_2026_dr/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S01-E03",
      "location": "见 ../../papers/acun_2026_dr/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S02-E1",
      "location": "见 ../../papers/colangelo_2025_grid_interactive/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S02-E2",
      "location": "见 ../../papers/colangelo_2025_grid_interactive/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S02-E3",
      "location": "见 ../../papers/colangelo_2025_grid_interactive/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S02-E7",
      "location": "见 ../../papers/colangelo_2025_grid_interactive/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S03:E1",
      "location": "见 ../../papers/li_2026_powerslider/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S03:E2",
      "location": "见 ../../papers/li_2026_powerslider/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S03:E3",
      "location": "见 ../../papers/li_2026_powerslider/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S03:E4",
      "location": "见 ../../papers/li_2026_powerslider/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S03:E6",
      "location": "见 ../../papers/li_2026_powerslider/analysis_v01.md 中的 evidence_index",
      "scope": "原来源版本内的定位，未完成独立复现。"
    },
    {
      "id": "S02-E6",
      "location": "S02 分析包：SRP_exp_performance.csv，固定工件提交34403aef78c2690a8e4f0ba8f7f62d302cdba967",
      "scope": "单作业报告值。"
    }
  ],
  "P_by_F_matrix": [
    {
      "F_id": "F01",
      "P_id": "P01",
      "status": "supported",
      "reason": "服务档位与履约样例直接限定可调性的解释",
      "evidence_ids": [
        "S02-E1",
        "S02-E2",
        "S02-E6"
      ]
    },
    {
      "F_id": "F01",
      "P_id": "P02",
      "status": "plausible",
      "reason": "相同任务也可按服务档位承担不同代价；档位本身不证明硬件敏感性",
      "evidence_ids": [
        "S02-E1"
      ]
    },
    {
      "F_id": "F01",
      "P_id": "P03",
      "status": "diagnostic-needed",
      "reason": "业务窗口可能约束积压恢复，尚缺短窗口状态代价",
      "evidence_ids": [
        "S02-E1",
        "S02-E3"
      ]
    },
    {
      "F_id": "F02",
      "P_id": "P01",
      "status": "plausible",
      "reason": "曲线给性能/功率边界；仍需合同和负载才能计算余量",
      "evidence_ids": [
        "S01-E01",
        "S01-E02"
      ]
    },
    {
      "F_id": "F02",
      "P_id": "P02",
      "status": "plausible",
      "reason": "直接证据支持原设置内对频率/功率档位的敏感性差异；不足以确认相同实测让电量、相同边界下的服务损失差异。阶段感知已是已有研究，亦不能视作新问题发现。",
      "evidence_ids": [
        "S01-E01",
        "S03:E1"
      ]
    },
    {
      "F_id": "F02",
      "P_id": "P03",
      "status": "diagnostic-needed",
      "reason": "稳态服务曲线不包含切换过程；是否重要未知",
      "evidence_ids": [
        "S01-E01",
        "S03:E3"
      ]
    },
    {
      "F_id": "F03",
      "P_id": "P01",
      "status": "plausible",
      "reason": "实际动作能否执行影响可兑现性，长期业务组合仍未知",
      "evidence_ids": [
        "S02-E7"
      ]
    },
    {
      "F_id": "F03",
      "P_id": "P02",
      "status": "unsupported",
      "reason": "动作协议差异本身不能解释某阶段每瓦损失",
      "evidence_ids": [
        "S01-E03",
        "S03:E3"
      ]
    },
    {
      "F_id": "F03",
      "P_id": "P03",
      "status": "diagnostic-needed",
      "reason": "已有状态处理且存在开销假设；需检验事件尺度与代价，不能仅以恢复一词认定空白",
      "evidence_ids": [
        "S02-E3",
        "S02-E7",
        "S03:E3"
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
        "F02"
      ],
      "M_ids": [
        "M01",
        "M02"
      ],
      "status": "plausible",
      "focus": "先理解并测清业务合同下功率—性能余量",
      "scores": {
        "mechanism": 2,
        "evidence": 2,
        "cross_setting": 1,
        "nontriviality": 0,
        "novelty": 0,
        "verifiability": 1
      },
      "unknowns": [
        "3090 profile",
        "明确的在线/后台SLO"
      ],
      "judgment": "适合入门与复现，不能直接作为新方法。"
    },
    {
      "id": "C02",
      "P_ids": [
        "P03"
      ],
      "F_ids": [
        "F01",
        "F03"
      ],
      "M_ids": [
        "M01",
        "M03"
      ],
      "status": "diagnostic-needed",
      "focus": "先核对切换/恢复在目标时间尺度上是否为真实瓶颈",
      "scores": {
        "mechanism": 1,
        "evidence": 1,
        "cross_setting": 1,
        "nontriviality": 1,
        "novelty": 0,
        "verifiability": 1
      },
      "unknowns": [
        "事件持续/间隔",
        "实测切换成本",
        "既有恢复调度/公平方法覆盖"
      ],
      "judgment": "补证据入口，不是已确认空白或已选题。"
    }
  ],
  "recommended_C": {
    "connection_id": "C02",
    "purpose": "后续补证据的入口，未选定方案。"
  },
  "first_low_cost_check": "先沿来源比较“任务业务约定—执行动作—动作代价—评价窗口”，补读最近邻；实测前单独形成有版本的诊断Idea。",
  "evidence_status": "partial",
  "evidence_locations": [
    "../../papers/acun_2026_dr/analysis_v01.md",
    "../../papers/colangelo_2025_grid_interactive/analysis_v01.md",
    "../../papers/li_2026_powerslider/analysis_v01.md"
  ],
  "missing_evidence": [
    "没有本机3090、网络、任务合同或响应事件实测。",
    "仅三个种子来源，尚未充分对照训练弹性/重启公平/跨节点通信/预测文献。",
    "S03部分合同和动态结果口径待澄清，本矩阵不使用有争议数字。",
    "领域内的新颖性及产业事件频率没有由本轮建立。"
  ],
  "next_skill": "discover-innovation",
  "handoff_ready": false,
  "actual_next_stage": "analyze-paper / deep-read Mode C 增量补证据；当前不进入正式Idea或迁移。"
}
```
