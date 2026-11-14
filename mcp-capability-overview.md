# 目前已掌握的 MCP 能做什么（以极目 + Helium 10 为主）

> 整理时间：2026-11-14（北京时间，Asia/Shanghai）。项目 AutoSell，主推 ring banner，ASIN `B0H6J55GBP`，售价 $13.99，最大 CPA $8，盈亏平衡 ACOS 约 57%，目标 ACOS 45–50%，广告账户 Trendy Treasures Store（美国站）。  
> 本报告以 **极目数据（Jiimore，已用 Key 实测：live 目录 19 个全部只读）** 和 **Helium 10（本账号已连接 197 个工具）** 为主，分三块写：① 数据研究与调查；② 能搭建或改什么（广告、Listing、出价）；③ 缺口与限制。并单独增加「极目 MCP 实测与评价」。其他已连接或已调研的 MCP 只作配套说明。  
> 只用代码抓取和已连接连接器的只读调用，没有用可视化浏览器，也没有调用任何写入 / 创建类工具。报告页面与本文件事实一致（页面是同一内容的排版版）。本次按你的要求把本文件和更新后的 `jiimore-mcp-tools.md` 存入 `Traffic_Research` 分支。未核实的事项标为 **未证实**。任何亚马逊侧写入仍须按批次确认。密钥未写入本文件。

## 先看结论

1. **研究已经能做。** Helium 10 已连接，可对 `B0H6J55GBP` 做关键词、竞品、Listing、ABA、利润、库存和广告表现分析。本周期 MCP 配额限额 1,000 次，已用 192 次，还剩 **808** 次（`get_mcp_usage_info`，该接口本身不占配额）。极目也已实测可查（见下条）。
2. **极目已经能用。** 2026-11-14 17:26–17:28（北京时间）用 Key 通过 HTTP JSON-RPC（streamable HTTP）连上 `https://www.jiimore.com/mcp`。实测有效请求头是 `x-api-key`（`Authorization: Bearer` 返回 invalid_token，`X-Mcp-Key` 无效）。协议版本 `2025-11-25`，initialize 之后必须带 `MCP-Protocol-Version`。服务端 `jiimore-mcp-server 1.0.0`。`tools/list` 实际有 **19** 个只读工具（官网介绍 17 个，多出 `jiimore_get_keyword_bids`、`jiimore_get_keyword_trends`）。按「每个工具只调用 1 次」全部调用成功（19/19）；其中 3 个对种子词 `ring banner` 返回空列表。响应里没有返回消耗点数或剩余额度。参数 schema 已公开，不再写「未公开」。
3. **广告结构在 Helium 10 侧已经露出可创建的动作类型。** 本次实测：`get_ads_query_platforms` 返回 `amazon`、`walmart`；`get_ads_campaign_mgmt_platforms` 进一步返回亚马逊侧可管理动作，包括创建 SP 活动 / 广告组 / 广告 / 关键词 / 商品投放 / 否定词，以及更新上述对象，单次上限 `maxActionsPerRequest = 20`。这说明 **Ads PPC 授权已经能用**（此前报告里写“未能独立验证”，本次已用只读接口核实）。但本次没有执行任何写入，**账户是否已是 Managed 模式（官方要求，并收 2% 广告花费管理费）仍未证实**；真正创建或改价仍须按批次确认。
4. **Get MCP Ads 现在能读、不能写。** 已连到 Trendy Treasures Store 美国站档案 `NA:1475642200050500`（USD），工作区权限显示 write，但套餐是 Free，`writes: false`。Amazon Ads 能力清单共 107 个工具：30 个只读现在可用，77 个写入工具均为 `plan_required`。付费后仍是一次调用创建一个对象（来自既有调研，本次未再单独核实计费页价格，价格 **未证实**）。
5. **没有哪条路径能在零确认的情况下自动建完整广告结构。** 极目全部只读，不能建广告或改 Listing。Scale Insights 的 MCP 不能新建活动 / 广告组 / 关键词；它的网页 Mass Campaigns 可以建，但要人在界面里点 Preview 再 Execute。领星、旺旺、买家消息都没有已连接的 MCP。

## 连接现状（2026-11-14，北京时间实测）

