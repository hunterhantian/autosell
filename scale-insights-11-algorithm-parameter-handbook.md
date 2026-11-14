# Scale Insights 11 种自动化算法参数手册

> 官方文档没有“上百个参数”的完整说明书。这里把文档里能找到的约 80 个参数按算法整理好，并按你的账户给出每种算法的起步配置建议（未应用到账户）。

> ⚠️ **目标 ACOS 提醒（2026-11-14 加注）：** 本手册的“建议起步配置”是按 **30% 目标 ACOS** 写的，与当前 ring banner 广告策略的 **45%–50% 目标 ACOS**（盈亏平衡约 57%）不一致。直接套用前，需要把所有基于目标 ACOS 的参数（目标 ACOS、出价算法、停词和恢复阈值等）按 45%–50% 重新计算。

## 目录

- 结论
- 每种算法文档写了多少参数
- 所有规则共用的设置
- 11 种算法参数目录
- 建议起步配置（未应用到账户）
- 小账户真正要管的参数
- 文档里的矛盾和空白
- 来源

## 结论

> **没有完整的参数说明书。** 官方文档按算法讲了用途、默认值、例子和几个关键开关，但没有一页列出所有字段。很多条件只出现在截图里，完整字段清单在登录后的规则编辑页和每种算法的批量导入 Excel 模板里。公开的 MCP 技能包也不带规则字段，字段由连上账户后的服务端提供。

我从 docs.scaleinsights.com 的 Algorithms 目录（11 个子目录、约 60 篇）、帮助中心、预设策略页和 2026 年各期发布说明里，一共整理出约 80 个有名字的参数，其中出价规则 21 个。创始人说“有一种算法超过 100 个参数”，但没点名是哪一种。我推测是出价规则：它的条件分四段，每段可以加很多指标和上下限，组合起来很容易过百。分时规则按 168 个小时格算也能过百，所以这只是推测。

## 每种算法文档写了多少参数

*按文档里有名字或有明确说明的设置计数；不含条件里可选的具体指标。*

| 算法 | 参数数 |
|---|---|
| 出价 Bidding | 21 |
| 位置 Placement | 12 |
| 收割 Import | 9 |
| 否定 Negative | 8 |
| 预算 Daily Budget | 8 |
| 唤醒 Revive | 7 |
| 黑白名单 | 4 |
| 启停 Status | 4 |
| 否定单词 | 3 |
| 分时 Dayparting | 2 |
| 默认出价 | 2 |

合计：80 个。

## 所有规则共用的设置

| 设置 | 说明 | 取值 |
|---|---|---|
| Priority 优先级 | 多条规则同时满足时谁先执行 | 文档两处说法相反，见下文 |
| Criteria Profile 条件档案 | 可复用的条件组：选指标，填 At least / At most | 条件之间是“且”的关系 |
| 条件档案 Accessibility | 哪些类型的规则能用这个档案 | 可设 |
| Days to Analyze / Ignore（全局） | 每种算法一套默认值，规则里没设就用全局 | 多数最多 180 天，出价 365，预算 90 |
| Strategic Objective 策略目标 | 把多条规则打包，一次挂到很多广告组 | 预算和位置规则不能打包 |
| 关联 ASIN | 广告组必须先关联一个托管 ASIN，规则才会生效 | 单 ASIN 广告组自动关联 |
| Preview / Strategy Simulator | 执行前预览改动；出价规则可用最近 60 天数据模拟 | 只读 |
| Bulk Import / Bulk Manage | 用 Excel 批量建、改规则和条件档案 | 模板字段就是最全的参数表，需登录下载 |

## 11 种算法参数目录

### Bidding 出价规则

| 项目 | 说明 |
|---|---|
| 做什么 | 按表现调关键词和商品投放的出价，或暂停/启用关键词 |
| 支持的广告 | SP 自动、SP 手动关键词和商品投放、SB、SD 全部支持 |
| 叠加方式 | 可叠加（多条按优先级执行） |
| 执行时间 | 每天一次 |
| 备注 | 文档写得最细的一种。规则编辑页把触发条件分成关键词、广告组合、活动、广告组四段，每段能加多少个指标，文档没有列全。 |

