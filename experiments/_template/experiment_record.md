# 实际实验记录包模板

> 空白模板，不是有效交接包。复制到对应归档路径后填写；未知写明缺项，不适用写明原因。必填 null/UNSET 未处理前，不可宣称阶段完成。

```json
{
  "package_type": "EXPERIMENT_RECORD",
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
  "next_skill": "squeeze-experiment",
  "experiment_id": "UNSET",
  "source_idea_id": null,
  "plan_version": null,
  "execution_status": null,
  "research_question_and_hypothesis": null,
  "planned_vs_actual_design_and_controls": null,
  "actual_configuration": null,
  "samples_materials_or_data_provenance": null,
  "all_runs_or_repetitions": [],
  "results_with_uncertainty": null,
  "observations_and_deviations": [],
  "known_limits_and_missing_evidence": null,
  "next_routes": null
}
```

只有实际执行后才填写事实；按计划值/实际值列偏差，保留所有 run、失败与排除。先记录再深挖。

涉及迁移时，关联 Idea 中的 source_transfer_id；逐组记录 A、A′、A′+B 的实际实现、条件/机制测量、成本和偏差，等价组可合并并说明。未运行组明确写未执行，不能由最终总指标推断源方法有效。

同时记录局部改善、整体目标、约束损失和代价；对照事前回退触发条件，说明实际保留/回退的配置与原始证据位置。结果无明显改善时保留新增信息及未排除的解释，不只写“没涨”。

字段完整性和上游要求见 [本仓库流程](../../RESEARCH_WORKFLOW.md) 及其中链接的本地技能原文。
