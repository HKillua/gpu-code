# SPADE: Signal-Aware DAG Scheduling and Dynamic Provisioning for Data Processing Clusters｜广读分析 v01

OSDI 2026，pp.1205–1222。核验日期：2026-11-05。

外部信号随时间变化时，如何联合DAG任务选择与资源供给，避免延期关键任务阻塞下游？

本轮读正文重点章节，未独立复现。以下区分来源主张、可定位证据与项目推断。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-024-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LC03"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LC03-E1",
    "LC03-E2",
    "LC03-E3"
  ],
  "missing_evidence": [
    "未运行代码或重新生成图表",
    "未核验真实功率计与GPU适配",
    "48槽lookahead的预测误差与发布时刻未进一步审计"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-024-v01",
  "source_identity_and_version": {
    "title": "SPADE: Signal-Aware DAG Scheduling and Dynamic Provisioning for Data Processing Clusters",
    "authors": "Adam Lechowicz, Rohan Shenoy, Noman Bashir, Mohammad Hajiesmaili, Adam Wierman, Christina Delimitrou",
    "venue": "OSDI 2026，pp.1205–1222",
    "official_url": "https://www.usenix.org/conference/osdi26/presentation/lechowicz",
    "pdf_url": "https://www.usenix.org/system/files/osdi26-lechowicz.pdf",
    "reading_depth": "正文重点阅读：问题、方法、评估与边界；未独立复现/非逐页证明精读",
    "local_pdf": "paper.pdf",
    "local_text": "text.txt"
  },
  "research_question": "外部信号随时间变化时，如何联合DAG任务选择与资源供给，避免延期关键任务阻塞下游？",
  "study_type_and_context": "Spark/Kubernetes系统与Spark仿真；主要是CPU数据处理DAG，属于与GPU调度邻近的资源供给问题。",
  "design_units_samples_or_materials": "正文§5.3为51个VM：1个控制节点、50个worker，每worker2个executor，总计100 executor；不能把摘要的100-node直接记为100台GPU节点。TPC-H和Alibaba DAG，碳信号六地区2020–22小时轨迹；主原型10次试验/配置，额外异质性实验30次。",
  "intervention_input_or_procedure": "§3/Alg.1：用已有DAG评分器产生的相对重要性与外部信号共同设阈值，决定调度或暂缓；保留至少一项进度。SAP消融保留信号感知供给但用普通任务选择。§4：Spark stage选择与Kubernetes调度插件；限额下降不抢占运行任务。",
  "measurements_and_analysis": "§2.1明确区分signal-awareness与硬约束的signal-adherence，后者依赖外部执行层。§5.1能耗按满载常数×executor利用率建模；碳实验1分钟对应信号1小时。功率实验使用Google利用率trace导出的overloading代理，主要在仿真。未来值称未知，但上下界取48时间槽lookahead窗口；信息权限须单列。",
  "results_with_uncertainty": [
    {
      "mark": "SC",
      "text": "SC/DE定位｜§5.3/Table2：相对默认Spark/Kubernetes，原型平均碳目标降低32.9%、makespan为1.013；同节p95 JCT增加45.4%。10次重复，Fig.7–8阴影为标准差，非置信区间。整体吞吐接近不代表每个任务延迟接近。",
      "evidence": "LC03-E3"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "text": "SC：联合调度与供给改善折中。DE：Table2与Fig.6分别呈现总makespan和JCT尾部。AI：必须同时观察整体与个体损失。UN：GPU状态、板卡功率和交互SLO下是否成立。"
    }
  ],
  "limitations_biases_and_ethics": "CPU DAG与GPU训练/推理不同；利用率成本模型、时间压缩、lookahead边界和非抢占配额限制外推。本文不提供瞬时电力安全保证。未审阅代码/原日志或伦理细节。",
  "reproducibility_and_transferability": {
    "artifact_audit": "未运行或完成实现审计；工件存在不等于复现成功",
    "principles_and_boundaries": "可借鉴依赖结构、SAP消融、统一未来信息和全局/尾部指标的读法；尚未做跨领域方法适配，不能把DAG评分直接移植为GPU方案。"
  },
  "candidate_P": [
    {
      "id": "LC03-P1",
      "mark": "AI",
      "text": "外部信号导致资源减少时，忽略任务依赖的统一削减会不会放大下游等待？",
      "evidence": "LC03-E1"
    }
  ],
  "candidate_F": [
    {
      "id": "LC03-F1",
      "mark": "DE",
      "text": "DE｜§5.3整体makespan接近默认时，p95 JCT仍可明显增大；§2.1主动区分软目标与硬执行层。",
      "evidence": [
        "LC03-E1",
        "LC03-E3"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "LC03-E1",
      "where": "§2.1，PDF p.4；软目标与硬执行层",
      "url": "https://www.usenix.org/system/files/osdi26-lechowicz.pdf"
    },
    {
      "id": "LC03-E2",
      "where": "§3/Alg.1、§4，PDF pp.6–8；相对重要性与SAP",
      "url": "https://www.usenix.org/system/files/osdi26-lechowicz.pdf"
    },
    {
      "id": "LC03-E3",
      "where": "§5.1、§5.3/Table2/Fig6，PDF pp.9–11；51VM/100executor、计量、重复、尾部",
      "url": "https://www.usenix.org/system/files/osdi26-lechowicz.pdf"
    }
  ],
  "unresolved_questions": [
    "未运行代码或重新生成图表",
    "未核验真实功率计与GPU适配",
    "48槽lookahead的预测误差与发布时刻未进一步审计"
  ]
}
```