| 参数（文档原名） | 大白话 | 默认 / 范围 | 出处 |
|---|---|---|---|
| Days to Analyze | 分析多少天的历史数据 | 默认 30，最多 365（只有出价规则能到 365） | how-do-algorithms-analyze-data |
| Days of Recent Data to Ignore | 跳过最近几天（数据还没回传完） | 默认 2 | what-is-the-bidding-rule |
| Trigger: Keyword | 关键词层面的条件，如点击、订单、ACOS | 具体指标清单：文档未说明 | November 05 2026 发布说明 |
| Trigger: Campaign | 广告活动层面的条件 | 具体指标清单：文档未说明 | November 05 2026 发布说明 |
| Trigger: Ad Group | 广告组层面的条件 | 具体指标清单：文档未说明 | November 05 2026 发布说明 |
| Portfolio: Budget Usage | 广告组合预算用到多少就不再加价 | 最高 200% | portfolio budget conditions |
| Portfolio: Lookback Period | 预算用量往回看几天（任一天达标即触发） | 默认 7，最多 180 | portfolio budget conditions |
| Organic Rank | 自然排名条件 | 仅 SP 精准词，且要先加进排名追踪 | organic and ads ranking |
| Sponsored Product Rank | 广告排名条件 | 同上 | organic and ads ranking |
| Action 动作类型 | 按目标 ACOS 算价 / 固定金额 / 百分比 / 暂停 / 启用 | 五选一 | what-is-the-bidding-rule |
| Target ACOS | 目标 ACOS，用来算“最优出价” | 每条规则可不同 | how-to-calculate-optimum-bid |
| Fixed Amount / Percentage | 每次加减多少钱或多少百分比；设 $0 就是“保护好词不动” | 自填 | exclude good keywords |
| Min Bid ($) | 出价下限 | 最低 $0.02，步长 $0.01 | March 2026 发布说明 |
| Max Bid ($) | 出价上限 | 自填 | normalize bids |
| 每次最大加价 % | 单次执行最多涨多少 | 字段原名文档未写明 | what-is-the-bidding-rule |
| 每次最大降价 % | 单次执行最多降多少（例子用 2%） | 字段原名文档未写明 | what-is-the-bidding-rule |
| Optimize bid using Keyword Revenue Per Click | 勾上用“每次点击收入×目标 ACOS”算价；不勾用“售价×目标 ACOS×转化率” | 开/关 | how-to-calculate-optimum-bid |
| Normalize: Dynamic Up & Down | 扣掉亚马逊动态竞价会自动抬高的部分 | 开/关，仅目标 ACOS 模式、仅 SP | normalize bids |
| Normalize: Placement modifier | 扣掉位置加价（TOS/PP/ROS 选一）的部分 | 开/关 + 选位置 | normalize bids |
| Observation period | 帮助中心例子写“= 2 Days”，含义文档未说明 | 文档未说明 | Part 4 帮助文章 |
| Suggested bid strategy | 只出现一次名字 | 文档未说明 | what-is-the-bidding-rule |

### Dayparting 分时规则

| 项目 | 说明 |
|---|---|
| 做什么 | 按星期几和小时开关广告组，或按小时加减出价 |
| 支持的广告 | SP、SB、SD 全部支持 |
| 叠加方式 | 互斥（一个广告组只能挂一条） |
| 执行时间 | 每小时一次，按站点时区 |
| 备注 | 参数种类只有 2 个，但每周 7×24=168 个格子各有一组值。 |

| 参数（文档原名） | 大白话 | 默认 / 范围 | 出处 |
|---|---|---|---|
| Ad Scheduling（每格） | 该小时广告组开或关 | 默认全开 | using dayparting for ad scheduling |
| Hourly Bid Adjustment %（每格） | 该小时出价在基础出价上加减 | -99% 到 +900%，默认 0% | getting started with hourly bid adjustments |

### Import 收割规则

| 项目 | 说明 |
|---|---|
| 做什么 | 把表现好的搜索词加成新关键词或商品投放 |
| 支持的广告 | SP 手动关键词和商品投放、SB 视频、SD；SP 自动活动不能当目的地 |
| 叠加方式 | 可叠加 |
| 执行时间 | 每天一次 |
| 备注 | 只往已有的广告组里加词，不会新建活动或广告组。 |

