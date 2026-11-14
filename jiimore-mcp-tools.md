# 极目数据（Jiimore）MCP 工具参考：19 个工具（已连接并实测）

> 整理与实测时间：2026-11-14 17:26–17:28（北京时间，Asia/Shanghai）。项目 AutoSell，测试输入围绕 ring banner：关键词 `ring banner`、`will you marry me banner`、`diamond ring balloon`，ASIN `B0H6J55GBP`，站点 US。  
> 状态：**已用 Key 连接，并完成每个工具各 1 次的只读实测**。端点 `https://www.jiimore.com/mcp`，传输为 HTTP JSON-RPC（streamable HTTP）。实测有效请求头是 **`x-api-key`**（`Authorization: Bearer` 返回 `invalid_token`，`X-Mcp-Key` 无效）。协议版本 **`2025-11-25`**，`initialize` 之后的请求必须带 `MCP-Protocol-Version`。服务端 `jiimore-mcp-server 1.0.0`。密钥未写入本文件。  
> 参数名、类型和限制来自本次 `tools/list` 的 input schema，不再写「未公开」。官方介绍页 [https://www.jiimore.com/ai-mcp](https://www.jiimore.com/ai-mcp) 仍列 17 个工具；live 目录多出 `jiimore_get_keyword_bids`、`jiimore_get_keyword_trends` 两个，下面会单独标明。19 个工具的 `readOnlyHint` 全部为 true，没有写入入口。  
> 本次没有用可视化浏览器，也没有再次调用极目接口来重写本文。未核实的事项标为 **未证实**。

## 工具数量与读写能力

- **live 目录一共 19 个工具，全部只读。** 官网介绍页和第三方文章此前写的是 17 个；本次 `tools/list` 多出建议出价和关键词趋势 2 个。【实测：2026-11-14 17:26–17:28，北京时间】
- 按「每个工具只调用 1 次、失败不重试」全部调用成功（19/19，HTTP 200，`isError` 均为 false）。其中 3 个对长尾词 `ring banner` 返回空列表（调用本身成功）。响应里没有返回消耗点数或剩余额度。
- 官方页面在工具列表末尾写着“持续更新中...”，以后可能还会增加。
- 原先 17 个工具的官方中文名和标签，仍来自页面脚本 `https://www.jiimore.com/assets/mcp-A9WazGJ6.js`。2 个新工具在官网介绍页未列出，中文名是按 `tools/list` 的英文 title 翻译的，表里会注明。
- 下面按任务重新归成三类（新工具归入「关键词 / ABA / 出价 / 趋势」）：

| 分类 | 数量 | 只读 | 可写 | 包含的官方标签 / 说明 |
|---|---:|---:|---:|---|
| 关键词 / ABA / 出价 / 趋势 | 9 | 9 | 0 | ABA、关键词，加上市场标签里返回关键词的工具，以及 live 目录多出的出价、趋势 |
| 商品 / ASIN 工具 | 6 | 6 | 0 | ASIN 标签，加上返回商品的 ABA 和关键词工具 |
| 细分市场工具 | 4 | 4 | 0 | 市场标签里以细分市场 ID 为核心的工具 |
| **合计** | **19** | **19** | **0** | |

> 分类说明：官方的“市场”标签有 6 个工具。其中“同细分市场商品”和“细分市场商品”返回的是商品，放进“商品 / ASIN”类。“ASIN找细分市场”和“关键词找细分市场”返回的是细分市场，留在“细分市场”类。“细分市场关键词”返回的是词，放在第一类。每个工具能查到的官方标签都写在表里，方便核对。

### 通用参数（所有工具相同）

参数一律包在必填对象 `request` 里；未知字段会被拒绝。

- **`countryCode`**（可选，string）：两位市场代码，例如 `US` / `JP` / `DE`。省略时用服务端默认值，且必须在白名单内。`jiimore_get_keyword_bids` 只支持 US；`jiimore_get_keyword_rankings` 也只支持 US，其他站点会直接拒绝且不扣次数（schema 原文）。
- **`page`**（可选，integer）：从 1 起算，默认 1。
- **`pageSize`**（可选，integer）：1–50，默认 20。过大的上游结果可能被截断，并写入 `warnings`。`jiimore_find_asins_batch` 虽然接受这两个分页字段，但会忽略，每个规范化后的 ASIN 只返回一条摘要。
- 数组类入参会在网关侧去空格、去重；ASIN 还会转成大写。

---

## 一、关键词 / ABA / 出价 / 趋势工具（9 个）

| # | 工具名 | 官方中文名 / 标签 | 说明 | 输入参数 | 返回内容 |
|---|---|---|---|---|---|
| 1 | `jiimore_expand_aba_keywords_by_keyword` | ABA关键词拓展 / ABA | 用种子关键词拓展相关的 ABA 搜索词 | `request.keywords`（必填，1–20 个，每个最多 200 字符）；可选 `countryCode`、`page`、`pageSize` | 需求、点击、销量、转化、ABA 排名、关联度、竞争度、Top ASIN、广告预估等指标 |
| 2 | `jiimore_search_aba_keywords` | ASIN查ABA关键词 / ABA | 用 ASIN 查询相关的 ABA 关键词 | `request.asins`（必填，1–20 个 10 位 ASIN）；可选 `countryCode`、`page`、`pageSize` | 需求、点击、销量、转化、ABA 排名、关联度、竞争、Top ASIN、广告预估 |
| 3 | `jiimore_search_keywords_batch` | 批量关键词指标 / 关键词 | 批量查询市场搜索词的指标（上游 batch 接口，不是把多个词拼成一个词去搜） | `request.keywords`（必填，1–20 个，每个最多 200 字符）；可选 `countryCode`、`page`、`pageSize` | 相关关键词的需求、点击、销量、转化、ABA 排名，以及细分市场上下文指标 |
| 4 | `jiimore_search_keywords_by_asin` | ASIN查相关关键词 / 关键词 | 先根据 ASIN 解析出类目，再查询相关搜索词；解析不到类目就返回空页 | `request.asin`（必填，单个 10 位 ASIN）；可选 `countryCode`、`page`、`pageSize` | 需求、点击、销量、转化、ABA 排名，以及细分市场上下文 |
| 5 | `jiimore_search_keywords_by_keyword` | 关键词拓词 / 关键词 | 先根据种子关键词解析出相关类目，再拓展搜索词；解析不到类目就返回空页 | `request.keyword`（必填，单个词，1–200 字符）；可选 `countryCode`、`page`、`pageSize` | 需求、点击、销量、转化、ABA 排名，以及细分市场上下文 |
| 6 | `jiimore_get_keyword_rankings` | ASIN反查关键词排名 / 关键词 | 查询单个 ASIN 的反查关键词排名。上游虽能解析逗号分隔的多个 ASIN，但本 MCP 工具每次只接受 1 个。仅支持 US | `request.asin`（必填，单个 10 位 ASIN）；可选 `days`（仅 7 或 30，默认 7）、`countryCode`、`page`、`pageSize` | ABA Flow 指标、自然排名、首页 / Top 16 覆盖情况、流量分数 |
| 7 | `jiimore_get_niche_keywords` | 细分市场关键词 / 市场 | 根据细分市场 ID 获取这个市场里的关键词 | `request.nicheId`（必填，非空，最多 128 字符，来自工具 16 或 17）；可选 `countryCode`、`page`、`pageSize` | 关键词指标，以及 **90 天**的点击份额、搜索份额和搜索量 |
| 8 | `jiimore_get_keyword_bids` | 关键词建议出价（官网介绍页未列出；`tools/list` 英文名 Get Keyword Ad Bids） | 查询美国站关键词的亚马逊建议出价。始终返回 SB 出价；提供 `asin` 时同时返回该 ASIN 的 SP 出价。默认查 BROAD / PHRASE / EXACT 三种匹配。有 24 小时缓存；未命中缓存时受共享限流，部分结果可能是 `RATE_LIMITED` 或 `FAILED`（列在 `missing`，可稍后重试）。亚马逊没有出价的组合列在 `noBid`。完全没有出价的调用不扣次数。仅支持 US | `request.keywords`（必填，1–20 个，每个最多 200 字符，会转小写去重）；可选 `asin`（10 位，用于返回 SP 出价）、`matchTypes`（`BROAD` / `PHRASE` / `EXACT`，不区分大小写，默认三种都查）、`countryCode` | 按词、匹配类型给出的 SP / SB 建议价及区间；可能含 `missing`、`noBid` |
| 9 | `jiimore_get_keyword_trends` | 关键词趋势（官网介绍页未列出；`tools/list` 英文名 Get Keyword Trends） | 查询最多 5 个词的历史趋势：周度搜索量、点击、销量、转化率、ABA 排名、Top 3 ASIN 份额，以及 CPC 估计趋势（仅 US）。默认近 12 个月，区间最长 24 个月。每条序列单独报状态，一条失败不会挡住其他词。完全没有数据的调用不扣次数 | `request.keywords`（必填，1–5 个，每个最多 200 字符，会转小写去重）；可选 `startDate` / `endDate`（`yyyy-MM-dd`，`endDate` 默认今天，`startDate` 默认 `endDate` 往前 12 个月）、`countryCode` | 各词自己的周序列及状态 |

## 二、商品 / ASIN 工具（6 个）

| # | 工具名 | 官方中文名 / 标签 | 说明 | 输入参数 | 返回内容 |
|---|---|---|---|---|---|
| 10 | `jiimore_find_asins_batch` | 批量ASIN查询 / ASIN | 批量查询 ASIN，每个 ASIN 返回一条标准化的商品摘要。结果按请求顺序返回；没有市场数据的 ASIN 会进入 `warnings`。列表价字段可能不全 | `request.asins`（必填，1–20 个 10 位 ASIN）；可选 `countryCode`、`page`、`pageSize`（分页字段会被忽略） | 每个 ASIN 一条标准化商品摘要；**允许部分成功**，失败的会给出警告 |
| 11 | `jiimore_get_asin_details` | ASIN详情 / ASIN | 批量获取标准化的 ASIN 详情。本工具不请求评论、趋势序列或 ASIN 所属细分市场列表；未能解析的 ASIN 会反映在完整度和警告里 | `request.asins`（必填，1–20 个 10 位 ASIN）；可选 `countryCode` | 商品信息、周度点击和转化指标、Best Seller Rank、卖点摘要 |
| 12 | `jiimore_find_similar_asins` | 相似商品 / ABA | 根据 ABA 的周度相似度数据查找相似商品。列表价字段可能不全 | `request.asins`（必填，1–20 个 10 位 ASIN）；可选 `countryCode`、`page`、`pageSize` | 商品摘要、相似度字段、相关度字段 |
| 13 | `jiimore_find_aba_asins_by_keyword` | ABA商品搜索 / ABA | 用关键词查找 ABA 里的相关商品 | `request.keywords`（必填，1–20 个，每个最多 200 字符）；可选 `countryCode`、`page`、`pageSize` | 商品摘要、相似度 / 相关度、周度市场信号 |
| 14 | `jiimore_find_asins_by_keyword` | 关键词找商品 / 关键词 | 用关键词发现亚马逊商品；结果可能包含行为相关的商品，不只是精确匹配。先解析类目，解析不到就返回空页。列表价字段可能不全 | `request.keyword`（必填，单个词，1–200 字符）；可选 `countryCode`、`page`、`pageSize` | 商品摘要、周度点击 / 转化、市场排名信号 |
| 15 | `jiimore_find_same_niche_asins` | 同细分市场商品 / 市场 | 先获取目标 ASIN 所在的细分市场，再找同一细分市场里的相关商品；没有详情或没有细分市场时返回空页。列表价字段可能不全 | `request.asin`（必填，单个 10 位 ASIN）；可选 `countryCode`、`page`、`pageSize` | 商品摘要、周度市场指标 |

## 三、细分市场工具（4 个）

| # | 工具名 | 官方中文名 / 标签 | 说明 | 输入参数 | 返回内容 |
|---|---|---|---|---|---|
| 16 | `jiimore_find_niches_by_asin` | ASIN找细分市场 / 市场 | 根据 ASIN 解析出所属类目，再查找相关的细分市场；解析不到类目就返回空页 | `request.asin`（必填，单个 10 位 ASIN）；可选 `countryCode`、`page`、`pageSize` | 需求、转化、商品数、品牌数、竞争、价格、趋势等指标；返回的 `nicheId` 可供工具 7、18、19 使用（本次已实测可贯通） |
| 17 | `jiimore_find_niches_by_keyword` | 关键词找细分市场 / 市场 | 根据关键词解析出相关类目，再查找细分市场；解析不到类目就返回空页 | `request.keyword`（必填，单个词，1–200 字符）；可选 `countryCode`、`page`、`pageSize` | 需求、转化、商品数、品牌数、竞争、价格、趋势等指标 |
| 18 | `jiimore_get_niche_details` | 细分市场详情 / 市场 | 根据细分市场 ID 获取基础信息、PDR 评论洞察和市场潜力；这个市场里的商品和关键词要用别的工具单独查 | `request.nicheId`（必填，非空，最多 128 字符）；可选 `countryCode` | 基础信息、PDR 评论洞察、市场潜力 |
| 19 | `jiimore_get_niche_asins` | 细分市场商品 / 市场 | 根据细分市场 ID 获取这个市场里的商品 | `request.nicheId`（必填，非空，最多 128 字符）；可选 `countryCode`、`page`、`pageSize` | 商品摘要，以及 **90 天**的细分市场点击份额和点击量指标 |

> 不同工具的数据周期不一样：ASIN 类工具多是**周度**指标，关键词排名反查是 **7 天或 30 天**，细分市场的份额类指标是 **90 天**。比较之前要先核对口径。【文档写明：官方工具说明与 `tools/list` schema；[idcspy.net/32663](https://www.idcspy.net/32663.html) 的 FAQ 第 3 条】

---

## 总评

**极目 MCP 已经能作为 Helium 10 的只读对照源使用，值得纳入 ring banner 研究流程，但不能替代 H10，也不能直接拿返回的第一个细分市场做结构。**

对 ring banner 最有用的是：ASIN 反查（29 个词，爬取日期新至 2026-11-14）、ABA 拓词（3 个种子扩出 132 个词，带转化和 CPC）、建议出价（3 个词 × 3 种匹配的 SP/SB，这是 Helium 10 清单里没有的形态）、周度趋势，以及相似商品（107 个，相关性明显好于「按 ASIN 找细分市场」）。

需要警惕的数据口径问题：

- 按关键词先解析类目的 3 个工具（`find_niches_by_keyword`、`find_asins_by_keyword`、`search_keywords_by_keyword`）对长尾词 `ring banner` 会空返回；空结果不等于没有市场。
- `search_keywords_batch` 会把长尾词默默丢掉，且没有任何 warning。
- 按 ASIN 找关键词 / 细分市场会漂到花环、气球拱门、万圣节装饰等相邻大类，不能直接当 ring banner 的市场定义。
- 部分列表的 `currentPrice` 为 0；ASIN 详情里的价格是带长小数的计算值，不宜当当前标价。
- 同细分市场商品可能缺 `title` / `brand`，必须再查详情才能判断是不是竞品。
- 建议出价的 `fetchedAt` 年份是 2612，明显是时间戳异常，不能当新鲜度用。SB 三个词的建议价几乎一样，可能偏通用。
- 与 Helium 10 口径不同，数字不能混用。极目的增量主要是：点击 / 搜索转化率、按匹配类型的 SP+SB 建议出价、带点击和转化的周趋势、细分市场 PDR、中文译名，以及 ASIN 详情里的 FBA / 推荐费估计。

---

## 分工具实测表（2026-11-14 17:26–17:28，北京时间）

测试约定：每个工具只调用 1 次，失败不重试。站点全部为 US。细分市场 ID 只使用前面工具返回的结果；三个依赖 `nicheId` 的工具，用的是 `find_niches_by_asin` 返回的第一个 ID（`44c486fa2c6818c9cfb898a06cc3b29f`，标题是 **balloon arch kit / 气球拱门套件**，与核心产品相关度低）。19 次业务调用合计耗时约 46 秒，另加 `initialize` / `tools/list`。耗时和大小按响应记录四舍五入。

| # | 工具 | 结果 | 耗时 / 大小 | 返回摘要与样例 | 评价（用途 / 质量 / 与 H10） |
|---|---|---|---|---|---|
| 1 | `jiimore_expand_aba_keywords_by_keyword` | 成功 | 9.6s / 19.3KB | 入参 3 个种子词。扩出 132 个词，本页 20 条。前排：engagement party decorations（16,327 搜索 / 797 单 / 转化率 12.9%）、will you marry me sign、engagement decorations、proposal decorations 等。含竞争度、关联度、Top ASIN、广告花费估计、ACOS/CPA | **本次最有价值的拓词工具之一。** 词更贴近订婚场景。耗时最长但仍可接受。和 H10 ABA / Magnet 重叠，转化、关联等级、广告估计是增量。建议作为 ring banner 草案种子来源，再和 Cerebro、H10 ABA 对照 |
| 2 | `jiimore_search_aba_keywords` | 成功，条数少 | 1.0s / 2.1KB | 入参 `asins=[B0H6J55GBP]`。只返回 2 个 ABA 词：engagement decor（935 搜索，转化率 13.5%，关联等级 3）、bridal decor（201 搜索，转化率 17.3%） | 能出高相关词和广告估计，但条数远少于 ABA 按词拓展（132）和反查（29）。当作补充，不要当主词表 |
| 3 | `jiimore_search_keywords_batch` | 成功，缺 1 个词 | 1.2s / 1.3KB | 入参 3 个词，只回来 2 个：`will you marry me banner`、`diamond ring balloon`。`ring banner` 未出现，也没有 warning | 适合一次拉多个已知词的量、转化、CPC。长尾词可能被默默丢掉，要用趋势或出价工具补看。和 H10 `analyze_keywords` 类似，转化口径是极目的长处 |
| 4 | `jiimore_search_keywords_by_asin` | 成功，相关度差 | 1.5s / 11.1KB | 入参 `asin=B0H6J55GBP`。1,373 个词，本页 20 条。前排是 balloon arch kit（32,463）、halloween garland、balloon arch、star garland、toran 等，本页没有 ring / engagement banner | 体量大，但按类目扩展，噪声很高，不适合直接当广告词表。应用反查或 ABA 拓词做主词表，这个只作补充并人工过滤。H10 反查通常更贴商品 |
| 5 | `jiimore_search_keywords_by_keyword` | 成功但空结果 | 2.5s / 222B | 入参 `keyword=ring banner`。0 条。先解析类目 | 对长尾精确词很脆。拓词请改用 ABA 拓展。空结果不等于没有市场 |
| 6 | `jiimore_get_keyword_rankings` | 成功 | 1.7s / 15.2KB | 入参 30 天窗口。29 个反查词，本页 20 条。样例：engagement decorations 搜索量 3,621、转化率 13.7%、自然排名 82，爬取日 2026-11-14；engagement banner 搜索量 850、转化率 14.6%、自然排名 57，首页 ASIN 数 1，爬取日 2026-11-13。本页没有 `ring banner` 本身（共 2 页，未翻页） | **非常适合 ring banner 词表。** 词更贴近订婚装饰，新鲜度高。和 H10 Cerebro 重叠，但带点击转化率和中文译名。值得与 Cerebro 对照，口径不要混用 |
| 7 | `jiimore_get_niche_keywords` | 成功，但市场不对 | 2.9s / 15.2KB | 入参是第一个 niche（气球拱门套件）。20 个词，全是 balloon arch kit / stand 等，带 90 天点击份额和搜索份额 | 市场关键词画像完整，但本次不能当 ring banner 词表。必须先人工挑 niche 再查 |
| 8 | `jiimore_get_keyword_bids` | 成功（新工具） | 5.7s / 3.3KB | 入参 3 个词 + `asin=B0H6J55GBP` + 三种匹配。SP 和 SB 各 9 条，无 noBid。SP 样例：ring banner 广泛/词组建议 $0.68（0.51–0.85），精确 $0.60；will you marry me banner 三种匹配都是 $0.60；diamond ring balloon 广泛 $0.63，词组/精确 $0.65。SB 三个词几乎都是 $0.96 / 精确 $0.93。`fetchedAt=2612-01-19T15:16:12Z`，年份异常 | **对出价草案很有用，且 H10 清单里没有同形态的「按匹配类型 + ASIN 的亚马逊建议出价」。** SB 三个词几乎一样，可能偏通用；`fetchedAt` 不能当新鲜度。建议和 H10 CPC 历史、目标 ACOS 45–50%、CPA $8 一起用，不要单看建议价 |
| 9 | `jiimore_get_keyword_trends` | 成功（新工具） | 1.8s / 47.9KB | 默认近 12 个月。will you marry me banner、diamond ring balloon 各 48 周，到 2026-10-25；ring banner 只有 12 周，且只到 2026-08-23，缺近两个多月。ring banner 7 天搜索量约 166–223；will you marry me banner 最近一周 340 搜索 / 5 单，转化率约 7%；diamond ring balloon 最近一周 46 搜索 / 4 单，转化率约 26%，CPC 高（1.08–1.80） | **H10 有搜索量 / CPC / 销量历史，但极目把点击、转化、ABA 排名和 CPC 放在同一条周序列里，是增量。** ring banner 序列不完整，做季节判断要谨慎 |
| 10 | `jiimore_find_asins_batch` | 成功 | 1.2s / 778B | 入参 `asins=[B0H6J55GBP]`。1 条摘要：品牌 DOCZEN，评分 5.0，9 条评论；`currentPrice=0.0`（schema 已警告列表价可能不全） | 适合批量扫摘要，单 ASIN 不如详情工具。价格字段不可靠。和 H10 商品摘要重叠，深度更浅 |
| 11 | `jiimore_get_asin_details` | 成功（COMPLETE） | 5.3s / 1.5KB | 标题与产品一致（Doczen 80-inch ring banner）。`currentPrice` 12.18 USD（用户记录售价 $13.99，需核对）；FBA 费 3.43、推荐费 1.87（360 天口径）；7 天点击 142、30 天 1,381；点击转化率 14.1%，综合 21.1%；BSR Garlands #739；有卖点摘要 | **很有用。** 极目特色是点击转化率和费用估计，H10 的 Listing / 价格 / BSR 历史更完整。价格是带长小数的计算值，不宜当当前标价 |
| 12 | `jiimore_find_similar_asins` | 成功 | 1.7s / 6.5KB | 入参 `asins=[B0H6J55GBP]`。107 个相似商品，本页 10 条。带 similarityScore 和「高相似 / 中相似」。前排：LMGERBUD 戒指气球（分 100，转化率 16.9%）、戒指纸屑、Just Engaged Banner（JCWTO，分 18）等 | **相关度明显好于按 ASIN 找细分市场。** 适合建 ASIN 定投观察池，但仍混有气球和横幅，要人工分。和 H10 竞品 / 定投建议重叠，相似度分数是增量 |
| 13 | `jiimore_find_aba_asins_by_keyword` | 成功 | 1.3s / 2.7KB | 入参只传了 `ring banner`，仍返回 4 个商品。例如 ATILOY 戒指横幅（B0DFYF52J3，约 $5.79，评分 4.4） | **对找直接竞品有用**，即使类目解析类工具是空的，ABA 商品搜索仍能出戒指横幅。和 H10 竞品搜索互补 |
| 14 | `jiimore_find_asins_by_keyword` | 成功但空结果 | 1.0s / 221B | 入参 `keyword=ring banner`。0 条。同样先解析类目 | 长尾词入口失败。找商品改用 ABA 商品搜索或 H10 搜索 |
| 15 | `jiimore_find_same_niche_asins` | 成功，字段不全 | 1.5s / 2.9KB | 入参 `asin=B0H6J55GBP`。480 个同细分商品，本页 10 条。有 ASIN、价格、评分、评论、点击和转化；**全部没有 title / brand**。第一条 B0DPJHKBDH，转化率 13.6% | 量很大，但缺标题，必须再查详情才能判断是不是竞品。因为依赖的细分市场会漂移，名单噪声也大。H10 竞品工具更直接 |
| 16 | `jiimore_find_niches_by_asin` | 成功 | 1.7s / 21.2KB | 入参 `asin=B0H6J55GBP`。60 个细分市场，本页 20 条。第一条是 balloon arch kit，需求 1,386,738，7 天搜索量 118,127，38 个商品 / 32 个品牌。前 20 条全是花环、气球拱门、节日装饰，没有 engagement / ring banner 市场 | 能发现 ASIN 被归到哪些 OE 细分市场，但**相关度差**，不能当 ring banner 的市场定义。H10 也有选品/类目工具，极目的需求、转化、集中度指标可对照，必须人工筛选 |
| 17 | `jiimore_find_niches_by_keyword` | 成功但空结果 | 1.3s / 221B | 入参 `keyword=ring banner`。`totalRows=0`。文档写明：先按词解析类目，解析不到就空页 | 对长尾精确词很脆。研究 ring banner 不要以它为入口，改走 ASIN 或 ABA。H10 Magnet 对同一词通常仍能出词表 |
| 18 | `jiimore_get_niche_details` | 成功，但市场选错了核心品 | 1.3s / 15.3KB | 用的是第一个 niche：balloon arch kit。有基本面、95 周趋势（2025-01-12 至 2026-11-01）、潜力信号（商品数、价格、集中度、成功新品）、正负 PDR 评论（易组装、掉气、颜色不符等），退货率 5.6%，广告商品占比 97% | 工具本身信息密度高，PDR 和市场潜力是 H10 少见的增量。**这次样本是气球拱门，不能用来写 ring banner 的市场结论。** 以后必须先人工挑 niche 再查详情 |
| 19 | `jiimore_get_niche_asins` | 成功，同上，市场不对 | 1.7s / 6.8KB | 该 niche 38 个商品，本页 10 条，全是拱门支架 / 打气筒等。多条 `currentPrice=0`。有 90 天点击份额 | 看市场结构有用，但本次名单与 ring banner 无关。价格字段经常为 0 |

### 对 ring banner 研究的实操建议

1. 优先调用：`get_keyword_rankings`、`expand_aba_keywords_by_keyword`、`get_keyword_bids`、`get_keyword_trends`、`get_asin_details`、`find_similar_asins`、`find_aba_asins_by_keyword`、`search_keywords_batch`。
2. 不要把 `find_niches_by_keyword` / `find_asins_by_keyword` / `search_keywords_by_keyword` 当作长尾词的第一入口；空结果不等于没有市场。
3. `find_niches_by_asin`、`search_keywords_by_asin` 的结果要当「类目相邻池」看，先人工去掉花环 / 拱门 / 节日装饰，再考虑是否调用 niche 详情。
4. 建议出价可写入草案，但要和目标 ACOS 45–50%、CPA $8，以及 H10 CPC 历史一起算；不要使用异常的 `fetchedAt`。
5. 极目仍不能创建或修改广告、Listing。写入路径还是 Helium 10（须按批次确认）等。

---

## 接入方式

1. **在控制台生成 Key。** 官方页面写的流程是“复制专属连接与密钥 → 粘贴至AI客户端，一键启用”。管理页面是 [https://www.jiimore.com/data/mcp](https://www.jiimore.com/data/mcp)，要登录才能看。从搜索摘要看，这个页面会显示“账户剩余调用 X 次 / 总额 X 次”，还有一张 Key 列表，列名是名称、Key、创建日期、最新使用日期、操作。【文档写明：官方页面脚本；管理页内容来自搜索摘要，**本次没有登录管理页核实**】
2. **用 Key 连接 MCP 端点。** 端点是 `https://www.jiimore.com/mcp`。本次实测有效请求头是 **`x-api-key`**；`Authorization: Bearer` 返回 `invalid_token`，`X-Mcp-Key` 无效。协议版本 **`2025-11-25`**，`initialize` 之后必须带 `MCP-Protocol-Version`。传输为 HTTP JSON-RPC（streamable HTTP）。服务端声明为 `jiimore-mcp-server 1.0.0`。第三方文章列出的可用客户端有：Codex、Coze、Chatbox、Claude、OpenClaw、ChatGPT、WorkBuddy 等。【鉴权与协议为本次实测；客户端名单来自第三方：[idcspy.com/168675](https://www.idcspy.com/168675.html)；官网只写了“适配市面主流AI客户端”】
3. **用自然语言提问。** AI 会自己选择和组合工具，不用写代码。【文档写明：官方页面】
- Key 只放在请求头里，不要写进仓库、报告或聊天记录。

## 价格（第三方，官网定价页本次没能核实）

以下来自 [idcspy.net/32233](https://www.idcspy.net/32233.html)，是 2026-09-21 发布的中秋促销文章，**官网现价仍未证实**：

| MCP 套餐 | 活动价 | 用优惠码 idcspy 再打 9 折 |
|---|---|---|
| MCP 基础版 | 99 元/月 | 89.10 元/月 |
| MCP 进阶版 | 299 元/月 | 269.10 元/月 |
| MCP 专业版 | 599 元/月 | 539.10 元/月 |
| MCP 年度进阶版 | 999 元/年 | 899.10 元/年 |
| MCP 年度专业版 | 1,999 元/年 | 1,799.10 元/年 |
| MCP 年度旗舰版 | 2,999 元/年 | 2,699.10 元/年 |

- 同一篇文章说，网页端会员（月度高级会员 399 元/月等）和 MCP 套餐是**两套不同的产品**。
- 每个套餐包含多少次调用：**未证实**。管理页只显示“剩余调用次数”。本次 19 次业务调用的响应里，也没有返回消耗点数或剩余额度。
- 上面这些价格是促销期的价格，现在的价格可能不一样：**未证实**。

## 和本项目的关系（ring banner，B0H6J55GBP）

- **只能读数据。** 19 个工具全部是查询类工具（`readOnlyHint` 均为 true），**不能建活动、不能改出价、不能写入亚马逊广告账户或 Listing**。【实测：`tools/list` 与 19 次只读调用】
- **可以用来重新给 ring banner 的关键词和 ASIN 分类，但不要套用气球品数据，也不要直接用第一个细分市场。** 现有的 Helium 10 研究是按 Diamond Ring Balloons 做的；本次按 ASIN 找出的第一个细分市场也是「气球拱门套件」。更稳妥的做法是：
  - 用 `jiimore_get_keyword_rankings` 和 `jiimore_expand_aba_keywords_by_keyword` 做主词表，再用 `jiimore_search_aba_keywords`、`jiimore_search_keywords_batch` 补充。
  - 用 `jiimore_get_keyword_bids` 和 `jiimore_get_keyword_trends` 做出价草案和季节性对照，并与目标 ACOS 45–50%、最大 CPA $8、Helium 10 CPC 历史一起算。
  - 用 `jiimore_find_similar_asins` 和 `jiimore_find_aba_asins_by_keyword` 建竞品 / 定投观察池；`jiimore_find_same_niche_asins` 缺标题、且会随错误的 niche 漂移，只能作辅证。
  - 用 `jiimore_get_asin_details` 看转化率和费用估计；`jiimore_find_asins_batch` 可分批扫摘要，但列表价常为 0。
- **不要把按词解析类目的 3 个工具当作长尾词第一入口。** 对 `ring banner` 它们会空返回。
- **要注意调用次数。** 每次最多 20 个（趋势工具最多 5 个），344 个 ASIN 至少要分 18 批，会占用调用次数。本次每个工具只调用了 1 次，没有再测额度。

---

## 来源

- 本次实测（2026-11-14 17:26–17:28，北京时间）：HTTP JSON-RPC streamable HTTP，端点 `https://www.jiimore.com/mcp`，鉴权头 `x-api-key`，协议 `2025-11-25`；`initialize` + `tools/list`（19 个工具及完整 input schema）+ 每个工具 1 次 `tools/call`。输入为 ring banner 关键词与 ASIN `B0H6J55GBP`、US 站
- 官方介绍页（仍列 17 个工具）：https://www.jiimore.com/ai-mcp
- 官方页面脚本（原先 17 个工具的中文名和说明原文）：https://www.jiimore.com/assets/mcp-A9WazGJ6.js
- MCP 管理页（要登录，本次没有打开核实额度）：https://www.jiimore.com/data/mcp
- MCP 端点：https://www.jiimore.com/mcp
- 科灯导航《极目数据MCP能查什么》：https://www.idcspy.net/32663.html
- 科灯导航 中秋价格文章（价格 **未证实**）：https://www.idcspy.net/32233.html
- 美国主机侦探《亚马逊运营MCP工具对比》：https://www.idcspy.com/168675.html
