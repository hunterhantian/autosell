---
name: bulk sheet用法
description: >-
  设计、填写、上传核对或测试亚马逊广告官方 Bulk 表（Bulksheet）时使用：SP 自动、手动关键词、手动 ASIN
  活动的新建已实测通过，含已验证写法、文件识别、导出与上传后核对流程；Update 尚待测试。
---
# bulk sheet用法

状态（2026-11-19 更新）：SP **Create 已实测通过**，覆盖自动、手动关键词和手动 ASIN 三种结构。**Update 尚未实测**。下文分为“已验证”“已观察但原因未定”“待测试”三档，引用时不要把待测试当已验证。

## 安全规则
- Bulk 上传直接改动线上广告。新建、预算、出价、开关、位置溢价、否词或归档前，先列出逐行改动（对象、当前值、拟改值、理由、风险），获得用户对该批次的明确确认。
- 默认只生成本地文件，由用户本人手动上传，不擅自上传。保留现有盈利结构，新结构平行搭建。
- 测试活动必须用明确的 TEST 命名，活动状态 `paused`，日预算 $1，不改现有活动。
- 一份文件只做一件事：Create 与 Update 不混在同一张表；不同批次分开文件。
- 上传后不论页面显示什么，都不要整包重传同一份 Create，否则会重复创建。先逐行核对结果，再决定补哪几行。
- 新建活动默认没有任何 SI 自动化规则。启用前先挂好出价和止损规则，再开活动。

## 三种文件要区分
| 文件 | 怎么识别 | 用途 |
| --- | --- | --- |
| 官方空白模板 | SP 表约 37 列，只有表头，无业绩列 | 新建草稿；用 `Config` 下拉值填写 |
| 上传的原文件 | 37 列、带临时 ID、无 Status 或错误列 | 只是你传上去的东西，不是结果 |
| 后台导出表 | SP 表约 59 列，含 `(Informational only)` 和业绩列，有正式 ID | 对照现有结构、做 `Update` |

- 已观察：Bulk 上传记录页的 Download 链接下载回来的是**原上传文件**，不是逐行结果报告。逐行结果要另找，或用导出表和后台界面核对。
- 判断导出表是否可用，必须打开看 `Sponsored Products Campaigns` 工作表**有没有数据行**；只看列数或工作表名单不够。
- 导出表附带的 Search Term 工作表不能替代结构表：暂停且没流量的活动不会出现在搜索词里；搜索词里看到某活动也只能证明它还在，不能证明结构字段没被改。
- `Config` 页与命名区域是下拉来源，不要改。生成文件时保留模板全部表头、下拉、条件格式、定义名称和其他工作表。
- `Operation` 为空的行会被忽略（导出表默认 `Operation` 为空）。

## 已验证：Create 上传写法（2026-11-19 实测受理）
用官方空白模板 + `Config` 代码值新建，亚马逊整批受理：
- 自动活动 13 行：页面显示 Success，13/13 rows processed，上传约 1 分钟处理完；用户在后台确认活动已建成。
- 手动关键词活动 10 行 + 手动 ASIN 活动 9 行，共 19 行同一文件上传：后台可见两个广告组、3 条关键词和 2 个 ASIN 目标，活动为暂停。

已证实可被受理的写法：
| 字段 | 写法 |
| --- | --- |
| Targeting Type | `AUTO`、`MANUAL` |
| Bidding Strategy | `Dynamic bids - down only` |
| Placement | `placementTop`、`placementProductPage`、`placementRestOfSearch`、`placementAmazonBusiness`（0% 也可显式建行，四个都能传） |
| 活动否词 Match Type | `negativePhrase`、`negativeExact`，State `enabled` |
| 关键词 Match Type | `exact`、`phrase`、`broad` |
| 商品投放表达式 | `asin="B0XXXXXXXX"`（无空格；导出表 Resolved 列也是这个写法） |
| 自动投放表达式 | `close-match`、`loose-match`、`substitutes`、`complements`，每类一行，单独 State 和 Bid |
| Start Date | 文本 `YYYYMMDD`，填上传当天（按账户市场时区，美国站按洛杉矶时间判断） |
| Portfolio ID | 填组合正式 ID，可被受理 |
| 临时 ID | 字母开头，如 `tmpCampXXX`、`tmpAdGroupXXX`；子行填完全相同的临时 ID |
| Product Ad | 按 `SKU` 新建 |
| 状态组合 | 活动 `paused` + 广告组、商品广告、目标 `enabled` 一次建成 |

导出表里同一字段常显示为展示名（`Auto`、`Placement Top`、`Negative Phrase`），新建时仍用上面的代码值。

## 已观察但原因未定
- 当天新建后用 Download Campaigns 导出（18:18、18:59 两次，指定活动或全部），`Sponsored Products Campaigns` 均只有表头、没有任何活动行。可能是新建当天数据未就绪，也可能是导出选项问题，未定。新建当天不要依赖导出拿正式 ID，可隔天再导，或先用后台界面确认。
- 后台广告组页面在父活动暂停时，广告组和目标的状态栏也可能显示 Paused；以活动层级和各自设置为准。
- 广告组页出价列显示的 `$0.63 ($0.47-$0.79)` 这类数字是建议出价区间，不一定是实际出价；实际出价以出价字段或导出表为准。

## 导出操作（Download Campaigns 弹窗）
在 Bulk operations 页面创建并下载批量文件：
- `Campaign filter`：可勾 Export specific campaigns only 选指定活动，或不勾导出全部。
- `Targeting and keyword filter`：`Enabled & Paused`。
- 勾选：Sponsored Products data、Paused campaigns、Campaign items with zero impressions、Placement data for campaigns。
- 日期范围只影响业绩列，不影响结构行。
- 后台导出排队可能很慢。

