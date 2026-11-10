# Mode C 交叉矩阵包模板

> 空白模板，不是有效交接包。复制到对应归档路径后填写；未知写明缺项，不适用写明原因。必填 null/UNSET 未处理前，不可宣称阶段完成。

```json
{
  "package_type": "MODE_C_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "UNSET",
  "created_at": null,
  "timezone": "Asia/Shanghai",
  "source_ids": [],
  "evidence_status": "unknown",
  "evidence_locations": [],
  "missing_evidence": [
    "未填写的模板；不表示阶段已完成或已有证据。"
  ],
  "next_skill": "discover-innovation",
  "matrix_id": "UNSET",
  "scope_and_inclusion_rules": null,
  "source_map": [],
  "P": [],
  "F": [],
  "M": [],
  "E": [],
  "P_by_F_matrix": null,
  "C": [],
  "recommended_C": null,
  "first_low_cost_check": null
}
```

P/F/M/E/C 使用稳定 ID；条目标记 SC/DE/AI/UN；矩阵单元给出状态、理由、证据 ID，C 仅为连接点而非完整方法。

选定连接点后，若涉及跨领域方法，推进台账记录实际下一阶段为 transfer-ml-methods；保留原包 next_skill，不静默改写历史。无迁移需求时注明不适用，直接进入 Idea 设计。

字段完整性和上游要求见 [本仓库流程](../../RESEARCH_WORKFLOW.md) 及其中链接的本地技能原文。
