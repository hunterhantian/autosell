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
- 新建文件不包含 SI 自动化规则；SI 可能另有自动继承，需现场审计。仅在用户批准具体规则后挂接；不得因启用活动而自动沿用旧止损规则，尤其是已撤回的规则。

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

## 2026-11-22 增补：平行重建与文件审核

## 官方三层状态与精确 ID（已观察，并用于本地去重）

- 判断一条投放当前是否在投，必须同时看三个层级的官方状态，并用精确 ID 对齐：活动、广告组、targeting（关键词或商品目标）。任一父级未开启，targeting 自身为 enabled 也不等于正在投放。
- 报告快照、导出表状态、研究表上的「未暂停」，都只代表各自取数时刻，不能代替生成前的官方库存复核。
- 近窗花费（例如最近 7 天有花费）只说明该对象在统计期内曾经产生花费，**不能证明当前仍在投放**。花费可能发生在暂停之前，也可能来自其他同文案、不同匹配或不同父级的对象。
- 去重和保护名单必须落到精确的 campaign / ad group / target ID，以及「词或 ASIN + 匹配类型」。不要只靠活动名、广告组名或词面相同就合并或改写。

## 保护盈利盘，同时保护共享父级（方法约束）

- 已确认的盈利投放保持不动：不在本批新建中重复同一对象，也不用新建文件去改它们的出价、状态或匹配。
- 只保护这些 targeting 不够。它们所在的活动、广告组是共享父级：向父级写入活动级否词、改预算、改位置加价或改默认出价，都会连带影响未纳入本批的其他投放。
- 因此，新结构一律平行搭建，只使用临时 ID；未经用户明确批准，不把真实父级 ID 写入 Create/Update 行。对旧来源的否定若需要，应单独成文件、单独审批，默认不写入受保护父级，也不混进已交审的新建文件。

## 同词同匹配去重，不同匹配保留（已用于本地生成，创建受理待实测）

- 去重键是「关键词文本 + 匹配类型」，或「ASIN + 商品投放」。同一词的 exact / phrase / broad 是不同对象，应保留，不得为了表干净而统一改成 exact。
- 旧 targeting 迁到新手动活动时，保留原有匹配，不改写。搜索词新收词默认按 exact（商品搜索词按 ASIN 商品投放）。不得把旧投放改成 exact 再与搜索词合并。
- 与当前官方三层均开启的同词同匹配、与受保护盈利盘的同词同匹配、以及已在另一份未废止新建 Bulk 中的同词同匹配，均应排除，不重复创建，也不回写旧项。
- 若官方库存中已有同词同匹配，但并非三层全开，可按用户批准做平行新建，并在审核表标明「重建、不恢复旧项」，不得写成「当前无覆盖」。
- 自动广告四类（`close-match`、`loose-match`、`substitutes`、`complements`）不能搬进手动活动当关键词。类目投放同样不能当作手动关键词或 ASIN 目标拷贝。这类对象应单列「待拆」，另案设计，不写入本批 MANUAL Create。
- 搜索词与投放在同词同匹配上合并时，样本门槛和 CPC/CPA 用该投放自身口径，不得把搜索词词级订单或花费再加到投放上。

## 按流量档分活动，而不是按词或按窗口拆活动（方法约束）

- 手动新建按搜索量档位分活动（一档一个日预算），商品投放单独建活动，不虚构搜索量。空档不建。
- 不要按单个关键词、单个 ASIN，或按历史窗 / 近窗各建一套活动。同一活动内可按匹配类型再分广告组，但预算边界在流量档，不在单个词。
- 高流量与低流量对象若共用一个日预算，低流量对象容易被饿死；这是分档的原因，不是要求每个词单独给预算。
- 活动状态、Portfolio、日预算按该批次用户要求填写。已验证过「活动 paused + 子行 enabled」和「填写 Portfolio 正式 ID」可被受理；若用户要求 Portfolio 留空，按要求留空，但留空本身不是新的已验证默认值。

## 窗口订单、样本门槛与 CPC/CPA 口径（方法约束）

- 只有在用户明确批准「用两窗已知订单相加做样本门槛」时，才把同一对象在各窗的已知订单相加。缺窗不记 0。
- 历史窗与近窗通常有日期重叠，相加**未去重**，只表示「未去重的订单加总是否达到批准门槛」，不能当成独立订单数，也不得用来合算花费、CPA 或 CPC。
- CPA、CPC、花费必须用「同一对象、同一窗口」的原口径。默认优先近窗；近窗没有可用的正值 CPC 或 CPA 时，再用另一窗回补。两窗指标绝不加总、不混用、不臆填。缺 CPC 或 CPA 则不计出价，排除并在审核表说明。