| 参数（文档原名） | 大白话 | 默认 / 范围 | 出处 |
|---|---|---|---|
| Search Term Source | 从哪里汇总搜索词 | 7 选 1：Sponsored Products / Sponsored Brands / Auto Campaign / Campaign / Ad Group / Custom Group / Specific Ad Groups | understanding search term sources |
| Specific Ad Groups | 手动挑来源广告组 | 最多 30 个 SP + 30 个 SB | specific ad groups |
| Criteria Profile | 什么样的搜索词算合格 | 如订单 ≥5 且转化率 ≥15% | presets: Sales |
| Days to Analyze | 分析天数 | 默认 60，最多 180 | what-is-the-import-rule |
| Days to Ignore | 跳过最近几天 | 默认 2 | what-is-the-import-rule |
| Match Type | 新词的匹配方式 | Exact / Phrase / Broad（或商品投放） | what-is-the-import-rule |
| Initial bid | 新词起始出价 = 搜索词平均 CPC 的百分之几 | 字段原名文档未写明 | what-is-the-import-rule |
| Do not execute if the destination ad group exceeds the keyword limit | 广告组词数超限就不再加 | 开/关 | limit keyword imports |
| Max keywords | 单个广告组词数上限（启用和暂停的都算） | 1–1,000 | limit keyword imports |

### Negative 否定搜索词规则

| 项目 | 说明 |
|---|---|
| 做什么 | 把表现差的搜索词加成否定词或否定商品 |
| 支持的广告 | SP 自动、SP 手动关键词和商品投放、SB 视频关键词 |
| 叠加方式 | 可叠加 |
| 执行时间 | 每天一次 |
| 备注 | 官方建议别挂在只有精准词的广告组上，精准词差就用出价规则降价。 |

| 参数（文档原名） | 大白话 | 默认 / 范围 | 出处 |
|---|---|---|---|
| Search Terms Source | 从哪里汇总 | ASIN Search Terms / Campaign / Ad Group / Custom Group / Specific Ad Groups | what-is-the-negative-rule |
| Criteria Profile | 什么样的搜索词要否定 | 预设：点击 ≥20 且 0 单；商品投放点击 ≥5 且 0 单 | presets: Discovery Auto |
| Days to Analyze | 分析天数 | 默认 60，最多 180 | what-is-the-negative-rule |
| Days to Ignore | 跳过最近几天 | 默认 2 | what-is-the-negative-rule |
| Negative type / Match Type | 否定方式 | Negative Exact / Negative Phrase；商品为否定商品 | Part 4 帮助文章 |
| Scope | 否定对象范围 | 例子为 Keyword and ASIN，其余选项文档未说明 | Part 4 帮助文章 |
| Apply negatives at | 否定加在哪一层 | Ad Group 或 Campaign | Part 4 帮助文章 |
| Blacklist | 同时写进黑名单，以后所有挂了黑名单规则的广告都屏蔽 | 开/关 | Part 4 帮助文章 |

### Negative Word 否定单词规则

| 项目 | 说明 |
|---|---|
| 做什么 | 把搜索词拆成单个词，汇总后把差词加成词组否定 |
| 支持的广告 | SP 自动、SP 手动关键词、SB 视频关键词 |
| 叠加方式 | 可叠加 |
| 执行时间 | 每天一次 |
| 备注 | 否定单数会连带复数。标题里出现的词不会被否。 |

| 参数（文档原名） | 大白话 | 默认 / 范围 | 出处 |
|---|---|---|---|
| Criteria Profile | 单词合并后的条件 | 预设：点击 ≥25 且 0 单 | what-is-the-negative-word-rule |
| Days to Analyze | 分析天数 | 默认 60，最多 180 | what-is-the-negative-word-rule |
| Days to Ignore | 跳过最近几天 | 默认 2 | what-is-the-negative-word-rule |

### Blacklist / Whitelist 黑白名单

| 项目 | 说明 |
|---|---|
| 做什么 | 按产品维护四张词表：要屏蔽的和受保护的 |
| 支持的广告 | SP 自动、SP 手动关键词和商品投放、SB 视频关键词 |
| 叠加方式 | 互斥 |
| 执行时间 | 每天一次 |
| 备注 | 精准白名单只抵消精准黑名单，词组白名单只抵消词组黑名单。 |

| 参数（文档原名） | 大白话 | 默认 / 范围 | 出处 |
|---|---|---|---|
| Negative Exact List | 写进去就加成精准否定 | 按产品 | what-is-the-blacklist-whitelist-rule |
| Negative Phrase List | 写进去就加成词组否定 | 按产品 | what-is-the-blacklist-whitelist-rule |
| Exact Whitelist | 防止被否定、否定单词、黑名单规则做成精准否定 | 按产品 | what-is-the-blacklist-whitelist-rule |
| Phrase Whitelist | 防止被做成词组否定（不保护包含它的长词） | 按产品 | what-is-the-blacklist-whitelist-rule |

### Revive 唤醒规则

