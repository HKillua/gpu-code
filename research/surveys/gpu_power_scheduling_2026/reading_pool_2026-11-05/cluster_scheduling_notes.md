# GPU 集群调度基础阅读池

8 篇均已核验正式书目与摘要，覆盖 2018–2026 年。前三篇另外读了方法和评估正文，分析包均为 `partial`。其余五篇保留为摘要初筛。条目以训练为主，靠后的生产案例同时含开发、训练、在线及离线推理。检索按概念选取，完整书目与逐项阅读范围见[机器可读清单](cluster_scheduling.json)。

## 建议先读的三篇

1. **LS01，Tiresias（NSDI 2019）。** 先问“前面的长作业会不会把短作业堵住”。§5.2 图 10–11 并排阅读，两个策略的总体利用率相近，平均完成时间却明显不同。再读 §3.2 的队列规则，分清业务上的紧急和调度器按已获服务计算的优先级。[论文和书目](https://www.usenix.org/conference/nsdi19/presentation/gu) · [图 10–11](https://www.usenix.org/system/files/nsdi19-gu.pdf#page=11) · [分析包](../../../papers/gu_2019_tiresias/analysis_v01.md)
2. **LS02，Themis（NSDI 2020）。** 再问“哪一个用户在共享时吃了亏”。§3.2 定义共享时的完成时间相对独占公平份额的比值，排队和同机/跨机放置都会影响分子。§6.2 的 2.2–3.25 倍指不同方案之间最差应用的这一比值。把平均等待降下来和保护最差者，可以是两个独立目标。[论文和书目](https://www.usenix.org/conference/nsdi20/presentation/mahajan) · [比值定义](https://www.usenix.org/system/files/nsdi20-paper-mahajan.pdf#page=5) · [分析包](../../../papers/mahajan_2020_themis/analysis_v01.md)
3. **LS03，Pollux（OSDI 2021）。** 最后问“重新分卡后，有效训练进度多了多少”。每秒处理样本数与每个样本带来的训练进展都重要。重配还涉及时间损失。正文的 15–120 秒是作者实测的 checkpoint-restart 延迟范围，30 秒是调度器评估时采用的估计参数，8% 是该组实机运行报告的平均运行时开销。[论文和书目](https://www.usenix.org/conference/osdi21/presentation/qiao) · [重配代价](https://www.usenix.org/system/files/osdi21-qiao.pdf#page=10) · [参数与评估](https://www.usenix.org/system/files/osdi21-qiao.pdf#page=11) · [分析包](../../../papers/qiao_2021_pollux/analysis_v01.md)

## 带着问题浏览的五篇

| 编号与工作 | 摘要支持的阅读入口 | 进一步要核验什么 |
|---|---|---|
| LS04 Gandiva，OSDI 2018 | 小批次重复让早期反馈、分时和迁移有可利用的规律。 | 迭代边界上切换的代价如何随作业变化？[正式摘要](https://www.usenix.org/conference/osdi18/presentation/xiao) |
| LS05 Gavel，OSDI 2020 | 用每个作业在不同硬件上的有效吞吐，表达已有的不同调度目标。 | 这些速率怎样测，轮次执行与理想分配相差多少？[正式摘要](https://www.usenix.org/conference/osdi20/presentation/narayanan-deepak) |
| LS06 Sia，SOSP 2023 | 把作业弹性和资源异构放在同一分配问题里，覆盖弹性混合并行。 | 哪些训练能改配置，新作业需要多少初始测量？[正式书目](https://doi.org/10.1145/3600006.3613175) · [作者公开正式稿](https://suhasjs.github.io/files/sia-sosp23.pdf) |
| LS07 PCS，OSDI 2024 | 为用户报预计完成时间时，可预测性本身也是目标。 | 预计时间误差怎样算，需为准确预测牺牲多少效率或公平？[正式摘要](https://www.usenix.org/conference/osdi24/presentation/bin-faisal) |
| LS08 Alibaba ASI / SpotGPU，OSDI 2026，Operational Systems Paper | 六个月轨迹覆盖 155,410 张 GPU、81 个部门。作者把闲置资源与抢占成本联系起来，并报告部署后 GPU 分配率从 68% 升至 93%。 | 分配率的分母、观测窗口、实际计算忙碌度和服务质量各如何界定？[正式摘要](https://www.usenix.org/conference/osdi26/presentation/li-suyi) |

## 这组文献能帮助回答的部分

在两台各三卡的 RTX 3090 服务器上，可以先学会读出三种不同代价：Tiresias 的提交至完成时间、Themis 的相对共享代价、Pollux 的有效训练进度和重配损失。训练吞吐、卡分配率与整机功率各有自己的单位。CPU 配套、网络和同机放置也可能让“存在空闲 GPU”与“某个作业此刻可以启动”出现差别。[Tiresias](https://www.usenix.org/conference/nsdi19/presentation/gu) · [Themis](https://www.usenix.org/conference/nsdi20/presentation/mahajan) · [Pollux](https://www.usenix.org/conference/osdi21/presentation/qiao) · [Alibaba ASI](https://www.usenix.org/conference/osdi26/presentation/li-suyi)

这八篇的核心输入是集群资源与应用工作量。外部给定的节点或机房功率上限会引入额外输入；把上述方法用于该环境还需说明上限的测量边界、任务可让出的工作量以及恢复成本。原论文报告的百分比和时间不能换算成六卡上的瓦数。本组只做文献阅读，未运行调度器或测量本地硬件。
