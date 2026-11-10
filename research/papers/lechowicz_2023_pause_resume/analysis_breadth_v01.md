# The Online Pause and Resume Problem: Optimal Algorithms and An Application to Carbon-Aware Load Shifting｜广读分析 v01

POMACS 7(3), Article45, 2023；SIGMETRICS/Performance 2024展示；DOI 10.1145/3626776。核验日期：2026-11-05。

未来代价未知时，怎样在截止期前完成固定工作量，同时控制每次暂停/恢复的切换成本？

本轮读正文重点章节，未独立复现。以下区分来源主张、可定位证据与项目推断。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-026-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LC07"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LC07-E1",
    "LC07-E2",
    "LC07-E3",
    "LC07-E4"
  ],
  "missing_evidence": [
    "未逐行独立验证证明、未运行代码或数据",
    "无GPU状态与动作开销实测",
    "异构成本/弹性/预测增强后续文献未穷尽"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-026-v01",
  "source_identity_and_version": {
    "title": "The Online Pause and Resume Problem: Optimal Algorithms and An Application to Carbon-Aware Load Shifting",
    "authors": "Adam Lechowicz, Nicolas Christianson, Jinhang Zuo, Noman Bashir, Mohammad Hajiesmaili, Adam Wierman, Prashant Shenoy",
    "venue": "POMACS 7(3), Article45, 2023；SIGMETRICS/Performance 2024展示；DOI 10.1145/3626776",
    "official_url": "https://www.sigmetrics.org/opentoc/acm23_vol7_issue3_toc.html",
    "pdf_url": "https://noman-bashir.github.io/assets/pdf/SIGMETRICS-2024-DTPR.pdf",
    "reading_depth": "正文重点阅读：问题、方法、评估与边界；未独立复现/非逐页证明精读",
    "local_pdf": "paper.pdf",
    "local_text": "text.txt"
  },
  "research_question": "未来代价未知时，怎样在截止期前完成固定工作量，同时控制每次暂停/恢复的切换成本？",
  "study_type_and_context": "在线算法理论、竞争分析和假想数据中心上的真实碳trace数值案例；不是GPU集群实机。",
  "design_units_samples_or_materials": "§2：已知k、T、固定切换成本β、价格上下界L/U，每槽二元决策。§6：三地区小时碳轨迹，48/72/96小时horizon、随机取连续片段；闲置碳排放简化为0。",
  "intervention_input_or_procedure": "§3 DTPR：当前运行与暂停状态采用不同阈值，防止轻微波动反复切换；临近截止期强制完成剩余工作。§§4–5推导竞争界与下界，本轮未逐行审证；§6用动态规划离线OPT与立即执行、常阈值、k-search比较。",
  "measurements_and_analysis": "模型内总碳成本含固定β切换代价，报告经验竞争比与CDF。§6.1 L/U使用整段历史trace极值，不能说完全无需先验信息；没有测GPU保存/恢复的耗时或焦耳。",
  "results_with_uncertainty": [
    {
      "mark": "SC",
      "text": "SC｜理论最优性限定为该二元、固定成本、已知需求/截止期/有界信号模型中的确定性在线竞争界。§6/Figs5–8显示DTPR的设置内经验折中；不是每个输入都优于所有基线的声明。图中百分位描述分布，未由本轮核出独立重复置信区间。",
      "evidence": "LC07-E3"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "text": "SC：暂停恢复成本使双阈值有价值。DE：§2式1、Table1及§6.1显式计入β。AI：恢复/切换成本已存在理论最近邻，不能单凭把它加入GPU调度就声称创新。UN：状态依赖的时间/能量成本与并发业务适用性。"
    }
  ],
  "limitations_biases_and_ethics": "固定β、每槽单位工作、单作业截止期、闲置零排放简化；不包含GPU显存驻留、并行卡数、通信和在线请求SLO。§8已将弹性/异构切换成本及预测增强列为后续方向，其后续文献尚需核验。伦理细节未见单独核验。",
  "reproducibility_and_transferability": {
    "artifact_audit": "未运行或完成实现审计；工件存在不等于复现成功",
    "principles_and_boundaries": "可借鉴明确先验信息、双阈值强基线和把切换代价入目标的建模。未选择源方法B或形成迁移包；真实GPU状态与标量β的关系需要证据。"
  },
  "candidate_P": [
    {
      "id": "LC07-P1",
      "mark": "AI",
      "text": "GPU真实状态依赖的切换代价，在什么情况下超出了固定β近邻模型可解释的范围？",
      "evidence": "LC07-E1"
    }
  ],
  "candidate_F": [
    {
      "id": "LC07-F1",
      "mark": "DE",
      "text": "DE｜§2/§6明确把切换成本纳入；§8也明确提出弹性与异构切换成本扩展，不能把作者未来工作清单当作今天未被解决的空白。",
      "evidence": [
        "LC07-E1",
        "LC07-E4"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "LC07-E1",
      "where": "§2/Table1/式1，PDF pp.4–6；模型和先验",
      "url": "https://noman-bashir.github.io/assets/pdf/SIGMETRICS-2024-DTPR.pdf"
    },
    {
      "id": "LC07-E2",
      "where": "§3/Alg1、§§4–5；双阈值和作者理论声明",
      "url": "https://noman-bashir.github.io/assets/pdf/SIGMETRICS-2024-DTPR.pdf"
    },
    {
      "id": "LC07-E3",
      "where": "§6.1–6.2，PDF pp.17–21；假想集群、真实trace与数值结果",
      "url": "https://noman-bashir.github.io/assets/pdf/SIGMETRICS-2024-DTPR.pdf"
    },
    {
      "id": "LC07-E4",
      "where": "§8，PDF p.22；异构切换、弹性和预测增强展望",
      "url": "https://noman-bashir.github.io/assets/pdf/SIGMETRICS-2024-DTPR.pdf"
    }
  ],
  "unresolved_questions": [
    "未逐行独立验证证明、未运行代码或数据",
    "无GPU状态与动作开销实测",
    "异构成本/弹性/预测增强后续文献未穷尽"
  ]
}
```