| 连接器 | 状态 | 工具数 | 说明 |
|---|---|---:|---|
| Helium 10（`user-helium10`） | 已连接 | 197 | 152 读 / 45 写。本次 Ads 只读探测成功。配额 1,000，已用 192，剩余 808 |
| Get MCP Ads（`user-get-mcp-ads`） | 已连接 | 状态接口显示 60 | Free 套餐；Amazon Ads 已选 1 个账户。能力清单另列 107 个 Amazon 工具（含未出现在初始列表里的） |
| Get MCP Ads Amazon（`user-get-mcp-ads-amazon`） | 需要授权 | 0 | `needsAuth`，本次不可用 |
| X（`user-X`） | 已连接 | 101 | 可发帖、读帖、私信等；对外发帖须先确认 |
| 公开 X（`x`） | 可用 | 13 | 只读公开帖文、用户、趋势、新闻 |
| Gmail（`user-Gmail-xai`） | 已连接 | 17 | 可搜、读、起草、发送；发出前须确认 |
| Google Drive（`user-Google Drive-xai`） | 已连接 | 5 | 搜索、读取、列目录、建文件夹、进回收站。没有专门的“新建 Docs/Sheets”工具 |
| GitHub（`user-GitHub-xai`） | 已连接 | 112 | 本项目文档所在仓库 `hunterhantian/autosell` |
| Notion（`user-Notion-xai`） | 已连接 | 77 | 页面、数据库、日历；可做台账和报告 |
| Finance（`user-Finance-xai`） | 需要授权 | 0 | `needsAuth`，本次不可用 |
| 极目 Jiimore | **已用 Key 实测连通** | **19（全部只读）** | 端点 `https://www.jiimore.com/mcp`；有效请求头 `x-api-key`。官网介绍 17 个，live 目录多出价和趋势 2 个。19/19 调用成功，3 个对 `ring banner` 空结果。参数 schema 已从 `tools/list` 取得 |
| Scale Insights MCP | 未连接 | — | MCP 不能建结构；网页 Mass Campaigns 可建，但要手动 Preview / Execute |
| 领星 Lingxing | 未连接 | — | 见下文“条件达成后” |

Get MCP Ads 已选账户：Trendy Treasures Store，`NA:1475642200050500`，币种 USD。同源连接下还能看到 CAD、MXN 两个同名档案，但未勾选。默认报表周期：最近 7 天（2026-11-07 至 2026-11-13）。

---

## 一、数据研究与调查

这一块今天就能用 **Helium 10 + 极目** 做对照；两边口径不同，不能把数字当成同一套指标，也不能互相替代。

### 现在就能做（Helium 10，已连接）

针对 ring banner `B0H6J55GBP` 和竞品，可用的只读工具包括：

- **选品 / 关键词：** `get_keywords_by_asin`（Cerebro 反查）、`get_keywords_by_keyword`（Magnet 拓词）、`analyze_keywords`（批量指标）、`search_amazon_keywords`、`search_amazon_brand_analytics`（ABA）、`get_keywords_search_volume_history`、`get_keywords_cpc_history`、`get_keywords_sales_history`、`get_top_keywords`、`compare_asin_keywords`、`get_google_trends_history`
- **竞品 / ASIN：** `search_competitors_by_asin`、`get_competitor_overview`、`search_product_targets_by_asin`（FBT / 也购买 / 建议投放）、`search_niche_by_keyword`、`search_products`、`get_asin_bsr_history`、`get_asin_price_history`、`get_asin_reviews_history`、`get_asin_review_analysis`
- **Listing 分析（不改线上）：** `get_listing_details`、`get_listing_score`、`compare_listings`、`retrieve_listing_by_asin`、`get_keyword_performance_with_competitors`、`analyze_product_images`
- **广告分析：** `get_ads_query_platforms` → `get_ads_query_list` / `get_ads_query_schema` / `get_ads_query_materials` → `execute_ads_query`；`execute_ads_changelog_query`；`get_amazon_ads_average_budget_utilization`、`get_amazon_ads_current_budget_utilization`；`list_ads_rules`、`get_ads_rule`（只读规则）。本次已确认平台列表和活动管理 schema 可返回，查询链路可用的前提已经满足。执行 `execute_ads_query` 前仍须让用户确认具体 profile，不能默认全选。
- **利润、流量、库存：** `get_account_profit_and_loss_summary` 及 series / breakdown、`get_product_profit_and_loss_summary` 及系列、`list_amazon_cogs`、`get_amazon_seller_traffic`、`get_amazon_product_traffic`、`get_inventory_values`、`get_fba_inventory_health_status`、`get_sales_velocity`、`get_search_query_performance`（SQP，需要品牌备案 ASIN，本产品是否品牌备案 **未证实**）
- **客服跟进只读：** `get_followup_account_status`、`list_followup_automations` 及表现、邮件 / 索评统计。MCP 不能创建或修改 Follow-Up 规则。

注意：仓库里已有的流量研究（`helium10-diamond-ring-balloons-traffic-research.md` 和 `traffic-research-skill.md`）是按 **Diamond Ring Balloons** 做的，不是 ring banner。数字和分层不能直接套到 `B0H6J55GBP`，需要重新跑一轮。极目本次按 ASIN 找出的第一个细分市场也是「气球拱门套件」，同样不能直接当 ring banner 的市场定义。

配套只读：Get MCP Ads 现在可以拉该美国站档案的活动、广告组、关键词、投放、否定、报表（如 `amazon_ads_get_campaigns`、`amazon_ads_get_ad_groups`、`amazon_ads_get_keywords`、`amazon_ads_get_targets`、`amazon_ads_get_product_ads`、`amazon_ads_create_report` / `amazon_ads_get_report` 等 30 个可用只读工具）。这是账户内广告结构的实盘读取，和 Helium 10 / 极目的市场研究互补。

### 现在也能做（极目，已实测，19 个全部只读）

