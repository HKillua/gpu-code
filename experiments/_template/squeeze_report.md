# 实验深挖与回流包模板

> 空白模板，不是有效交接包。复制到对应归档路径后填写；未知写明缺项，不适用写明原因。必填 null/UNSET 未处理前，不可宣称阶段完成。

```json
{
  "package_type": "SQUEEZE_REPORT",
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
  "squeeze_id": "UNSET",
  "input_experiment_record_ids": [],
  "claim_scope": null,
  "data_quality_and_provenance_gaps": null,
  "primary_result_with_uncertainty": null,
  "process_dynamics": null,
  "mechanism_and_intermediate_findings": null,
  "errors_heterogeneity_and_boundary_conditions": null,
  "robustness_sensitivity_and_alternative_explanations": null,
  "new_P": [],
  "new_F": [],
  "new_M": [],
  "evidence_index": [],
  "open_questions": [],
  "next_routes": null,
  "mode_C_input_bundle": {
    "source_ids": [],
    "P": [],
    "F": [],
    "M": [],
    "E": [],
    "missing_evidence": []
  }
}
```

核对原始证据后分析动态、机制线索与替代解释；新 P/F/M/E 回流 Mode C，不能直接跳成下一轮 Idea。

涉及迁移时，分别分析适配收益、B 在 A′ 中的增量、条件/机制预测与整体成本。指标提高而机制预测未出现时保留竞争解释；形成新 P/F/M/E 后先增量更新 Mode C。

科研死循环检查：分析局部收益为何传递或未传递到整体，副作用/成本、噪声、交互的证据分别是什么；结论说明本轮新增知识、最有区分力的下一检查，以及保留/回退/停止依据，不自动在旧补丁上追加机制。

字段完整性和上游要求见 [本仓库流程](../../RESEARCH_WORKFLOW.md) 及其中链接的本地技能原文。