| 项目 | 说明 |
|---|---|
| 做什么 | 给以前卖得好、现在没流量的词慢慢加价 |
| 支持的广告 | 只支持 SP 手动关键词 |
| 叠加方式 | 可叠加 |
| 执行时间 | 每天一次 |
| 备注 | 新接入账户只有 60 天历史，官方说可能不够用；很多卖家改用出价规则“慢慢加价”。 |

| 参数（文档原名） | 大白话 | 默认 / 范围 | 出处 |
|---|---|---|---|
| Days to Analyze | 分析天数 | 默认 30，最多 180 | what-is-the-revive-rule |
| Days to Ignore | 跳过最近几天 | 默认 2 | what-is-the-revive-rule |
| “活跃”判定条件 | 多少曝光算还活着 | 默认 30 天内曝光 ≥1；字段原名文档未写明 | what-is-the-revive-rule |
| “可唤醒”判定条件 | 历史表现多好才值得唤醒 | 文档未说明 | what-is-the-revive-rule |
| 每次加价幅度 | 例子用 2% | 字段原名文档未写明 | what-is-the-revive-rule |
| Target ACOS | 加到满足目标 ACOS 或词重新活跃为止 | 文档未说明默认值 | what-is-the-revive-rule |
| 出价上限 | “规则里定义的上限” | 字段原名文档未写明 | overview-of-each-algorithm |

### Default Bid 默认出价规则

| 项目 | 说明 |
|---|---|
| 做什么 | 调自动广告组的默认出价 |
| 支持的广告 | 只支持 SP 自动活动，主要给没有四个投放组的老自动活动 |
| 叠加方式 | 互斥 |
| 执行时间 | 每天一次 |
| 备注 | 有紧密/宽泛/同类/关联四组的自动活动，官方建议用出价规则。 |

| 参数（文档原名） | 大白话 | 默认 / 范围 | 出处 |
|---|---|---|---|
| Target ACOS | 目标 ACOS | 文档未说明默认值 | what-is-default-bid-rule |
| Criteria | 触发条件 | 文档未说明 | overview-of-each-algorithm |

### Status 启停规则

| 项目 | 说明 |
|---|---|
| 做什么 | 按表现暂停或启用广告活动和广告组 |
| 支持的广告 | SP 自动、SP 手动关键词和商品投放 |
| 叠加方式 | 可叠加 |
| 执行时间 | 每天一次 |
| 备注 | 会和分时规则打架：分时会把被暂停的广告组重新打开。官方解法是 1 活动 1 广告组，暂停时连活动一起停。 |

| 参数（文档原名） | 大白话 | 默认 / 范围 | 出处 |
|---|---|---|---|
| Criteria | 广告组表现条件，如 ACOS、花费、订单 | 预设：ACOS ≥50% 且花费 ≥$20 就停 | presets: Bulk Auto |
| Campaign status | 活动：暂停 / 启用 / 不变 | 三选一 | why are the status rules not working |
| Ad Group status | 广告组：暂停 / 启用 / 不变 | 三选一 | why are the status rules not working |
| 分析窗口 | 用全局设置 | 规则级是否可单独设：文档未说明 | how-do-algorithms-analyze-data |

### Daily Budget 每日预算规则

| 项目 | 说明 |
|---|---|
| 做什么 | 按表现或星期几调活动日预算 |
| 支持的广告 | SP 自动、SP 手动关键词和商品投放（活动层，不能放进策略目标） |
| 叠加方式 | 互斥 |
| 执行时间 | 站点时区午夜一次 |
| 备注 | 不设基准预算时，每天的百分比会在前一天的结果上连乘。 |

| 参数（文档原名） | 大白话 | 默认 / 范围 | 出处 |
|---|---|---|---|
| Daily Budget Baseline $ | 计算起点（可选） | 可空 | what-is-the-daily-budget-rule |
| Min Budget $ | 预算下限 | 自填 | what-is-the-daily-budget-rule |
| Max Budget $ | 预算上限，官方建议一定要设 | 自填 | what-is-the-daily-budget-rule |
| Days of data to analyze | 分析天数（可选） | 最多 90 | what-is-the-daily-budget-rule |
| Days of recent data to ignore | 跳过最近几天（可选） | 自填 | what-is-the-daily-budget-rule |
| Days to observe change | 改完后几天内不再改 | 自填 | what-is-the-daily-budget-rule |
| Adjustment %（周一到周日） | 按星期几加减（可选，7 个值） | 例：周日 +50%、周二 -15% | what-is-the-daily-budget-rule |
| Criteria Profile + 调整 % | 满足一个条件就乘一次，多个条件连乘 | 例：+30% 再 -5% | what-is-the-daily-budget-rule |