参数统一包在 `request` 里，可选 `countryCode`（US / JP / DE，本次全部用 US），分页工具还有 `page`、`pageSize`（1–50，默认 20）。分类如下（含 live 目录多出的 2 个）：

- **关键词 / ABA / 出价 / 趋势（9）：** `jiimore_expand_aba_keywords_by_keyword`（`keywords` 1–20）、`jiimore_search_aba_keywords`（`asins` 1–20）、`jiimore_search_keywords_batch`（`keywords` 1–20）、`jiimore_search_keywords_by_asin`（单个 `asin`）、`jiimore_search_keywords_by_keyword`（单个 `keyword`）、`jiimore_get_keyword_rankings`（单个 `asin`，`days` 为 7 或 30，默认 7）、`jiimore_get_niche_keywords`（`nicheId`）、**`jiimore_get_keyword_bids`（新；`keywords` 1–20，可选 `asin` 与 `matchTypes`，仅 US）**、**`jiimore_get_keyword_trends`（新；`keywords` 1–5，可选 `startDate`/`endDate`，默认近 12 个月）**
- **商品 / ASIN（6）：** `jiimore_find_asins_batch`（`asins` 1–20）、`jiimore_get_asin_details`（`asins` 1–20）、`jiimore_find_similar_asins`（`asins` 1–20）、`jiimore_find_aba_asins_by_keyword`（`keywords` 1–20）、`jiimore_find_asins_by_keyword`（单个 `keyword`）、`jiimore_find_same_niche_asins`（单个 `asin`）
- **细分市场（4）：** `jiimore_find_niches_by_asin`（单个 `asin`）、`jiimore_find_niches_by_keyword`（单个 `keyword`）、`jiimore_get_niche_details`（`nicheId`）、`jiimore_get_niche_asins`（`nicheId`）

极目没有本账户广告、利润或库存接口，也没有任何写入能力。套餐价格第三方文章写过基础版活动价 99 元/月等，**官网定价仍未核实**。本次响应没有返回调用点数。

Helium 10 与极目都有 ABA / 反查 / 竞品，但口径不同。极目相对 H10 多出来的，主要是：**关键词和 ASIN 的点击转化率 / 搜索转化率**、**按匹配类型的亚马逊建议出价（SP + SB 区间）**、**带点击和转化的周度趋势**、细分市场的 PDR 评论洞察和市场潜力、中文译名，以及 ASIN 详情里的 FBA / 推荐费估计。H10 相对极目多出来的，是账户内广告、利润、库存、SQP、价格 / BSR 历史，以及写入能力。

---

## 极目 MCP 实测与评价

测试约定：每个工具只调用 1 次，失败不重试。输入围绕 ring banner：关键词 `ring banner`、`will you marry me banner`、`diamond ring balloon`，ASIN `B0H6J55GBP`，站点 US。细分市场 ID 只使用前面工具返回的结果；三个依赖 `nicheId` 的工具，用的是 `find_niches_by_asin` 返回的第一个 ID（标题是 **balloon arch kit / 气球拱门套件**，与核心产品相关度低，结论里会单独说明）。19 次业务调用合计耗时约 46 秒，另加 initialize / tools/list。

### 总评

**极目 MCP 已经能作为 Helium 10 的只读对照源使用，值得纳入 ring banner 研究流程，但不能替代 H10，也不能直接拿返回的第一个细分市场做结构。**

对 ring banner 最有用的是：ASIN 反查（29 个词，爬取日期新至 2026-11-14）、ABA 拓词（3 个种子扩出 132 个词，带转化和 CPC）、建议出价（3 个词 × 3 种匹配的 SP/SB）、周度趋势，以及相似商品（107 个，相关性明显好于「按 ASIN 找细分市场」）。需要警惕的是：按关键词解析类目的 3 个工具对长尾词 `ring banner` 会空返回；按 ASIN 找关键词 / 细分市场会漂到花环、气球拱门、万圣节装饰等相邻大类；部分列表的 `currentPrice` 为 0；建议出价的 `fetchedAt` 年份是 2612，明显是时间戳异常，不能当新鲜度用。

### 分工具实测表