## 模板 Config 允许值
- Product：`Sponsored Products`。
- Entity：`Campaign`、`Ad Group`、`Bidding Adjustment`、`Campaign Negative Keyword`、`Keyword`、`Negative Keyword`、`Product Targeting`、`Negative Product Targeting`、`Product Ad`。
- Operation：`Create`、`Update`、`Archive`。
- 新建活动、广告组、商品广告、关键词、广告组否词、商品投放、否定商品投放的 State：`enabled`、`paused`；更新时还可用 `archived`。
- 活动否词：新建 State 只能 `enabled`；更新 State 只能 `archived`。
- Sites：`Amazon Business`、`Amazon Haul`。Off-Amazon ad serving：`Increase reach`、`Limit off-Amazon spend`（两者实际效果未测，留空可上传）。
- `Campaign Name` 最多 128 字符；`Ad Group Name` 最多 255 字符；`Keyword Text` 最多 80 字符，避免斜杠、脱字符、逗号和连续两个句点。
- `End Date` 须晚于开始日期，无结束日期留空。
- `Daily Budget`、`Ad Group Default Bid`、`Bid` 为非负数字；`Percentage` 最多两位小数。

## 导出表结构细节
- `Daily Budget`、`Ad Group Default Bid`、`Bid` 为数字；`Percentage` 导出为文本 `0.0`，不带 `%`。
- 0% 位置溢价在导出中以显式行出现，四个位置都有行。`Bidding Adjustment` 行不填 `State`。
- 子实体行靠 `Campaign ID` / `Ad Group ID` 挂父级，名称在 Informational 列。
- `ASIN` 是 Informational 列，不能代替 `SKU`。
- 活动否词只挂 `Campaign ID`；广告组否词还要 `Ad Group ID`。

## 新建草稿结构（已验证）
所有行用同一个活动临时 ID，子行再引用同一个广告组临时 ID。

**自动活动（13 行示例）**
1. `Campaign / Create`：名称、`AUTO`、`paused`、日预算、Start Date、竞价策略、Portfolio ID。
2. `Ad Group / Create`：名称、`enabled`、默认出价。
3. `Product Ad / Create`：SKU、`enabled`。
4. `Bidding Adjustment / Create` ×4：每个位置一行。
5. `Product Targeting / Create` ×4：四类自动目标，各设 State 和 Bid。
6. `Campaign Negative Keyword / Create`（可选）：文本、`negativePhrase` 或 `negativeExact`、`enabled`。

**手动关键词活动（10 行示例）**：Campaign（`MANUAL`）、Ad Group、Product Ad、Bidding Adjustment ×4、`Keyword / Create` ×3（同一词的 exact、phrase、broad，各设 Bid 和 State）。

**手动 ASIN 活动（9 行示例）**：Campaign（`MANUAL`）、Ad Group、Product Ad、Bidding Adjustment ×4、`Product Targeting / Create` ×2（`asin="…"`）。

选词和选 ASIN 用真实报告里出现过的数据；绝不编造 ASIN，不投自己的商品 ASIN。

## 上传后核对流程
1. 等页面从 Uploading 变成处理完成，记下成功和失败行数。Uploading 或下载链接都不代表成功。
2. 有失败行时，只针对失败行修正后补传，不整包重传。
3. 用后台界面或隔天导出确认：活动状态、预算、各目标状态与出价、溢价、否词，并记下正式 ID。
4. 把新确认的写法和现象更新回本技能，标明验证层级。

## 测试进度
1. 已完成：只读导出对照，确认四类自动目标写法、层级、组合 ID。
2. 已完成（2026-11-19）：自动活动 Create 13/13 成功。
3. 已完成（2026-11-19）：手动关键词 + 手动 ASIN 活动 Create 19 行，后台可见全部目标。
4. 待做：Update 测试。计划在同一暂停测试自动活动上改 loose-match 出价、改暂停目标出价、开启单个目标；需要正式 ID，因当天导出为空而暂缓。
5. 待做：经用户确认后归档测试活动。

## 尚未验证
- `Update` 和 `Archive` 的写法、必填字段、错误提示。
- Update 时用代码值还是展示名。
- 逐行结果报告从哪里下载、是什么格式。
- 新建当天导出为空的确切原因。
- `Off-Amazon ad serving`、`Sites` 的实际效果。
- SI 自动规则、止损、分时不在 Bulk 模板内，需在 SI 里另行配置。

## 案例记录：AutoSell ring banner（美国站，2026-11-19）
- 商品：SKU `8H-M207-8UMI`，ASIN `B0H6J55GBP`；组合 ring banner HARVEST `94692977159392`。
- 测试活动（均暂停、$1、Dynamic bids - down only）：
  - `TEST AUTO ring banner BULK`：广告组 `TEST RING BANNER`，默认 0.50；loose-match enabled 0.50，其余三类 paused 0.50；Top 50%，其他 0；活动否词 `ring boxes`（词组）、`just married`（精准）。上传 ID `1c67294d-c7a0-474c-88fd-f3a6c74481b5`。
  - `TEST MANUAL KW ring banner BULK`：`engagement decorations` exact/phrase/broad，各 0.50。
  - `TEST MANUAL ASIN ring banner BULK`：`B0BWR49R77`、`B0F48KS4Q2`，各 0.50。
- 文件在共享电脑 `/workspace/ringbanner/bulk_official/`：官方空白模板、AUTO V2 导出、`TEST_AUTO_ring_banner_BULK_create_v1.xlsx`、`TEST_MANUAL_KW_ASIN_ring_banner_BULK_create_v1.xlsx` 及各自 audit。
- 已获批准但未执行的 Update：loose-match 0.50→0.55；close-match 保持暂停，0.50→0.45；substitutes 暂停→开启，出价 0.50。