### Placement 位置规则

| 项目 | 说明 |
|---|---|
| 做什么 | 调搜索顶部（TOS）、搜索其余位置（ROS）、商品页（PP）的位置加价 |
| 支持的广告 | SP 自动、SP 手动关键词和商品投放（活动层，不能放进策略目标） |
| 叠加方式 | 可叠加；同一位置每轮只执行优先级最高的一条 |
| 执行时间 | 默认每天，可在全局设置改频率 |
| 备注 | 官方建议留一条“兜底”规则：活动不出单时把 TOS 慢慢降到 0%。 |

| 参数（文档原名） | 大白话 | 默认 / 范围 | 出处 |
|---|---|---|---|
| Placement | 管哪个位置 | TOS / ROS / PP | what-is-the-placement-rule |
| Placement % Value | 要把位置加价推到的目标值 | 如 200% | what-is-the-placement-rule |
| Increment Value | 每次最多加多少 | 如 30% | what-is-the-placement-rule |
| Decrement Value | 每次最多减多少 | 如 50% | what-is-the-placement-rule |
| Criteria | 活动或该位置的订单、转化率、ACOS 等 | 例：订单 ≥30、转化 ≥15%、ACOS ≤80% | what-is-the-placement-rule |
| Organic Rank 条件 | 按自然排名调位置 | 仅 1 活动 1 广告组 1 ASIN 1 精准词 | March 26 2026 发布说明 |
| Days to Analyze | 分析天数（规则级可覆盖全局） | 最多 180 | what-is-the-placement-rule |
| Days to Ignore | 跳过最近几天 | 自填 | what-is-the-placement-rule |
| Days to Observe | 最近改过就先不动，观察几天 | 自填 | multiple placement rules |
| Observe 范围 | 只看本位置的改动，或看任何位置的任何改动 | This Rule's Placement Only / Any Placement, Any Action | multiple placement rules |
| Adjust Keyword Bid % | 同时调关键词出价；每活动每轮只执行一条 | 自填 | multiple placement rules |
| Execution frequency | 执行频率（全局设置） | 默认每天 | what-is-the-placement-rule |

## 建议起步配置（未应用到账户）

> ⚠️ **这是建议，没有改你的账户。** 按你的情况写的起步值：戒指气球装饰，138 个 SP 活动，每月约 $3,100，目标 ACOS 30%，平均 CPC 约 $1.26，点击到订单约 16%。“名称未核实”表示这个设置文档里有说明，但没写出界面上的字段原名。

> ⚠️ **注意：** 以下建议值按 30% 目标 ACOS 计算，当前策略目标是 45%–50% ACOS，使用前需要重新计算。

| 项目 | 说明 |
|---|---|
| 平均几次点击出一单 | 约 6 次（1 ÷ 16%），每单广告花费约 $7.9 |
| 要做到 30% ACOS | 客单价需约 $26 以上；你的实际客单价我手上没有 |
| 流量密度 | 每天约 $103，平均每个活动不到 $1，所以分析窗口要拉长到 60–90 天 |
| 位置表现 | TOS 约 33%，ROS 约 61%，PP 约 82% |

### 出价：开，建两条，这是主力

| 参数 | 建议值 | 理由 | 核对 |
|---|---|---|---|
| 规则 A：动作 | Pause（暂停关键词） | 先把只花钱不出单的词停掉 | 文档有 |
| 规则 A：条件 | 点击 ≥20 且订单 = 0 | 按 16% 转化率，一个正常词点 20 次还 0 单的概率只有约 3% | 文档有 |
| 规则 A：Days to Analyze / Ignore | 90 / 2 | 单个词流量少，要更长时间才攒够 20 次点击 | 文档有 |
| 规则 A：优先级 | 高于规则 B | 先判断要不要停，再判断要不要调价 | 文档有 |
| 规则 B：动作 | Target ACOS | 按你的目标自动算每个词该出多少 | 文档有 |
| 规则 B：Target ACOS | 30% | 你定的目标 | 文档有 |
| 规则 B：条件 | 点击 ≥10 | 数据太少的词不动，避免一两次点击就乱调 | 文档有 |
| 规则 B：Days to Analyze / Ignore | 60 / 2 | 比默认 30 天长，适合低流量；2 天留给数据回传 | 文档有 |
| 规则 B：Min / Max Bid | $0.30 / $2.50 | 上限约是现在平均 CPC $1.26 的 2 倍，防止失控 | 文档有 |
| 规则 B：每次最大加价 / 降价 | 10% / 10% | 一次改一点，几天走到位，不会一下子把词降没 | 名称未核实 |
| 规则 B：Revenue Per Click | 开 | 用词的真实收入算价，变体售价不同时更准 | 文档有 |
| 规则 B：Normalize 位置加价 | 开，选 TOS（活动有 TOS 加价时） | TOS 加价会让实际出价变高，算价时要扣掉 | 文档有 |