| # | 工具 | 结果 | 耗时 / 大小 | 返回摘要与样例 | 评价（用途 / 质量 / 与 H10） |
|---|---|---|---|---|---|
| 1 | `jiimore_find_niches_by_asin` | 成功 | 1.7s / 21.2KB | 入参 `asin=B0H6J55GBP`。60 个细分市场，本页 20 条。第一条是 balloon arch kit，需求 1,386,738，7 天搜索量 118,127，38 个商品 / 32 个品牌。前 20 条全是花环、气球拱门、节日装饰，没有 engagement / ring banner 市场 | 能发现 ASIN 被归到哪些 OE 细分市场，但**相关度差**，不能当 ring banner 的市场定义。H10 也有选品/类目工具，极目的需求、转化、集中度指标可对照，必须人工筛选 |
| 2 | `jiimore_find_niches_by_keyword` | 成功但空结果 | 1.3s / 221B | 入参 `keyword=ring banner`。`totalRows=0`。文档写明：先按词解析类目，解析不到就空页 | 对长尾精确词很脆。研究 ring banner 不要以它为入口，改走 ASIN 或 ABA。H10 Magnet 对同一词通常仍能出词表 |
| 3 | `jiimore_get_asin_details` | 成功（COMPLETE） | 5.3s / 1.5KB | 标题与产品一致（Doczen 80-inch ring banner）。`currentPrice` 12.18 USD（用户记录售价 $13.99，需核对）；FBA 费 3.43、推荐费 1.87（360 天口径）；7 天点击 142、30 天 1,381；点击转化率 14.1%，综合 21.1%；BSR Garlands #739；有卖点摘要 | **很有用。** 极目特色是点击转化率和费用估计，H10 的 Listing / 价格 / BSR 历史更完整。价格是带长小数的计算值，不宜当当前标价 |
| 4 | `jiimore_find_asins_batch` | 成功 | 1.2s / 778B | 1 条摘要：品牌 DOCZEN，评分 5.0，9 条评论；`currentPrice=0.0`（schema 已警告列表价可能不全） | 适合批量扫摘要，单 ASIN 不如详情工具。价格字段不可靠。和 H10 商品摘要重叠，深度更浅 |
| 5 | `jiimore_find_asins_by_keyword` | 成功但空结果 | 1.0s / 221B | 入参 `keyword=ring banner`。0 条。同样先解析类目 | 长尾词入口失败。找商品改用 ABA 商品搜索或 H10 搜索 |
| 6 | `jiimore_find_same_niche_asins` | 成功，字段不全 | 1.5s / 2.9KB | 480 个同细分商品，本页 10 条。有 ASIN、价格、评分、评论、点击和转化；**全部没有 title / brand**。第一条 B0DPJHKBDH，转化率 13.6% | 量很大，但缺标题，必须再查详情才能判断是不是竞品。因为依赖的细分市场会漂移，名单噪声也大。H10 竞品工具更直接 |
| 7 | `jiimore_get_keyword_bids` | 成功（新工具） | 5.7s / 3.3KB | 3 个词 × BROAD/PHRASE/EXACT，SP 和 SB 各 9 条，无 noBid。SP 样例：ring banner 广泛/词组建议 $0.68（0.51–0.85），精确 $0.60；will you marry me banner 三种匹配都是 $0.60；diamond ring balloon 广泛 $0.63，词组/精确 $0.65。SB 三个词几乎都是 $0.96 / 精确 $0.93。`fetchedAt=2612-01-19T15:16:12Z`，年份异常 | **对出价草案很有用，且 H10 清单里没有同形态的「按匹配类型 + ASIN 的亚马逊建议出价」。** SB 三个词几乎一样，可能偏通用；`fetchedAt` 不能当新鲜度。建议和 H10 CPC 历史、目标 ACOS 45–50%、CPA $8 一起用，不要单看建议价 |
| 8 | `jiimore_get_keyword_rankings` | 成功 | 1.7s / 15.2KB | 入参 30 天窗口。29 个反查词，本页 20 条。自然排名、flowScore、首页/Top16 覆盖、7 天量与转化、`lastSpiderDate`。样例：engagement decorations 搜索量 3,621、转化率 13.7%、自然排名 82，爬取日 2026-11-14；engagement banner 搜索量 850、转化率 14.6%、自然排名 57，首页 ASIN 数 1，爬取日 2026-11-13。本页没有 `ring banner` 本身（共 2 页，未翻页） | **非常适合 ring banner 词表。** 词更贴近订婚装饰，新鲜度高。和 H10 Cerebro 重叠，但带点击转化率和中文译名。值得与 Cerebro 对照，口径不要混用 |
| 9 | `jiimore_get_keyword_trends` | 成功（新工具） | 1.8s / 47.9KB | 默认近 12 个月。will you marry me banner、diamond ring balloon 各 48 周，到 2026-10-25；ring banner 只有 12 周，且只到 2026-08-23，缺近两个多月。ring banner 7 天搜索量约 166–223；will you marry me banner 最近一周 340 搜索 / 5 单，转化率约 7%；diamond ring balloon 最近一周 46 搜索 / 4 单，转化率约 26%，CPC 高（1.08–1.80） | **H10 有搜索量 / CPC / 销量历史，但极目把点击、转化、ABA 排名和 CPC 放在同一条周序列里，是增量。** ring banner 序列不完整，做季节判断要谨慎 |
| 10 | `jiimore_search_keywords_batch` | 成功，缺 1 个词 | 1.2s / 1.3KB | 3 个词只回来 2 个：`will you marry me banner`、`diamond ring balloon`。`ring banner` 未出现，也没有 warning | 适合一次拉多个已知词的量、转化、CPC。长尾词可能被默默丢掉，要用 trends 或出价工具补看。和 H10 `analyze_keywords` 类似，转化口径是极目的长处 |
| 11 | `jiimore_search_keywords_by_asin` | 成功，相关度差 | 1.5s / 11.1KB | 1,373 个词，本页 20 条。前排是 balloon arch kit（32,463）、halloween garland、balloon arch、star garland、toran 等，本页没有 ring / engagement banner | 体量大，但按类目扩展，噪声很高，不适合直接当广告词表。应用反查或 ABA 拓词做主词表，这个只作补充并人工过滤。H10 反查通常更贴商品 |
| 12 | `jiimore_search_keywords_by_keyword` | 成功但空结果 | 2.5s / 222B | 入参 `ring banner`。0 条。先解析类目 | 与第 2、5 个工具同一弱点。拓词请改用 ABA 拓展 |
| 13 | `jiimore_expand_aba_keywords_by_keyword` | 成功 | 9.6s / 19.3KB | 3 个种子扩出 132 个词，本页 20 条。前排：engagement party decorations（16,327 搜索 / 797 单 / 转化率 12.9%）、will you marry me sign、engagement decorations、proposal decorations 等。含竞争度、关联度、Top ASIN、广告花费估计、ACOS/CPA | **本次最有价值的拓词工具之一。** 词更贴近订婚场景。耗时最长但仍可接受。和 H10 ABA / Magnet 重叠，转化、关联等级、广告估计是增量。建议作为 ring banner 草案种子来源，再和 Cerebro、H10 ABA 对照 |
| 14 | `jiimore_find_aba_asins_by_keyword` | 成功 | 1.3s / 2.7KB | 入参只传了 `ring banner`，仍返回 4 个商品。例如 ATILOY 戒指横幅（B0DFYF52J3，约 $5.79，评分 4.4） | **对找直接竞品有用**，即使类目解析类工具是空的，ABA 商品搜索仍能出戒指横幅。和 H10 竞品搜索互补 |
| 15 | `jiimore_find_similar_asins` | 成功 | 1.7s / 6.5KB | 107 个相似商品，本页 10 条。带 similarityScore 和「高相似 / 中相似」。前排：LMGERBUD 戒指气球（分 100，转化率 16.9%）、戒指纸屑、Just Engaged Banner（JCWTO，分 18）等 | **相关度明显好于按 ASIN 找细分市场。** 适合建 ASIN 定投观察池，但仍混有气球和横幅，要人工分。和 H10 竞品 / 定投建议重叠，相似度分数是增量 |
| 16 | `jiimore_search_aba_keywords` | 成功，条数少 | 1.0s / 2.1KB | 按 ASIN 只返回 2 个 ABA 词：engagement decor（935 搜索，转化率 13.5%，关联等级 3）、bridal decor（201 搜索，转化率 17.3%） | 能出高相关词和广告估计，但条数远少于 ABA 按词拓展（132）和反查（29）。当作补充，不要当主词表 |
| 17 | `jiimore_get_niche_details` | 成功，但市场选错了核心品 | 1.3s / 15.3KB | 用的是第一个 niche：balloon arch kit。有基本面、95 周趋势（2025-01-12 至 2026-11-01）、潜力信号（商品数、价格、集中度、成功新品）、正负 PDR 评论（易组装、掉气、颜色不符等），退货率 5.6%，广告商品占比 97% | 工具本身信息密度高，PDR 和市场潜力是 H10 少见的增量。**这次样本是气球拱门，不能用来写 ring banner 的市场结论。** 以后必须先人工挑 niche 再查详情 |
| 18 | `jiimore_get_niche_asins` | 成功，同上，市场不对 | 1.7s / 6.8KB | 该 niche 38 个商品，本页 10 条，全是拱门支架 / 打气筒等。多条 `currentPrice=0`。有 90 天点击份额 | 看市场结构有用，但本次名单与 ring banner 无关。价格字段经常为 0 |
| 19 | `jiimore_get_niche_keywords` | 成功，同上，市场不对 | 2.9s / 15.2KB | 20 个词，全是 balloon arch kit / stand 等，带 90 天点击份额和搜索份额 | 市场关键词画像完整，但本次不能当 ring banner 词表 |

