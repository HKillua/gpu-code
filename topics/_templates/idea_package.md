# Idea 与最小验证包模板

> 空白模板，不是有效交接包。复制到对应归档路径后填写；未知写明缺项，不适用写明原因。必填 null/UNSET 未处理前，不可宣称阶段完成。

```json
{
  "package_type": "IDEA_PACKAGE",
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
  "next_skill": "record-experiment",
  "idea_id": "UNSET",
  "source_matrix_id": null,
  "selected_C_id": null,
  "source_P_ids": [],
  "source_F_ids": [],
  "source_M_ids": [],
  "source_E_ids": [],
  "research_question": null,
  "hypothesis_and_mechanism": null,
  "testable_predictions": [],
  "design_and_controls": null,
  "sample_materials_or_experimental_units": null,
  "primary_outcome_and_estimand": null,
  "secondary_outcomes": [],
  "analysis_plan_and_uncertainty": null,
  "resources_time_cost_and_risk": null,
  "ethics_safety_privacy_or_compliance": null,
  "stopping_and_interpretation_rules": null,
  "status": null,
  "source_transfer_id": null,
  "transfer_condition_commitments": null,
  "adaptation_vs_method_attribution": null,
  "plan_version": null
}
```

只选一个 C；补齐单变量、代码修改范围、预测、基线、主要指标、不确定性与各种结果的解释规则，再实施。

stopping_and_interpretation_rules 中明确成功、无差异、局部改善但整体无收益、代价不可接受的判据；写回退目标版本/配置、触发条件和需保留的观察。secondary_outcomes 与 resources_time_cost_and_risk 覆盖副作用、资源和复杂度代价，不能只看目标模块指标。

涉及跨领域迁移时引用 source_transfer_id，填写条件承诺、适配与方法贡献的归因设计及 plan_version；未走迁移分支写明不适用和原因。Idea 包保持 v1.0，新增字段是 v2.0 总控补充，不能倒改旧包版本。

字段完整性和上游要求见 [本仓库流程](../../RESEARCH_WORKFLOW.md) 及其中链接的本地技能原文。
