# 选题层

这里记录“准备研究什么，以及为什么值得继续”。当前没有已确定的论文题目。

推进遵循 [正式流程](../RESEARCH_WORKFLOW.md)：先有来源分析与 Mode C，再选一个 C 形成 [IDEA_PACKAGE](_templates/idea_package.md)。Idea 按 `ideas/<topic_id>_idea_vNN.md` 保存；每次更新 [推进台账](progress.md)。

涉及跨领域方法时先形成 [TRANSFER_PACKAGE](_templates/transfer_package.md)，按 `transfers/<topic_id>_transfer_vNN.md` 保存，再交回 Idea 设计。迁移报告逐项核验机制和生效条件，分别验证适配 A′ 与源方法 B 的贡献；不以组件拼装代替创新判断。

- [当前计算机侧探索](exploration_2026-11-10.md)：学科边界、单个诊断Idea与下一步证据门槛；未确定最终选题。
- [候选题台账](candidates.md)：统一候选 ID、核心假设、近邻、验证门槛与当前状态。
- [导师讨论一页稿](导师讨论_一页选题建议.md)：现有调研形成的讨论材料，不代表导师已定题。
- [决策记录](decisions.md)：只记录已经确认的选择、依据与后续调整。
- 证据入口：[完整调研](../research/surveys/gpu_power_scheduling_2026/供电约束下的GPU调度_产业与研究调研.md)。

## 维护方式

候选使用稳定 ID（如 `T01`）；实验通过 ID 引用，改标题不改 ID。状态可用 `candidate / investigating / selected / deferred / rejected`。只有实际确认的选择才标记 `selected`；完成某个实验不自动代表选题成立。

每个候选至少说明：具体问题、最近邻差异、待验证机制、最小实验、继续/停止条件、证据链接。若后续需要展开，创建 `T01_<short_name>.md` 并从台账链接，避免不断复制综述。

新实验结果要回写判断，并链接实际运行；导师或用户作出选择时，再追加决策记录。失败实验也是选题依据，不删去导致放弃方向的证据。