### 对 ring banner 研究的实操建议

1. 优先调用：`get_keyword_rankings`、`expand_aba_keywords_by_keyword`、`get_keyword_bids`、`get_keyword_trends`、`get_asin_details`、`find_similar_asins`、`find_aba_asins_by_keyword`、`search_keywords_batch`。
2. 不要把 `find_niches_by_keyword` / `find_asins_by_keyword` / `search_keywords_by_keyword` 当作长尾词的第一入口；空结果不等于没有市场。
3. `find_niches_by_asin`、`search_keywords_by_asin` 的结果要当「类目相邻池」看，先人工去掉花环 / 拱门 / 节日装饰，再考虑是否调用 niche 详情。
4. 建议出价可写入草案，但要和目标 ACOS 45–50%、CPA $8，以及 H10 CPC 历史一起算；不要使用异常的 `fetchedAt`。
5. 极目仍不能创建或修改广告、Listing。写入路径还是 Helium 10（须确认）等。

---

## 二、能搭建或改什么（广告、Listing、出价）

### 现在就能做的“准备”，还不能直接改线上

- **在 Helium 10 里生成 Listing 草稿：** `generate_listing`、`rewrite_listing_section`、`request_image_upload_url`。这些先写在 Helium 10 侧，不会自动上架。
- **读取并设计广告结构：** 用上一节的 H10 研究工具和极目的反查 / ABA / 建议出价，加上 `get_ads_campaign_mgmt_platforms` / `get_ads_campaign_mgmt_schema`，可以列出将要创建的活动、广告组、关键词、ASIN 投放和出价草案，按目标 ACOS 45–50%、最大 CPA $8 重算后再给你确认。
- **读广告规则和标签：** `list_ads_rules`、`get_ads_rule`、`list_ads_tags`。MCP 不能创建或打广告标签，要到 Helium 10 Ads 界面操作。