### 位置：开，你的数据最需要这个

| 参数 | 建议值 | 理由 | 核对 |
|---|---|---|---|
| TOS 规则：条件 | TOS ACOS ≤35% 且订单 ≥3 | 你的 TOS ACOS 约 33%，已接近目标，值得多拿 | 文档有 |
| TOS 规则：Increment / Placement % Value | +10% / 50% | 每次只加 10%，最多到 50%，慢慢试 | 文档有 |
| PP 规则：条件 | PP ACOS ≥60% 且点击 ≥20 | 你的商品页 ACOS 约 82%，是最大的漏洞 | 文档有 |
| PP 规则：Decrement / Placement % Value | -10% / 0% | 一直降到不再额外加价 | 文档有 |
| ROS 规则 | 同 PP 规则 | ROS 约 61%，也明显超标 | 文档有 |
| Days to Analyze / Ignore | 60 / 2 | 单个活动每个位置的数据很少 | 文档有 |
| Days to Observe + 范围 | 7 天，This Rule's Placement Only | 改完一个位置等一周看效果，三个位置各管各的 | 文档有 |
| 注意 | PP、ROS 如果已经是 0%，规则就降不动了 | 这时要靠出价规则降基础出价，再配合 TOS 加价 | 我的说明 |

### 否定：开，只挂在自动、广泛、词组和类目广告组上

| 参数 | 建议值 | 理由 | 核对 |
|---|---|---|---|
| 规则 A：Search Terms Source | ASIN Search Terms | 同一 ASIN 的所有广告合起来算，数据攒得快 | 文档有 |
| 规则 A：条件 | 点击 ≥20 且订单 = 0 | 和出价规则同一个标准，约花 $25 | 文档有 |
| 规则 A：Negative type | Negative Exact | 精准否定只挡这一个搜索词，误伤最小 | 文档有 |
| 规则 A：Days to Analyze / Ignore | 90 / 2 | 低流量要更长窗口 | 文档有 |
| 规则 A：Apply negatives at | 1 活动 1 广告组就选 Ad Group，否则选 Campaign | 官方帮助文章的做法 | 文档有 |
| 规则 B：商品投放 | 点击 ≥10 且订单 = 0 | 官方预设是 5 次，你流量少，放宽一点；10 次 0 单的概率约 17% | 文档有 |
| 高 ACOS 搜索词 | 先不开 | 否定有单的词会丢销量，等出价规则跑一段再看 | 我的建议 |

### 黑白名单：开，而且要在否定单词之前设好

| 参数 | 建议值 | 理由 | 核对 |
|---|---|---|---|
| Phrase Whitelist | 你的核心词，如 ring balloon、diamond ring、engagement、proposal | 防止否定单词规则把核心词误否；词表请按你的实际核心词改 | 文档有 |
| Exact Whitelist | 出单最多的那些搜索词 | 保护最赚钱的词 | 文档有 |
| Negative Phrase List | 从搜索词报告里挑明显不相关的词 | 以后新广告也自动屏蔽 | 文档有 |

### 否定单词：白名单设好后再开

| 参数 | 建议值 | 理由 | 核对 |
|---|---|---|---|
| 条件 | 点击 ≥30 且订单 = 0 | 官方预设 25；否单数会连带复数，误伤大，所以再保守一点 | 文档有 |
| Days to Analyze / Ignore | 90 / 2 | 低流量 | 文档有 |
| 挂在哪 | 只挂自动、广泛、词组 | 官方提醒不要挂在精准词广告组 | 文档有 |

### 收割：开，前提是已经有接收新词的精准广告组

| 参数 | 建议值 | 理由 | 核对 |
|---|---|---|---|
| Search Term Source | Sponsored Products | 同一 ASIN 的所有 SP 广告一起找好词 | 文档有 |
| 条件 | 订单 ≥2 且 ACOS ≤35% | 官方入门预设是 ≥2 单；加 ACOS 门槛避免收进贵词 | 文档有 |
| Match Type | Exact | 先只建精准词，好控价 | 文档有 |
| Initial bid | 平均 CPC 的 100% | 按它过去实际花的钱起步 | 名称未核实 |
| Days to Analyze / Ignore | 90 / 2 | 低流量 | 文档有 |
| Max keywords | 30 | 不让一个广告组塞太多词、分不到预算 | 文档有 |