## 项目参数不得写成通用标准

- 样本门槛（例如至少 3 单）、新活动日预算、目标 CPA、位置加价比例，都是**当次用户批准的项目参数**，不是所有商品或所有批次的默认值。
- 换项目或换批次时重新核对当前明确要求，只有关键参数未定且会实质影响结果时才请示，不得把上一批的 3 单、固定日预算、目标 CPA 或某位置 +50% 自动沿用。

## 过线与待优化必须独立成文件（方法约束）

- 「过线保留/新建」与「待优化降价新建」分开出 Create 文件和审核表，不混进已交审文件，也不在未批准时上传。参数修订后，旧版标记作废并另存新版，不覆盖旧文件，不整包重传已作废版本。
- 待优化若按位置加价对齐目标 CPA，基础出价公式为：
  `基础 bid = 同对象历史 CPC ×（目标 CPA ÷ 该对象实际 CPA）÷（1 + 该位置加价百分比）`，再按分币四舍五入（例如加价 50% 时，分母为 1.5）。
- 该公式的假设是转化率不变，因而「目标 CPA ÷ 实际 CPA」用来缩放可接受 CPC；转化率变化时，实际 CPA 不会自动等于目标 CPA。
- 分币舍入后，再乘回（1 + 加价百分比）得到该位置理论静态出价；应对照下调后的目标参考 CPC（历史 CPC × 目标 CPA / 实际 CPA），而非未下调的原 CPC。
- 即使只给搜索其余位置加价、其他位置为 0，且策略为 Dynamic bids - down only，也不保证只出该位置、不保证实际成交 CPC 等于基础 bid，更不保证实际 CPA 等于目标 CPA。down only 还可以在竞价中继续下调。
- 过线新建的出价规则按该批次批准执行，不得把待优化公式自动套到过线文件上。

## 命名可筛选，但命名不授权自动化

- 活动名可加入稳定、可筛选的标记，便于日后用名称或规则条件圈定这批活动。
- 名称只是标签。写入可筛选字样不等于已批准挂自动化规则，也不等于可以开启活动。新建活动默认仍无 SI 规则；要挂规则须另出预览，由用户对具体规则和范围再批准。

## 否词：范围先批准，默认精准，冲突写明（本批已写入本地 Create，活动级否词写法曾在 2026-11-19 受理，本批尚未上传）

- 否词挂到哪些活动（新活动活动级、旧来源活动级，或仅某广告组）必须由用户批准。未经批准，不扩大到全部新活动，更不写入受保护的共享父级。
- 默认匹配为精准否定（`negativeExact`），除非用户另指定词组或其他类型。
- 同一生效范围内，不应把同一词或同一 ASIN 同时作为正向目标和会屏蔽它的否定。冲突项要在审核表逐条写明「投还是否」的决定，不得静默丢弃或两边都写。
- 核心大流量词、近核心变体，以及仅在某一窗亏损、或两窗结论冲突的否词，不得一刀切进全部新活动；来源受限的建议否定，不自动扩大范围。
- ASIN 否定是否定商品投放（`Negative Product Targeting`），不是关键词否词，不能写成 Keyword / negativeExact。若该写法或能力尚未核验，本批不写，单列待确认。

## 本地校验、日期、作废与上传边界

- 用官方空白模板生成、本地校验 0 错误 0 警告，只说明文件结构、枚举值和内部一致性通过。**不等于亚马逊已受理，也不等于可以上传。** 默认只交审、不上传；上传须用户对该具体文件明确批准。
- `Start Date` 仍为文本 `YYYYMMDD`，填**上传当日**的账户市场日历日。美国站用 IANA 时区 `America/Los_Angeles` 做真实转换，须计入冬夏令时，不能把「洛杉矶当日 00:00 = 北京时间 16:00」当成全年固定换算（标准时间 UTC−8 时对应 16:00，夏令时间 UTC−7 时对应 15:00）。若生成日与实际上传日已跨过市场日，必须先改正文日期再交审或上传。
- 参数或纳入范围变更后，旧上传稿和旧审核表标记作废，说明作废原因和替代文件；旧文件保留不覆盖。已作废文件不得上传，也不得与现行文件混在一张表。
- 本批平行新建（按流量档分活动、按公式下调基础 bid、各新活动写活动级否词等）**尚未上传实测**。字段代码值仍以 2026-11-19 已验证段落为准；Update / Archive 仍未实测。实证后应把结果按「已验证 / 已观察但原因未定 / 待测试」回写本技能，不要把本地校验通过写成已验证受理。