### 按批次确认后，Helium 10 可以改的东西

写入工具已在本账号清单中。官方要求广告档案为 **Managed** 模式，不能是 Disable 或 Analytics-only，否则可能出现 `FEATURE.ADS_ACCESS_DISABLED`。Managed 模式会对经 Helium 10 Ads 管理的广告花费收 **2%** 管理费。本次只读探测没有报这个错误，但**没有做写入实测，不能据此断定账户已经是 Managed**。

确认之后可用：

- **广告结构与出价（终端写入）：** `execute_ads_campaign_mgmt_actions`。本次读到的亚马逊动作类型包括：`campaign_create`（支持 Sponsored Products）、`adgroup_create`、`ad_create`、`keyword_create_sp` / `keyword_create_sb`、`target_create_sp`（以及 SB / SD / STV）、否定关键词和否定投放的创建与更新，以及活动 / 广告组 / 关键词 / 投放的更新。单次最多 **20** 条 action（与官方《Using Ads Write Capabilities》一致）。官方文章另写每类最多 10 个 item、不能删除活动只能暂停；工具说明里出现过 delete，两处不一致，这里并列记录。
- **广告自动化规则：** `manage_ads_rule`。`draft_open` / `draft_save` / `draft_discard` 只动草稿；`draft_commit`（`automated=true`）会改线上出价、日预算或位置等，`rule_delete` 会永久删规则。带 Tactic 的 BID 规则不能通过 MCP 编辑。
- **把 Listing 文案推到 Seller Central：** `sync_listing_to_amazon`（标题、卖点、要点、描述）。这是破坏性写入，必须有明确的“发布”同意；只认可文案不算。不改主图、视频或 A+。必须带 SKU；本产品 SKU 当前报告未掌握。
- **利润核算写入（Helium 10 侧成本，不是改广告）：** `edit_amazon_cogs`、`delete_amazon_cogs`、`create_amazon_indirect_cost`、`edit_amazon_indirect_cost`、`delete_amazon_indirect_costs`。同样要先确认。

### 条件达成后，其他工具能改什么

| 路径 | 条件 | 能做 | 不能做 |
|---|---|---|---|
| Get MCP Ads 写入 | 升级出 Free 套餐（价格 **未证实**）；工作区已是 write，账户已连上 | 能力清单里有创建 / 更新 / 归档 SP、SB、SD 的活动、广告组、广告、关键词、投放、否定，以及改预算和状态。既有调研：免费档为 `plan_required`，付费后一次调用创建一个对象 | 现在 Free 档 77 个写入工具全部不可用。不适合一次搭整套结构 |
| Scale Insights MCP | 连接 SI 账户。试用 30 天，自动化限 1 个产品、1 个站点；付费按 ASIN：5 个 $78/月，10 个 $98，20 个 $178 | 读算好的数据；直接改出价、预算、否定词、启停、位置加价等（先预览再批准）。可生成规则包由 SI 每日执行 | **MCP 不能新建活动、广告组或关键词** |
| Scale Insights 网页 Mass Campaigns | 有 SI 账户，人在网页里操作 | FMC / SKC / SPC / BAC 四种模式可建活动 | 必须手动点 Preview，再点 Execute。没有公开 API，也没有 SP Bulk Sheet 上传 |
| 领星 Open API | 未连接；需开通并授权写入 | 可创建 SP 活动、广告组、广告和关键词 | 不能创建商品投放（product targets） |
| 领星 MCP | 未连接；仅付费可用（既有调研） | 可创建 SP 投放 | 不能创建活动 |

极目没有写入能力。本次 19 个工具的 `readOnlyHint` 全部为 true，也没有实测到任何写入入口。

---

## 三、缺口与限制

### 环节对照表

图例：**现在能做** = 已连接且本次或文档已证实可用（写入仍须批次确认）。**条件后能做** = 需要套餐、授权或人工网页步骤。**做不到** = 目前没有可用 MCP 路径。

