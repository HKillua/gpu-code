# 实验试错层

当前没有真实运行记录。`_template/` 仅是计划模板，不是第一次实验。

先读 [正式流程](../RESEARCH_WORKFLOW.md)。实际运行前需要可追溯的 Idea/计划；运行后先生成 [EXPERIMENT_RECORD](_template/experiment_record.md)，再按证据生成 [SQUEEZE_REPORT](_template/squeeze_report.md)，随后回到 Mode C。目录状态不代替包的 `execution_status/evidence_status`。

## 一次实验怎么组织

```text
experiments/
├── _template/README.md
└── YYYYMMDD_short_name/
    ├── README.md              # 假设、状态、方法、结论与运行索引
    ├── config.yaml            # 或实际采用的其他可读配置格式
    ├── run.py                 # 仅在需要专用入口时建立
    ├── analysis/              # 可重现分析脚本和小型汇总
    └── runs/<run_id>/         # 实际配置、环境、原始日志与指标
```

从 [模板目录](./_template/) 复制开始，先填写计划；实验记录和深挖包仅在对应阶段填入实际证据。日期使用 Asia/Shanghai；run ID 带时间或唯一序号，避免重跑覆盖。

## 最小闭环

1. 关联选题 ID；先写假设、基线、主要指标、停止条件与资源需求。
2. 核验目标设备和现有作业，记录软件版本、拓扑与计量边界。
3. 首次运行先短时冒烟，记录成功或失败；完整运行使用独立 run ID。
4. 保存当次实际配置、命令、环境/代码版本和原始输出；分析结果引用这些输入。
5. 按 `record-experiment → squeeze-experiment → deep-read Mode C` 顺序沉淀证据，再更新 [候选台账](../topics/candidates.md) 与 [推进台账](../topics/progress.md)；负结果保留，新 Idea 需经新的矩阵与连接点。

状态为 `planned / running / completed / failed / inconclusive / stopped`。完成运行与结论成立是两个判断。进度、失败原因和下一步写在该实验 README；同一假设换参数可增加新 run，大幅改变问题则另建实验目录。

`runs/`、模型与 checkpoint 默认忽略 Git；小型配置、运行索引、分析代码和汇总结果放在可版本化位置。忽略 Git 不等于删除数据：重要原始产物仍需记录实际存储位置、内容哈希和备份方式。