### 预算：只当安全阀用

| 参数 | 建议值 | 理由 | 核对 |
|---|---|---|---|
| Daily Budget Baseline $ | 填当前预算 | 不填的话每天的百分比会连乘，越调越偏 | 文档有 |
| Max Budget $ | 当前预算的 2 倍 | 官方建议必须设，防止失控 | 文档有 |
| 条件 1 + 调整 | ACOS ≤25% 且订单 ≥5 → +20% | 好活动多给钱 | 文档有 |
| 条件 2 + 调整 | ACOS ≥50% 且花费 ≥$30 → -20% | 差活动少给钱 | 文档有 |
| Days of data to analyze / ignore | 30 / 2 | 预算看整个活动，30 天够用 | 文档有 |
| Days to observe change | 7 | 改完看一周再动 | 文档有 |
| 星期几调整 | 不设 | 还没有按星期的数据 | 我的建议 |

### 分时：先不开（全天开、0% 调整）

| 参数 | 建议值 | 理由 | 核对 |
|---|---|---|---|
| 全部 168 格 | 开，0% | 你流量少，砍掉时段容易少出单 | 文档有 |
| 下一步 | 先下载亚马逊小时报表上传看 4 周 | Dayparting Trend 只有销售没有点击花费，不够判断 | 文档有 |
| 以后若要动 | 弱时段 -20%，不要直接关 | 降价比关停温和 | 文档有 |

### 启停：先不开

| 参数 | 建议值 | 理由 | 核对 |
|---|---|---|---|
| 理由 | 关键词级的出价规则已经能停坏词 | 整组暂停太粗，而且会和分时规则打架 | 我的建议 |
| 若以后要开 | 只用于 1 活动 1 广告组；花费 ≥$40 且 0 单就停，ACOS ≤30% 再开 | 约等于 5 单的花费还 0 单；官方建议配一条恢复规则 | 文档有 |

### 唤醒：先不开

| 参数 | 建议值 | 理由 | 核对 |
|---|---|---|---|
| 理由 | 新接入只有 60 天历史，官方说可能不够 | 需要时用出价规则慢慢加价更简单 | 文档有 |

### 默认出价：不开

| 参数 | 建议值 | 理由 | 核对 |
|---|---|---|---|
| 理由 | 只给没有四个投放组的老自动活动用 | 有四个投放组的自动活动交给出价规则 | 文档有 |

## 小账户真正要管的参数

*我的建议，不是官方说法。*

1. 目标 ACOS：全账户先只用一个 30%。
2. 分析窗口：出价 60 天，否定和收割 90 天，跳过最近 2 天。
3. “多少次点击没单算差”：统一用 20 次，商品投放 10 次。
4. 出价上下限：$0.30 到 $2.50，每次最多改 10%。
5. 位置：TOS 慢慢加，PP 和 ROS 降到 0%。
6. 白名单：先写核心词，再开任何否定单词规则。
7. 其余几十个参数先保持默认或不开，跑满 4 周看变更日志再调。

## 文档里的矛盾和空白

| 问题 | 具体情况 |
|---|---|
| 优先级数字谁大谁先 | 帮助文章说暂停规则设 100、降价规则设 99，“保证暂停先执行”；位置规则文章又说 Priority 1 比 Priority 2 优先。试用时要先实测 |
| 条件指标的完整清单 | 文档提到订单、花费、点击、ACOS、转化率、CTR、曝光、销售额、排名、组合预算用量，但没给完整列表和每个层级可用哪些 |
| 分析窗口能否在规则里单独设 | 文档说出价和位置规则可以；否定规则的帮助文章也在规则里设了 90 天；启停和默认出价没说 |
| 预算规则窗口上限 | 通用说明写多数算法最多 180 天，预算规则文章写最多 90 天 |
| Observation period、Suggested bid strategy | 只出现名字，没有说明 |
| MCP 能改哪些规则字段 | 公开技能包不带字段定义，要连上账户才能看到 |

## 来源