| 环节 | 现在能做 | 条件达成后能做 | 做不到 / 明显限制 |
|---|---|---|---|
| 选品 / 关键词研究 | Helium 10 关键词与 ABA 只读工具；**极目已实测**：反查、ABA 拓词、批量词指标、趋势、建议出价 | — | 两边口径不能混用。极目按词解析类目的工具对长尾词会空返回；按 ASIN 扩词噪声大 |
| 竞品 / ASIN | Helium 10 竞品、定投、价格 / BSR / 评论历史；**极目已实测**：ASIN 详情、相似商品、ABA 商品、同细分商品 | — | 极目按 ASIN 找细分市场易漂到花环 / 拱门等相邻大类；部分列表价为 0；同细分结果缺标题 |
| Listing | 分析、生成 / 改写 H10 草稿；极目可提供卖点摘要和费用估计（只读） | 确认后用 `sync_listing_to_amazon` 推标题和描述 | 不能改主图、视频、A+；SKU 尚未掌握。极目不能写 Listing |
| 广告分析 | Helium 10 查询链路已实测可发现平台；Get MCP Ads 30 个只读；极目可提供建议出价和词转化（市场侧，不是账户实盘） | — | 查 H10 广告会消耗配额（还剩 808 次）。极目看不到本账户花费 |
| 广告结构创建 | 可读取现有结构，并看到 H10 可创建的 action 类型 | H10：确认 + Managed（2% 费用，是否已开通 **未证实**）；Get MCP Ads：付费套餐；SI 网页 Mass Campaigns：手动 Preview / Execute；领星：接入后部分可建 | 极目不能建结构；SI MCP 不能建结构；没有零确认全自动建仓 |
| 出价 / 规则优化 | 可读规则和预算使用率；极目可读建议出价（市场侧） | H10 `execute_ads_campaign_mgmt_actions` / `manage_ads_rule`（须确认）；SI MCP 可改出价预算否定（须连接并批准） | Get MCP Ads 免费档不能改；H10 不能在 MCP 里写标签。极目不能改出价 |
| 利润 / COGS | 可读 P&L 和已配置成本；极目详情含 FBA / 推荐费估计 | 确认后可在 H10 写入 COGS / 间接费用 | 若成本没配过，利润数字会不准 |
| 库存 / 补货 | H10：`get_inventory_values`、`get_fba_inventory_health_status`、`get_sales_velocity` | — | 没有已连接的 FBA 货件创建 / 补货下单 MCP。极目无库存接口 |
| 1688 采购 | 可用 Notion / Drive 做台账，Gmail 起草对外邮件 | 可另接 Apify 1688 只读抓取（按量付费，需先接） | **没有已连接的 1688 MCP**；旺旺无 MCP；下单付款必须人工 |
| 客服 / 跟进 | H10 Follow-Up 只读；Gmail 处理邮件 | — | Follow-Up 规则不能在 MCP 里创建或修改；**亚马逊买家消息没有 MCP** |
| 报告 / 文档 | Notion、GitHub、Drive、Gmail、本报告 Page | Get MCP Ads 可导出 Sheets（须另配 Google 连接并确认） | Drive 连接本身不能直接新建 Docs / Sheets |
| 社交 / X 推广 | 已连接 X：读帖、发帖、列表等；公开 X 只读 | — | 发帖、私信须先确认；X Ads 在 Get MCP Ads 上未连接 |

### 主要卡点，以及怎么解开

| 卡点 | 怎么解开 | 已知费用 |
|---|---|---|
| 极目细分市场 / 按词类目解析容易空或漂移 | 研究以 ASIN 反查、ABA 拓词、趋势和出价为主；niche 必须人工挑选后再查详情。Key 已验证可用，不必再为「连不上」卡住 | 第三方文章：MCP 基础版活动价 99 元/月 等；**官网定价未核实**。本次响应未返回点数消耗 |
| 用 Helium 10 真正创建或改广告 | 确认 Ads 档案是否已是 Managed；按批次确认 actions（每批最多 20） | Diamond 已含 MCP（月付 $359，年付约 $279/月）。Managed 另收广告花费的 **2%**。本周期配额还剩 808/1,000 |
| Get MCP Ads 不能创建或改广告 | 升级出 Free（`writes: false`，写入均为 `plan_required`） | 付费价格 **未证实**。既有调研：一次调用创建一个对象 |
| Scale Insights 未连接 | 注册并授权 MCP；试用不用绑卡 | 试用 30 天，自动化限 1 个产品 1 个站点；其后 5 ASIN $78/月 起。MCP 是否另收费，文档未说明 |
| 想用 SI 建结构 | 不用 MCP，改用网页 Mass Campaigns，人点 Preview / Execute | 同上 |
| 领星未连接 | 开通 Open API 或付费 MCP，并授权写入 | 价格 **未证实**。能力不完整：Open API 不能建商品投放；MCP 不能建活动 |
| 1688 / 旺旺 / 买家消息 | 1688 只读抓取可考虑另接 Apify；沟通由 Agent 起草、你来发送 | Apify 1688 Actor 按量付费（既有文档：约 $1/千条，完整商品页曾涨到 $9/千条，时点以当时页面为准） |
| Listing 推送到亚马逊 | 先有 SKU，再明确同意“发布” | 走已有 Helium 10 / SP-API，无额外公开 MCP 功能费 |
| 流量研究不能直接套用 | 对 ring banner 重新分类，不要用 Diamond Ring Balloons 的词表和出价，也不要直接用极目返回的第一个「气球拱门」细分市场 | 消耗 Helium 10 MCP 配额；极目本次已按 ring banner 调用过各 1 次 |

Helium 10 MCP 需要 Diamond 或更高套餐，MCP 功能费包含在内。官方包含配额 1,000 次/月；帮助中心写加购 $50 可增加 500 次。

