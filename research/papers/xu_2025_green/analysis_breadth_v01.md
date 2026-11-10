# GREEN: Carbon-efficient Resource Scheduling for Machine Learning Clusters｜广读分析 v01

NSDI 2025，pp.999–1014。核验日期：2026-11-05。

固定容量的多租户ML集群，如何在控制作业完成时间损失的同时减少运行碳排放？

本轮读正文重点章节，未独立复现。以下区分来源主张、可定位证据与项目推断。

```json
{
  "package_type": "ANALYSIS_PACKAGE",
  "package_version": "research-pipeline-contract v1.0",
  "package_id": "AN-20261105-023-v01",
  "created_at": "2026-11-05",
  "timezone": "Asia/Shanghai",
  "source_ids": [
    "LC02"
  ],
  "evidence_status": "partial",
  "evidence_locations": [
    "LC02-E1",
    "LC02-E2",
    "LC02-E3"
  ],
  "missing_evidence": [
    "未运行Slurm插件、未审阅完整代码工件",
    "本机功率接口、真实碳/预算信号与业务合同未知",
    "未检验完整训练质量与每个配置的统计区间"
  ],
  "next_skill": "deep-read Mode C",
  "analysis_id": "AN-20261105-023-v01",
  "source_identity_and_version": {
    "title": "GREEN: Carbon-efficient Resource Scheduling for Machine Learning Clusters",
    "authors": "Kaiqiang Xu, Decang Sun, Han Tian, Junxue Zhang, Kai Chen",
    "venue": "NSDI 2025，pp.999–1014",
    "official_url": "https://www.usenix.org/conference/nsdi25/presentation/xu-kaiqiang",
    "pdf_url": "https://www.usenix.org/system/files/nsdi25-xu-kaiqiang.pdf",
    "reading_depth": "正文重点阅读：问题、方法、评估与边界；未独立复现/非逐页证明精读",
    "local_pdf": "paper.pdf",
    "local_text": "text.txt"
  },
  "research_question": "固定容量的多租户ML集群，如何在控制作业完成时间损失的同时减少运行碳排放？",
  "study_type_and_context": "系统设计、32节点实机回放与一年trace驱动仿真；主要工作是无硬截止期的训练作业，并非硬功率上限控制器。",
  "design_units_samples_or_materials": "§8.1：32节点，每节点2张RTX3090、20 CPU核、64GB内存、ConnectX5 NIC。791个真实用户作业按原到达时间回放；Pollux/Zeus对照仅使用适配成功的150个作业。§8.3：Alibaba任务轨迹映射到预测任务类别，构造一年仿真。",
  "intervention_input_or_procedure": "§§4–6：GPU NVML读数、CPU功率模型和静态负载估计碳；用单位能量进度变化指导弹性卡数，上层队列作在线profiling，下层按功率与碳强度重排优先级。§7：Slurm插件，SIGTERM后120秒保存宽限，checkpoint和重启时间计入执行。",
  "measurements_and_analysis": "JCT包含排队、抢占与执行；碳排放由估计功率乘历史碳强度积分。§8.1提供48小时典型CIC；长时仿真每日更新。不能当作无未来信息的实时功率保证。",
  "results_with_uncertainty": [
    {
      "mark": "SC",
      "text": "SC｜§8.2/Fig.8：作者报告相对最优时间基线Tiresias，平均JCT增加3.6%–5.9%，尾JCT增加5.1%–7.1%，碳减排最高41.2%。这是四地区历史信号回放的设置内结果；不是3090单卡通用节能比例，正文未由本轮核出置信区间。",
      "evidence": "LC02-E3"
    }
  ],
  "claim_evidence_implementation_map": [
    {
      "text": "SC：兼顾时间与碳效率。DE：§8.1两类基线使用的作业集合不同，§8.2.3单独比较150个可适配作业。AI：读全量结果和模型可适配子集时必须保持分母。UN：独立运行与本机效应。"
    }
  ],
  "limitations_biases_and_ethics": "固定集群/历史碳信号、功率估计误差、部分基线移植和子集兼容性影响比较。没有在线请求尾延迟合同；不能把降低峰值等同硬cap达标。研究伦理/真实用户数据处理细节本轮未核验。",
  "reproducibility_and_transferability": {
    "artifact_audit": "未运行或完成实现审计；工件存在不等于复现成功",
    "principles_and_boundaries": "可借鉴完整JCT、固定容量和状态开销计账；RTX3090硬件贴近但网络/节点数量差异显著。不能将64卡结果或曲线转写成本项目实测。"
  },
  "candidate_P": [
    {
      "id": "LC02-P1",
      "mark": "AI",
      "text": "固定容量下，单纯把高功率任务推迟是否会产生积压，怎样与其他任务竞争？",
      "evidence": "LC02-E1"
    }
  ],
  "candidate_F": [
    {
      "id": "LC02-F1",
      "mark": "DE",
      "text": "DE｜作者实现已把checkpoint与重启计入JCT；恢复成本不是该系统忽略的通用空白。",
      "evidence": [
        "LC02-E2"
      ]
    }
  ],
  "evidence_index": [
    {
      "id": "LC02-E1",
      "where": "§4、§8.1，PDF pp.6/10；功率计量、CIC与资源",
      "url": "https://www.usenix.org/system/files/nsdi25-xu-kaiqiang.pdf"
    },
    {
      "id": "LC02-E2",
      "where": "§§5–7，PDF pp.6–10；弹性与双层队列、保存宽限",
      "url": "https://www.usenix.org/system/files/nsdi25-xu-kaiqiang.pdf"
    },
    {
      "id": "LC02-E3",
      "where": "§8.1–8.3，PDF pp.10–12；作业分母及结果",
      "url": "https://www.usenix.org/system/files/nsdi25-xu-kaiqiang.pdf"
    }
  ],
  "unresolved_questions": [
    "未运行Slurm插件、未审阅完整代码工件",
    "本机功率接口、真实碳/预算信号与业务合同未知",
    "未检验完整训练质量与每个配置的统计区间"
  ]
}
```
