# B0H6J55GBP（ring banner）RB_ 新结构 SI 规则汇总（2026-11-17 ~ 11-18）

> 店铺：Trendy Treasures Store（美国站）。售价 $13.99；SI 回本 ACOS 51.9%，广告前单位利润 $7.26。
> 目标：CPA ≤ $6（约 42% ACOS）。老盈利结构保持不动，新结构平行运行。
> 规则全部经 Hunter 预览并明确批准后，用 Scale Insights MCP 规则包写入。规则包写入后不能撤销，写入前都做了备份。

## 全局原则（13 个 RB_ 广告组通用）
- 7×24 投放：去掉 SI 默认的“Optimum Hours”分时（Dayparting:10100）。
- 不用 SI 自动收词（Import），收词改为每天报告提示、人工导入。
- 只按 adGroupId 手动分配目标（目标的自动匹配条件设为组名含 `manual-assign-only-rbn`，防止被自动挂到别的组）。
- 新建活动时，SI 会自动挂上默认目标（Ranking Exact/Phrase/Broad，或 Product Targeting 12000，带分时、收词和 25%/60% ACOS 竞价），每次都要检查并替换。
- 出价上限 $1.20（关键词和 ASIN 一样）；“关键词最低展示规则”另有自己的上限 $1.15。
- 否定词（engagement ring、wedding 等）由 Hunter 在 SI 或 Amazon 后台手动添加：MCP 只能把已经产生过花费的搜索词加成精确否定。

## 规则定义
| 定义 | 内容 |
|---|---|
| Bidding:11131 | 暂停：≥8 次点击且 0 单，暂停该投放；优先级 70，冷却 7 天 |
| Bidding:11134 | 暂停：≥10 次点击且 0 单，暂停该投放；优先级 70，冷却 7 天 |
| Bidding:11132 | 关键词竞价：目标 ACOS 42%，每次最多 +15% / −20%，出价 $0.30–1.20；≥1 单才触发；冷却 7 天，手动改价后 7 天不动 |
| Bidding:11133 | ASIN 竞价：目标 ACOS 42%，每次最多 +10% / −20%，出价 $0.30–1.20；≥1 单才触发；冷却 7 天，手动改价后 7 天不动 |
| Bidding:11135 关键词最低展示规则 | 活动前一个完整美国日花费 ≤ $9.99 且有曝光时，给没出单的关键词 +15%，出价 $0.30–1.15；优先级 40；每天可改一次；手动改价后 3 天不动 |
| Negative:20001 | 否定：≥10 次点击且 0 单，在本组加精确否定，不进黑名单 |

优先级说明：同一个投放每次运行只被一条规则处理，按优先级从高到低：暂停（70）> 42% ACOS 竞价（50）> 最低展示加价（40）。

## 目标和分配
| 目标 | 包含的规则 | 广告组 |
|---|---|---|
| 300001 AI - RBN Exact CPA6 | 11131 + 11132 + 11135（无否定规则） | high_Engagement 精准、mid_Engagement、low_Engagement、Engagement_sence（按精准处理） |
| 300002 AI - RBN Phrase-Broad CPA6 (pause) | 11131 + 11132 + 11135 + 2 条否定（10 次点击 0 单精确否定、低 CTR 否定 10100） | RB_SP_KW_PH_Ring |
| 300003 AI - RBN Phrase-Broad CPA6 (no pause) | 11132 + 11135 + 2 条否定（同上） | high_Engagement 词组组、广泛组（单关键词组，不暂停） |
| 300004 AI - RBN Product targets CPA6 | 11131 + 11133 | 旧 RB_T1 / T2 / T3 ASIN 组 |
| 300005 AI - RBN Product targets CPA6 P10N10 | 11134 + 11133 + Negative:20001 | 新 T1 发现、T2 拓展、T3 拓展 |

## 收词（只提示、不自动导入）
- SI 的 Import 规则没有“只建议”模式，一旦启用就会直接加词，所以不用它。
- 每天北京时间 17:00 的日报里出收词提示：
  - ≥2 单且 CPA < $6（约 ACOS ≤ 42%）：建议导入；
  - 1 单且 ≥3 次点击：观察。

## SI MCP 限制（这两天确认的）
- 条件里没有“花费占预算百分比”，所以“花费不到预算 50%”只能写成活动层固定金额（`CampaignCriteriaProfile` 里的 `MaxSpent`）。预算改了，这个金额要手动同步改。
- 回看前一个完整美国日：`DaysToAnalyse` 1 + `DaysToIgnore` 1（`DaysToIgnore` 0 会去看当天还不完整的数据）。
- MCP 不能开关单个广告组，也不能调整活动所属的 portfolio，只能在 Amazon 后台或 SI 网页上做。
- `get_campaign_settings` 和广告组开关状态读的是 SI 保存的数据，可能比 Amazon 后台晚一些。
- 规则包写入流程：`build_rule_package` → 第一次调用 `apply_change_set` 返回确认文字 → 用户确认 → 用新的 ChangeSetId 再调一次 `apply_change_set`。不能撤销，退回要靠反向规则包。

## 备份
- `/workspace/si-plans/backup-20261117/`、`backup-20261117-1947/`、`backup-20261118-0947/`，每份都含 10 类定义和全部广告组的分配，附 sha256 清单和 RESTORE_PLAN。
