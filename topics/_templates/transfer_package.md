# 跨领域迁移包模板

> 空白模板，不是已验证方案。接收 Mode C 的单个连接点，核验机制、条件、适配和归因；当前只问可迁移性时可只填写相关判断，正式 Idea 须有矩阵血缘。

```json
{
  "package_type": "TRANSFER_PACKAGE",
  "package_version": "research-pipeline-contract v2.0",
  "package_id": "UNSET",
  "transfer_id": "UNSET",
  "created_at": null,
  "timezone": "Asia/Shanghai",
  "source_ids": [],
  "source_matrix_id": null,
  "connection_C_id": null,
  "target_A": {
    "task_and_baseline_version": null,
    "data_and_evaluation_protocol": null,
    "failure_observations": [],
    "candidate_mechanisms_and_alternatives": []
  },
  "source_B": {
    "source_ids_and_version": [],
    "original_problem": null,
    "core_mechanism_and_locations": null,
    "enabling_conditions": [],
    "failure_boundaries": []
  },
  "transfer_case_ids": [],
  "case_adaptations_and_limits": [],
  "condition_gap_table": [],
  "adapted_A_prime": null,
  "preserved_or_modified_B": null,
  "transfer_hypothesis": null,
  "attribution_design": {
    "A": null,
    "A_prime": null,
    "A_prime_plus_B": null,
    "additional_controls_and_justification": []
  },
  "condition_and_mechanism_measurements": [],
  "decision": null,
  "evidence_status": "unknown",
  "evidence_locations": [],
  "missing_evidence": [
    "未填写模板；不代表已核验迁移有效性。"
  ],
  "next_skill": "discover-innovation"
}
```

`connection_C_id` 是 Mode C 连接点；`transfer_case_ids` 是已有迁移案例，二者不可混用。无案例时注明检索范围，不能声称没人迁移过。

条件逐项填“已满足／可建立／不满足／未知”，并写测量方法。A′ 无必要改动时允许与 A 相同，合并相应组，不能人为制造适配收益。附加 A+B 或破坏条件等对照由假设决定。

计划一次只改变一个关键因素；分别量化 A→A′ 的适配收益和 A′→A′+B 的条件性增量，结合机制测量与成本判断。

完整字段解释见 [迁移交接契约](../../research/standards/extensions/cross_domain_2026-11-05/research-workflow/references/six-skill-contract.md)；阶段位置见 [仓库流程](../../RESEARCH_WORKFLOW.md)。
