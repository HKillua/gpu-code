# 来源分析包模板

> 空白模板，不是有效交接包。复制到对应归档路径后填写；未知写明缺项，不适用写明原因。必填 null/UNSET 未处理前，不可宣称阶段完成。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
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
  "next_skill": "deep-read Mode C",
  "analysis_id": "UNSET",
  "source_identity_and_version": null,
  "research_question": null,
  "study_type_and_context": null,
  "design_units_samples_or_materials": null,
  "intervention_input_or_procedure": null,
  "measurements_and_analysis": null,
  "results_with_uncertainty": null,
  "claim_evidence_implementation_map": null,
  "limitations_biases_and_ethics": null,
  "reproducibility_and_transferability": null,
  "candidate_P": [],
  "candidate_F": [],
  "evidence_index": [],
  "unresolved_questions": []
}
```

逐项定位原文、图表或实现；候选 P/F 还须由 Mode C 重新核验。未访问材料与材料不存在分开。

字段完整性和上游要求见 [本仓库流程](../../RESEARCH_WORKFLOW.md) 及其中链接的本地技能原文。
