# RCC6 深度优化竞价规则

记录 2026-11-23 写入并回读验证的 RCC6A NEW 竞价规则包，供长期查阅。不是默认模板，也不授权改动旧结构或其他商品。

## 目标

- 所有 targeting 最终 CPA ≤ $3.50。
- 接受数据收集期，不要求当天实时止损。
- 不能没有展示：出价下限 $0.30 只限制地板，**不保证**仍有曝光；设计上避免把已经低于 $0.30 的词抬高。

## 适用范围

- Scale Insights 账户「zhou」，美国站；商品 RCC6A（ASIN `B0DHC7CFKS`）。
- 仅这 12 个 RCC6A NEW 活动、27 个广告组（过线 v2 + high cpa）：
  - 过线 v2：`RCC6A NEW KW 超高流量 20261122`、`RCC6A NEW KW 高流量 20261122`、`RCC6A NEW KW 中高流量 20261122`、`RCC6A NEW KW 中流量 20261122`、`RCC6A NEW KW 低0无量 20261122`、`RCC6A NEW ASIN 20261122`
  - high cpa：`RCC6A NEW high cpa 超高`、`RCC6A NEW high cpa 高`、`RCC6A NEW high cpa 中高`、`RCC6A NEW high cpa 中`、`RCC6A NEW high cpa 低0无量`、`RCC6A NEW high cpa ASIN`
- 用明确的 SI 广告组 ID 白名单分配，不按名称自动扩圈。
- **不挂到旧盈利结构**：已验证的 48 条盈利投放及其父活动、广告组保持不动（预算、版位、否定、规则均不改）。

## 规则 A：零单花费暂停

只暂停单个 keyword / product target，不改出价，不停广告组或活动。

| 项 | 值 |
| --- | --- |
| Criteria | `Criteria:800209`（名称：RCC6A NEW 零单花费7.41暂停） |
| Bidding | `Bidding:11138`（名称：RCC6A NEW 零单花费7.41暂停） |
| 条件（AND，边界含等号） | `MaxOrders=0` 且 `MinSpent=7.41` |
| 动作 | `BidComputation=10`，`NewState=paused` |
| 优先级 | 131（高于当时账户最高 121，未复用 `Bidding:11137`） |
| 窗口覆盖 | `DaysToAnalyse=30`，`DaysToIgnore=2` |
| 冷却 / 手动保护 | `DaysToPauseChange=1`，`DaysToObserveManualChange=7` |
| PMA | 0 |

「花费超过 $7.40」在包含边界、精确到分时，写成 `MinSpent=7.41`；花费正好 $7.40 且 0 单时不触发。

## 规则 B：满 3 单后降价 10%

| 项 | 值 |
| --- | --- |
| Criteria | `Criteria:800210`（名称：RCC6A NEW 3单超CPA3.50(ACOS35%)） |
| Bidding | `Bidding:11139`（名称：RCC6A NEW 3单超CPA3.50降价10%） |
| 条件（AND，边界含等号） | `MinOrders=3` 且 `MinACOS=35` |
| 动作 | `BidComputation=2`，`BidPercentAdjustment=-10` |
| 出价下限 | `MinBid=0.30`（地板，不保证展示） |
| 跳过低价词 | `MinCurrentBid=0.30`（当前出价已低于 0.30 的对象不进入本规则，避免被抬到 0.30） |
| 优先级 | 126 |
| 窗口覆盖 | `DaysToAnalyse=30`，`DaysToIgnore=2` |
| 冷却 / 手动保护 | `DaysToPauseChange=2`（避免每天连降），`DaysToObserveManualChange=7` |
| PMA | 0（这 12 个活动已有搜索其余位置 +50%；若 PMA 按最大版位倍数修正，「降 10%」会被放大） |

SI Criteria 没有 CPA 字段。`MinACOS=35` 是目标 CPA $3.50 ÷ 售价 $9.99 的整数近似（单件、无折扣假设）。多件订单和折扣会让换算失真，不能把它写成已经精确控制了 CPA。

两条规则同一窗口下互斥：0 单走 A，满 3 单且 ACOS≥35% 走 B。成交 1–2 单且 CPA 偏高时，两条都不触发。

## 评估窗口与执行周期

- 两条规则都单独写了回看 30 天、忽略最近 2 天，与共享竞价窗口一致。
- 2026-11-23 09:24（北京时间 Asia/Shanghai）只读账户 `get_automation_setup` summary 的共享 **Bidding settings**：`daysToAnalyse=30`，`daysToIgnore=2`，`runDays=Mon–Sun`（每天运行）。
- 共享调度的具体运行时刻、时区：未确认（该 summary 未返回钟点或时区）。
- 规则只对已启用的活动和广告组执行。忽略最近 2 天是为了归因延迟，不是当日实时止损。

## 关键坑

1. **7.41 是包含边界。** 「超过 7.4」不要写成 7.40，否则 $7.40 整好也会暂停。
2. **CPA 不等于 ACOS。** 只能用 35% 近似 $3.50 / $9.99；多件和折扣会偏离。
3. **MinBid 与 MinCurrentBid 不是一回事。** 只设 MinBid=0.30，可能把已低于 0.30 的词抬高。必须同时设 MinCurrentBid=0.30。下限不保证还有展示。
4. **PMA 必须为 0。** 活动已有 ROS +50% 时，若按最大版位倍数修正，降 10% 可能被放大到约 40% 的基础降幅。
5. **规则包不能 undo。** 不能用 SI 的一键撤销撤回；恢复必须另做获批规则包。
6. **暂停中的活动不执行规则。** 官方已建且广告组开启，不等于规则已在跑。挂规则和开活动是两步，须分别批准。
7. **Bulk 上传成功不等于 SI 已同步。** 必须在 SI 里按精确名称拿到广告组 ID 后才能挂规则，不要把官方 Amazon ID 直接当作 SI 分配 ID。同步时长无法预知。

## 写入记录

- 时间：2026-11-23 08:31（北京时间 Asia/Shanghai）写入；随后只读回读，27/27 个广告组均已挂上 A、B，字段与上表一致；该 ASIN 下其他组未被扩大覆盖。
- 真实 ID：`Criteria:800209` + `Bidding:11138`；`Criteria:800210` + `Bidding:11139`。
- 分配：27 个组的直接 Bidding 均为上述两条，生效 Bidding 计数为 2。
- 备份与回读：`/workspace/rcc6a_si_rules_apply_20261123/backup/`（十类定义与分配、SHA256、RESTORE_PLAN）；回读见同目录 `apply/post_apply_verification.json`。
- 这只证明「规则已挂上」，不等于已经暂停或降过价。实际执行要看之后的调度和变更日志。

## 恢复方式

- 规则变更不能 undo。若要撤回，须另做预览并经用户批准后，用新规则包：先从这 27 个明确广告组 ID 上 Remove `Bidding:11138` 和 `Bidding:11139`（不要误 Clear 后来另加的规则），确认 usedBy 为 0 且范围未扩大，再可选删除仅这四条新定义（`Criteria:800209`、`Criteria:800210`、`Bidding:11138`、`Bidding:11139`）。
- 不要改或删除其他共享定义（包括 `Bidding:11137`）。
- 撤规则只停止以后继续执行，**不会**把此前已被暂停的 target 重新开启，也不会把已经降低的 bid 改回去。若要恢复开关或出价，须对具体对象另做预览并获批。
