# Scale Insights MCP 实战经验（2026-11-17）

作者：amazon ads execute（SI 执行助手）
项目：AutoSell，美国站 Trendy Treasures Store，ring banner ASIN B0H6J55GBP
范围：这份文档只记录**实际调用 SI MCP 验证过**的行为。SI 网页（如 Mass Campaigns、Bulk Rule Editor 手动上传）的能力不等于 MCP 能力。

---

## 1. 连接与配额
- 服务地址 `https://mcp.scaleinsights.com/mcp`，共 58 个工具，账户 "zhou"，`account_ref` 留空即用当前连接账户。
- 配额：每会话 1200 units，每周 15000 units；会话额度约 3 小时滚动重置。一次完整 si-ppc-plan 约 150 units，出规则包+全量备份约 110 units。
- si-* 快捷指令（49 个）：每次先调 `resolve_shortcut` 取实时工作流；首次带 spec 的调用传 `spec_version`。

## 2. 写入的通用流程
1. `propose_*`（出价、预算、否定、关键词状态、活动设置、产品成本）只做预览，返回 change_set_id，什么都不写。
2. 把预览（对象、当前值、拟改值、理由、风险）给用户，**等用户本人明确批准**。
3. `apply_change_set` 不能和 propose 在同一轮调用（服务器拒绝 30 秒内的 change set）。
4. 普通 change set 写入后返回 `undo_token`，可用 `undo_change_set` 尽力回滚（被再次改过的项可能回不去）。
5. change_set_id 很长（1000+ 字符），用文件传参更稳，避免抄错。

## 3. 规则包（rule package）——最容易踩坑的部分
- `build_rule_package` 生成 Bulk Rule Editor ZIP，返回 `applyHere.changeSetId`，**30 分钟有效**，过期要重新出包。
- `apply_change_set` 对规则包是两段式：第一次调用不写入，返回 `Status: confirm`、一段 Confirmation 原文和**新的** ChangeSetId；必须把原文给用户看，用户确认后在**下一轮**用新的 ChangeSetId 再调用一次才真正写入。
- 规则和目标（objective）的改动**没有 undo**。规则定义在账户所有站点共用。
- 写入前必须备份：
  - 全部 10 类定义：Criteria、Bidding、Import、Negative、Negative Word、Objective、Status、Placement、Daily Budget、Dayparting（`get_automation_setup section=definitions`，翻完所有页）。
  - 全部广告组的分配（`section=ad_groups`，翻完所有页）；SB/SD 有规则时一起备份。
  - 校验：文件能解析、条数等于分页总数、目标组当前分配和预期一致。
  - 回滚方案 = 再出一个反向规则包，把组改回原来的 objective。
- 写入后立刻重读 `ad_groups`，核对每个组的 objective 和 effectiveRuleCounts。
- 网页备选：下载 ZIP，在 SI 的 Bulk Rule Editor 上传应用。

## 4. 否定词的限制
- `propose_negative_keywords` **只能否定已经产生点击和花费、但 0 单的搜索词**，只加精确否定（ASIN 搜索词会自动变成否定商品投放）。
- 不支持词组否定，不支持提前手动加一份否定词表。
- 新建活动没有搜索词，传 `search_terms` 也匹配 0 个。这类否定需要在 SI 网页或亚马逊后台手动加。

## 5. 活动同步与读取
- `get_campaign_settings` 读的是 SI 存着的设置，不会从亚马逊刷新；MCP 没有触发同步的工具。
- 在第三方工具（如领星）建的活动要等 SI 下一轮同步才出现；在 SI 网页建的活动立即可见。
- MCP 不能新建活动、广告组、正向关键词或商品投放（这是网页能力，不是 MCP 能力）。

## 6. 在 SI 里新建活动的默认规则（重要）
在 SI 网页新建的活动会**自动套上默认 objective**，账户里其他组即使没有任何规则也会这样：
- Ranking Exact / Ranking Phrase / Ranking Broad：
  - Bidding:10300 Aggressive：目标 ACOS 60%，3 次点击就动，单次最多 ±50%，上限 $3，无冷却。
  - Bidding:10003 Bad Conversions：转化率 1–5% 且花费 ≥ $10 时按 20% ACOS 调，单次最多降 99%。
  - 收词规则（本活动内，≥2 单，起价 110% CPC，无上限）；Phrase/Broad 另有两条否定（其中 20 点击 0 单那条会进 ASIN 黑名单）。
- Product Targeting：Bidding:10100 目标 25% ACOS；Import:10200 从该 ASIN 所有 SP 组（包括旧结构）收 ASIN。
- **Dayparting:10100 "Optimum Hours"**：周日到周三只开 6:00–20:59，周四到周六每天只开 22 点一小时。新活动上线后会被大段时间关掉。
- 结论：新活动建好后先查 `get_automation_setup section=ad_groups`，按自己的目标替换掉默认 objective。

## 7. 规则引擎的几个事实
- Status 规则只能暂停整个广告组或活动；**单个关键词/ASIN 暂停要用出价规则的 "state change → paused"**。
- TargetACOS 只能填整数（CPA $6、售价 $13.99 → 42.9%，只能取 42%）。
- 一个组只能有一个 objective；优先级数字大的规则先跑。
- 自动分配靠 objective 的 Criteria（活动名/广告组名包含、匹配类型）；只想手动分配时，把自动分配优先级设 0，并把名称条件设成一个不存在的字符串。
- 新组没有数据时 `check_ppc_plan` 会显示"作用于 0 个组"，属正常。

## 8. 本次实际落地的方案（RB_ 新结构，2026-11-17 已写入）
- 范围：8 个 RB_ 新活动、10 个广告组，只按 adGroupId 手动分配；旧盈利结构不碰。
- 目标：CPA ≤ $6（42% ACOS）；售价 $13.99；SI 盈亏平衡 ACOS 51.9%，广告前单件利润 $7.26。
- 规则：
  - R1 暂停：点击 ≥ 8 且 0 单 → 暂停关键词/ASIN（优先级 70）；high_Engagement 的词组和广泛组（各只有 1 个词）不受此规则限制。
  - R2 关键词调价：≥1 单后向 42% ACOS 调，单次 +15% / −20%，出价 $0.30–$1.20，冷却 7 天，手动改过的 7 天内不动。
  - R3 ASIN 调价：同上，单次最多 +10%。
  - R4 否定：搜索词点击 ≥ 10 且 0 单 → 本组精确否定，不进黑名单；保留默认低 CTR 否定（曝光 ≥1300、CTR ≤0.08%、0 单）。
  - 不分时（7×24），不收词。
- 阈值依据：平均转化率约 16%；8 次点击 0 单约有 25% 概率误停正常词，这是用户接受的取舍。
- 7 天冷却的理由：订单归因 3–7 天才稳定，新活动数据少，避免误调。
- 写入结果：新建 11 条定义、修改 0、删除 0、10 个分配变更；写入后核对无分时、无收词。
- 待办：写入后 SI 显示 10 个组为关闭，需在亚马逊后台确认已开启；约两周后用 si-tune 复盘。

## 9. 其他
- 产品成本可以通过 `propose_product_cost_changes` 写入（有 undo），成本设置正确后盈亏平衡 ACOS 才可信。本 ASIN：生产 $0.85、头程 $0.15、FBA $3.63、佣金 $2.10。
- 连接器 get-mcp-ads 只支持 Meta/Google/TikTok/Pinterest，不支持亚马逊。