- [Algorithms 目录（11 个子目录）](https://docs.scaleinsights.com/docs/algorithms)，SI 文档
- [Overview of Each Automation Algorithm](https://docs.scaleinsights.com/docs/overview-of-each-algorithm)，SI 文档
- [Available algorithms for ads](https://docs.scaleinsights.com/docs/available-algorithms-for-ads)，SI 文档
- [Stackable and Mutually Exclusive Rules](https://docs.scaleinsights.com/docs/stackable-and-mutually-exclusive-rules)，SI 文档
- [How Do Algorithms Analyze Data?](https://docs.scaleinsights.com/docs/how-do-algorithms-analyze-data)，SI 文档
- [What is the Bidding Rule?](https://docs.scaleinsights.com/docs/what-is-the-bidding-rule)，SI 文档
- [How to Calculate the Optimum Bid](https://docs.scaleinsights.com/docs/how-to-calculate-optimum-bid)，SI 文档
- [Normalize Bids (Placement / Dynamic)](https://docs.scaleinsights.com/docs/bidding-algorithm-normalize-bids-based-on-placement-and-dynamic-updown)，SI 文档
- [Portfolio budget conditions](https://docs.scaleinsights.com/docs/how-to-use-portfolio-budget-conditions-in-bidding-rules)，SI 文档
- [Organic and Ads Ranking conditions](https://docs.scaleinsights.com/docs/bidding-rule-conditions-based-on-organic-and-ads-ranking)，SI 文档
- [What is the Dayparting rule?](https://docs.scaleinsights.com/docs/what-is-the-dayparting-rule)，SI 文档
- [Getting Started with Hourly Bid Adjustments](https://docs.scaleinsights.com/docs/getting-started-with-hourly-bid-adjustments)，SI 文档
- [What is the Import Rule?](https://docs.scaleinsights.com/docs/what-is-the-import-rule)，SI 文档
- [Limit Keyword Imports per Ad Group](https://docs.scaleinsights.com/docs/how-to-limit-keyword-imports-per-ad-group)，SI 文档
- [Understanding Search Term Sources](https://help.scaleinsights.com/en/knowledge-base/understanding-search-term-sources-in-import-rules)，SI 帮助中心
- [What is the Negative Rule?](https://docs.scaleinsights.com/docs/what-is-the-negative-rule)，SI 文档
- [Considerations with Negative Rule](https://docs.scaleinsights.com/docs/considerations-with-negative-rule)，SI 文档
- [Part 4: Long-Term Bleeder Management](https://help.scaleinsights.com/en/knowledge-base/part-4-long-term-bleeder-management-with-automation-rules)，SI 帮助中心
- [What is the Negative Word Rule?](https://docs.scaleinsights.com/docs/what-is-the-negative-word-rule)，SI 文档
- [What is the Blacklist/Whitelist Rule?](https://docs.scaleinsights.com/docs/what-is-the-blacklist-whitelist-rule)，SI 文档
- [What is the Revive Rule?](https://docs.scaleinsights.com/docs/what-is-the-revive-rule)，SI 文档
- [What is the Default Bid Rule?](https://docs.scaleinsights.com/docs/what-is-default-bid-rule)，SI 文档
- [What is the Status Rule?](https://docs.scaleinsights.com/docs/what-is-the-status-rule)，SI 文档
- [Why are the Status Rules not working?](https://docs.scaleinsights.com/docs/why-are-the-status-rules-not-working)，SI 文档
- [What is the Daily Budget Rule?](https://docs.scaleinsights.com/docs/what-is-the-daily-budget-rule)，SI 文档
- [What is the Placement Rule?](https://docs.scaleinsights.com/docs/what-is-the-placement-rule)，SI 文档
- [How Multiple Placement Rules Work Together](https://docs.scaleinsights.com/docs/how-multiple-placement-rules-work-together)，SI 文档
- [Criteria Profile](https://docs.scaleinsights.com/docs/how-to-create-and-use-a-criteria-profile)，SI 文档
- [Bulk Import for Rules and Criteria](https://docs.scaleinsights.com/docs/how-to-use-bulk-import-for-automation-rules-and-criteria-profiles)，SI 文档
- [Presets for Sponsored Products](https://docs.scaleinsights.com/docs/presets-for-sponsored-products)，SI 文档
- [March 26, 2026 Release Notes](https://docs.scaleinsights.com/docs/march-26-2026-release-notes)，SI 文档
- [Why I Decided to Build SI MCP（“100 多个参数”出处）](https://blog.scaleinsights.com/why-i-decided-to-build-si-mcp)，SI 博客

---

原始页面：https://scale-insights-11-yyxt.pages.grok.bot/
