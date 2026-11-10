# On the Limitations of Carbon-Aware Temporal and Spatial Workload Shifting in the Cloud｜广读分析 v01

EuroSys 2024，pp.924–941，DOI 10.1145/3627703.3650079。核验日期：2026-11-05。

在理想与受限条件下，时间/空间迁移的碳收益到底有多大，哪些约束会压缩收益？

本轮读正文重点章节，未独立复现。以下区分来源主张、可定位证据与项目推断。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-025-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LC05"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LC05-E1",
    "LC05-E2",
    "LC05-E3"
  ],
  "missing_evidence": [
    "未审阅6.6GB工件、未独立运行分析",
    "没有本机任务功率与状态开销数据",
    "缺真实预测发布版本和近年目标地区轨迹"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-025-v01",
  "source_identity_and_version": {
    "title": "On the Limitations of Carbon-Aware Temporal and Spatial Workload Shifting in the Cloud",
    "authors": "Thanathorn Sukprasert, Abel Souza, Noman Bashir, David Irwin, Prashant Shenoy",
    "venue": "EuroSys 2024，pp.924–941，DOI 10.1145/3627703.3650079",
    "official_url": "https://2024.eurosys.org/accepted-papers.html",
    "pdf_url": "https://lass.cs.umass.edu/papers/pdf/eurosys24-shiftinglimitations.pdf",
    "reading_depth": "正文重点阅读：问题、方法、评估与边界；未独立复现/非逐页证明精读",
    "local_pdf": "paper.pdf",
    "local_text": "text.txt"
  },
  "research_question": "在理想与受限条件下，时间/空间迁移的碳收益到底有多大，哪些约束会压缩收益？",
  "study_type_and_context": "数据驱动的上界与情景分析，不是GPU实机调度器或需求响应部署。",
  "design_units_samples_or_materials": "§3：123地区2020–2022小时碳轨迹，覆盖批处理与交互抽象；遍历小时边界到达、不同作业长度、slack、迁移约束。Table1中状态切换和空间迁移开销置零。",
  "intervention_input_or_procedure": "§3.2：未来碳信号和任务长度全知；可延期作业寻找连续低碳槽，可中断作业选择k个最低槽；空间维度比较区域选择/迁移策略。§6.2以人工均匀噪声评估预测误差，不是训练了真实预测模型。",
  "measurements_and_analysis": "绝对减排和相对全局平均碳强度的变化；报告不同到达时间的均值与离散程度。需核对归一化分母，不能把每kWh碳强度差等同集群完成同样工作的总减排。",
  "results_with_uncertainty": [
    {
      "mark": "SC",
      "text": "SC｜§5、§8：约束容量、时延、可延期窗口后，收益与理想全知情形存在明显差距，部分简单策略已能取得大部分模型内收益。此结论受历史地区/工作抽象影响；未在GPU上独立复现，不推出所有未来调度都无价值。",
      "evidence": "LC05-E3"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "text": "SC：量化收益边界。DE：§3/Table1/§3.2公开零切换成本与全知假设。AI：先读上界有助于检查拟议复杂策略是否存在足够改进空间。UN：真实GPU动作、发布时间预测与更近年份能源数据。"
    }
  ],
  "limitations_biases_and_ethics": "旧年份平均碳强度、小时尺度、零迁移成本和理想资源/任务信息；非因果电网排放研究，不反映额外负荷的边际排放。伦理/数据许可仅见公开来源说明，本轮未逐项审查。",
  "reproducibility_and_transferability": {
    "artifact_audit": "未运行或完成实现审计；工件存在不等于复现成功",
    "principles_and_boundaries": "可借鉴先量化可获得收益、使用简单强基线和限制未来信息。两机六卡只能做局部重放，不能实现跨123地区实机迁移。"
  },
  "candidate_P": [
    {
      "id": "LC05-P1",
      "mark": "AI",
      "text": "目标地区、可延期窗口与容量约束下，复杂调度还有多少实际改进余量？",
      "evidence": "LC05-E1"
    }
  ],
  "candidate_F": [
    {
      "id": "LC05-F1",
      "mark": "DE",
      "text": "DE｜Table1/§3.2的全知与零切换成本属于上界条件；§6.2人工预测误差不是带发布时间的真实预测。",
      "evidence": "LC05-E1"
    }
  ],
  "evidence_index": [
    {
      "id": "LC05-E1",
      "where": "§3.1/Table1/§3.2，PDF pp.4–5；轨迹、零开销与全知",
      "url": "https://lass.cs.umass.edu/papers/pdf/eurosys24-shiftinglimitations.pdf"
    },
    {
      "id": "LC05-E2",
      "where": "§5、§5.3，PDF pp.7–12；约束敏感性与设计含义",
      "url": "https://lass.cs.umass.edu/papers/pdf/eurosys24-shiftinglimitations.pdf"
    },
    {
      "id": "LC05-E3",
      "where": "§6.2、§8，PDF pp.13–14；人工误差与结论边界",
      "url": "https://lass.cs.umass.edu/papers/pdf/eurosys24-shiftinglimitations.pdf"
    }
  ],
  "unresolved_questions": [
    "未审阅6.6GB工件、未独立运行分析",
    "没有本机任务功率与状态开销数据",
    "缺真实预测发布版本和近年目标地区轨迹"
  ]
}
```