另：`user-get-mcp-ads-amazon` 和 Finance 连接器目前是 `needsAuth`，不能当作可用能力。

---

## 今天具体可以做的事（带工具名）

1. **为 ring banner 重做关键词和竞品研究**（推荐先做）：Helium 10 的 `get_keywords_by_asin`、`get_keywords_by_keyword`、`analyze_keywords`、`search_amazon_brand_analytics`、`get_keywords_cpc_history`、`search_competitors_by_asin`、`get_competitor_overview`、`search_product_targets_by_asin`；同时用极目已跑通的反查、ABA 拓词、建议出价和趋势做对照。按 45–50% 目标 ACOS、最大 CPA $8 重算出价草案。不要沿用气球品的分层，也不要直接用极目第一个细分市场。
2. **看本账户广告实盘：** Get MCP Ads 的 `amazon_ads_get_campaigns` 等只读工具，或 Helium 10 `execute_ads_query`（先让你确认 profile）。对照现有结构和准备新建的结构，避免重复建仓。
3. **看利润、库存、流量：** `get_product_profit_and_loss_summary`、`list_amazon_cogs`、`get_inventory_values`、`get_fba_inventory_health_status`、`get_sales_velocity`。若 COGS 未配置，先告诉你缺口，不编造成本。可参考极目详情里的 FBA / 推荐费估计，但要和账户实际成本核对。
4. **起草 Listing 文案：** `retrieve_listing_by_asin`、`generate_listing` / `rewrite_listing_section`。在你明确说“发布”之前，不调用 `sync_listing_to_amazon`。
5. **把研究结论写入 Notion 或 GitHub：** 已连接。本报告和更新后的 `jiimore-mcp-tools.md` 已按你的要求存入 `Traffic_Research` 分支。
6. **对外沟通草稿：** Gmail `create_draft`（先不发送）；X 内容可先起草，`create_posts` 须确认。

---

## 针对 ring banner 广告目标的建议下一步

目标：售价 $13.99，最大 CPA $8，盈亏平衡 ACOS 约 57%，投放目标 45–50%。账户：Trendy Treasures Store 美国站。

建议顺序：

1. **先研究、再出结构草案，先不写入。** 用 Helium 10 对 `B0H6J55GBP` 重新分层关键词和 ASIN 定投池，并用极目的反查、ABA 拓词、建议出价和趋势对照，按 45–50% ACOS 和 $8 CPA 上限算出建议出价与预算，列成一批可执行的活动 / 广告组 / 关键词 / 投放清单给你确认。
2. **若你确认用 Helium 10 MCP 搭建：** 先确认 Ads 档案是否接受 Managed 模式及 2% 管理费（本次未证实是否已开通）。然后按每批最多 20 条 action 调用 `execute_ads_campaign_mgmt_actions`，**每一批都先给你看再执行**。
3. **若你不想开 Managed：** 备选是 Scale Insights 网页 Mass Campaigns（你在界面里 Preview / Execute），或升级 Get MCP Ads 后逐条创建（更慢）。SI MCP 本身建不了结构。极目不能承担搭建。
4. **极目已通，作为对照而不是阻塞项。** 本次已完成每个工具一次探测。后续若要继续拉极目数据，应优先走反查 / ABA / 出价 / 趋势，并对细分市场做人工筛选。

项目既有规则不变：没有你按批次明确确认，不会对亚马逊广告或 Listing 做任何写入。

---

## 来源

- 本次实时连接状态：`GetMcpServerStatus` 与各命名空间工具目录（2026-11-14，北京时间）
- Helium 10 只读实测：`get_ads_query_platforms`（返回 amazon、walmart）、`get_ads_campaign_mgmt_platforms`（返回可管理动作及 `maxActionsPerRequest=20`）、`get_mcp_usage_info`（limit 1000，used 192，remaining 808）
- Get MCP Ads 只读实测：`get_connection_status`、`get_sources`、`list_accounts`、`get_capabilities(platform=amazon_ads)`
- 极目 MCP 实测（2026-11-14 17:26–17:28，北京时间）：HTTP JSON-RPC streamable HTTP，端点 `https://www.jiimore.com/mcp`，鉴权头 `x-api-key`，协议 `2025-11-25`；`initialize` + `tools/list`（19 个工具及完整 input schema）+ 每个工具 1 次 `tools/call`。输入为 ring banner 关键词与 ASIN `B0H6J55GBP`、US 站
- 仓库 `hunterhantian/autosell` 分支 `Traffic_Research`：本文件 `mcp-capability-overview.md`、已按 2026-11-14 实测更新的 `jiimore-mcp-tools.md`、`helium10-mcp-tools.md`、`scale-insights-mcp-capability-report.md`、`traffic-research-skill.md`；主分支 `docs/amazon-automation-mcp-map.md`
- 领星能力来自既有调研（Open API 可建 SP 活动 / 广告组 / 广告 / 关键词，不能建商品投放；付费 MCP 可建 SP 投放，不能建活动），本次未重新打开领星后台，保持原结论并标明来源为既有调研
