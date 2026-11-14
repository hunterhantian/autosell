# Helium 10 MCP 工具参考：本账号 197 个工具

> 整理时间：2026-11-14（北京时间）。只用代码抓取（WebSearch、WebFetch、curl 同类的只读接口），没有用可视化浏览器，也没有调用任何 Helium 10 写入 / 创建类工具，也没有向 GitHub 推送。
> 主要来源有两类：一是官方介绍与帮助中心（端点、鉴权、套餐、配额、广告写入限制）；二是本账号已连接的 MCP 命名空间 `user-helium10`，用只读的工具目录接口核过名称、说明和参数 schema。官方帮助中心写的是“200 个工具、28+ 个功能类”，本账号实时清单是 **197** 个，不以官方整数覆盖实测数字。
> 几乎所有工具都有 `session_id`（第一次调用后必须原样回传，官方 schema 写明这是会话串联用的内部参数）和 `context`（一句话说明调用目的，只用于分析，不影响行为）。下面“关键参数”列只写业务参数，这两项不再逐行重复。业务参数以本账号工具 schema 为准；schema 或官方文档没有给出的字段名，写作“未公开”。

## 工具数量

- **本账号已连接工具数：197。** 状态接口（2026-11-14，北京时间）显示：`user-helium10` 已连接，账号 `default`，传输方式 HTTP，工具数 197。
- **官方帮助中心写的是 200 个工具、28+ 个功能类**（[Getting Started with Helium 10 MCP](https://kb.helium10.com/hc/en-us/articles/51580564409883-Getting-Started-with-Helium-10-MCP)，页面标注更新时间为 November 13, 2026 22:33；来源时区未标明，这里按原文照录）。
- **官方产品页另有一处写“40+ MCP tools”**（[helium10.com/tools/mcp](https://www.helium10.com/tools/mcp/)）。与帮助中心的 200、本账号的 197 不一致，这里并列写出，不自行调和。
- 官方帮助中心里，个别分类自己报的数量和列出的工具名对不上（例如 Advertising 写 24 个但正文列举更少，Amazon P&L 写 8 个但列表只见 7 个，Follow-Up 写 6 个但列表只见 4 个）。下面按**本账号 197 个实到工具**重新分类，不以官方分类计数为权威。
- 官方工具名在本账号清单中未见的，记为“未在本账号工具清单中见到”，不当成“官方不存在”。目前对照到的有：`create_amazon_cogs`（本账号对应的是可做 upsert 的 `edit_amazon_cogs`）、`get_current_user_info`、`get_market_overall`（本账号有 `get_market_trends`）。

| 分类 | 数量 | 读 | 写 |
|---|---:|---:|---:|
| 关键词研究 | 18 | 18 | 0 |
| 关键词跟踪（Keyword Tracker） | 24 | 8 | 16 |
| 商品 / ASIN 研究 | 12 | 12 | 0 |
| Listing Analyzer（列表分析） | 5 | 5 | 0 |
| Listing Builder（列表生成） | 8 | 4 | 4 |
| My List（我的产品） | 4 | 2 | 2 |
| 广告 Ads / PPC | 22 | 20 | 2 |
| 亚马逊利润与成本 | 17 | 12 | 5 |
| 亚马逊流量与库存 | 5 | 5 | 0 |
| Search Query Performance | 1 | 1 | 0 |
| 预警与账户健康 | 12 | 11 | 1 |
| Follow-Up | 6 | 6 | 0 |
| Market Tracker | 5 | 5 | 0 |
| 账号与用量 | 5 | 5 | 0 |
| 亚马逊红人（Influencers） | 10 | 5 | 5 |
| Walmart 利润、成本与库存 | 18 | 13 | 5 |
| TikTok 利润、成本与库存 | 18 | 13 | 5 |
| TikTok 商品研究 | 6 | 6 | 0 |
| 其他 | 1 | 1 | 0 |
| **合计** | **197** | **152** | **45** |

> 分类说明：“写”指会在 Helium 10 或亚马逊侧创建、修改或删除数据。Listing Builder 里的 `generate_listing` / `rewrite_listing_section` / `request_image_upload_url` 先写的是 Helium 10 侧草稿或上传；只有 `sync_listing_to_amazon` 会把文案推到 Seller Central。广告规则工具 `manage_ads_rule` 里，草稿的打开 / 保存 / 丢弃不改线上规则，`draft_commit` 和 `rule_delete` 会改线上，仍整工具计为写。

---

## 一、关键词研究（18 个，全部为读）

对应官方 Keyword Research，以及本账号多出来的沃尔玛、历史序列、ABA、Google Trends 等工具。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 1 | `analyze_keywords` | 综合分析亚马逊关键词（搜索量、竞争、相关度等，具体指标见返回） | 读 | 见本账号工具 schema；官方未在介绍页单独展开字段名的部分记为未公开 |
| 2 | `analyze_walmart_keywords` | 综合分析沃尔玛关键词 | 读 | 见本账号工具 schema；未在介绍页展开的字段名记为未公开 |
| 3 | `get_keywords_by_asin` | 用 ASIN 反查关键词（Cerebro 一类） | 读 | 见本账号工具 schema（业务输入通常含站点与 ASIN）；未展开字段名记为未公开 |
| 4 | `get_keywords_by_keyword` | 用种子关键词拓词（Magnet 一类） | 读 | 见本账号工具 schema（业务输入通常含站点与关键词）；未展开字段名记为未公开 |
| 5 | `get_keywords_by_item_id` | 用沃尔玛 Item ID 反查关键词 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 6 | `compare_asin_keywords` | 比较多个 ASIN 覆盖的关键词 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 7 | `compare_item_keywords` | 比较多个沃尔玛 Item 的关键词 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 8 | `get_keywords_search_volume_history` | 查询关键词搜索量历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 9 | `get_keywords_cpc_history` | 查询关键词 CPC 历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 10 | `get_keywords_sales_history` | 查询关键词销量历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 11 | `get_keywords_search_frequency_rank_history` | 查询搜索频率排名（SFR）历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 12 | `get_walmart_keywords_by_keyword` | 用种子词拓展沃尔玛关键词 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 13 | `get_walmart_search_trend_score_history` | 查询沃尔玛搜索趋势分历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 14 | `search_amazon_keywords` | 搜索亚马逊关键词 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 15 | `search_amazon_brand_analytics` | 查询 Amazon Brand Analytics（ABA）相关数据 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 16 | `get_google_trends_history` | 查询 Google Trends 历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 17 | `get_top_keywords` | 获取头部 / 热门关键词 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 18 | `check_asin_keyword_index` | 检查某个 ASIN 是否被指定关键词收录 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 二、关键词跟踪 Keyword Tracker（24 个，其中 16 个为写）

用于 Helium 10 Keyword Tracker：跟踪词、竞品词、备注、标签、文件夹和排名历史。写入会改跟踪列表，不是改广告活动。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 19 | `list_tracked_products` | 列出已跟踪的产品 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 20 | `list_tracked_keywords` | 列出已跟踪的关键词 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 21 | `list_tracked_competitor_keywords` | 列出已跟踪的竞品关键词 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 22 | `get_keywords_rank_history` | 查询已跟踪关键词的排名历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 23 | `get_keywords_new_suggestion` | 获取跟踪词的新建议 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 24 | `get_sbh_sbv_rank_history` | 查询 SBH / SBV 相关排名历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 25 | `list_keyword_folders` | 列出关键词文件夹 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 26 | `list_folder_keywords` | 列出某文件夹中的关键词 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 27 | `add_untracked_product_keywords` | 为产品添加尚未跟踪的关键词 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 28 | `add_keyword_competitors` | 添加关键词竞品 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 29 | `remove_keyword_competitors` | 移除关键词竞品 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 30 | `add_keyword_note` | 为关键词添加备注 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 31 | `update_keyword_note` | 更新关键词备注 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 32 | `remove_keyword_note` | 删除关键词备注 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 33 | `delete_tracked_keywords` | 删除已跟踪关键词 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 34 | `delete_tracked_product` | 删除已跟踪产品 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 35 | `link_keyword_tag` | 给关键词打上已有标签 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 36 | `unlink_keyword_tag` | 取消关键词与标签的关联 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 37 | `add_keywords_to_folder` | 把关键词加入文件夹 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 38 | `create_keyword_folder` | 新建关键词文件夹 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 39 | `delete_keyword_folder` | 删除关键词文件夹 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 40 | `delete_keywords_from_folder` | 从文件夹移除关键词 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 41 | `move_keywords_between_folders` | 在文件夹之间移动关键词 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 42 | `rename_keyword_folder` | 重命名关键词文件夹 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |

## 三、商品 / ASIN 研究（12 个，全部为读）

对应官方 Product Research（Black Box、竞品、历史价格 / BSR / 评论等），并计入本账号有、官方分类列表未必写全的 `get_bought_together_history`。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 43 | `search_products` | 按条件搜索在售商品 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 44 | `search_inactive_products` | 搜索已下架 / 非活跃商品 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 45 | `search_niche_by_keyword` | 用关键词搜索细分市场 / 利基 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 46 | `search_competitors_by_asin` | 用 ASIN 查找竞品 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 47 | `search_competitors_by_item_id` | 用沃尔玛 Item ID 查找竞品 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 48 | `search_product_targets_by_asin` | 用 ASIN 查找可作为投放对象的商品 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 49 | `get_competitor_overview` | 获取竞品概览 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 50 | `get_asin_bsr_history` | 查询 ASIN 的 BSR 历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 51 | `get_asin_price_history` | 查询 ASIN 的价格历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 52 | `get_asin_reviews_history` | 查询 ASIN 的评论数量等历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 53 | `search_browse_nodes` | 搜索亚马逊类目节点 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 54 | `get_bought_together_history` | 查询经常一起购买的历史数据 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 四、Listing Analyzer（5 个，全部为读）

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 55 | `get_listing_details` | 获取 Listing 详情 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 56 | `get_listing_score` | 获取 Listing 评分 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 57 | `compare_listings` | 比较多个 Listing | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 58 | `get_asin_review_analysis` | 分析单个 ASIN 的评论 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 59 | `get_category_review_analysis` | 分析类目层面的评论 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 五、Listing Builder（8 个，其中 4 个为写）

官方帮助中心有一处 FAQ 写 MCP 不能修改 Listing 内容；但本账号工具清单和该工具自己的说明里，都有会把文案推到 Seller Central 的 `sync_listing_to_amazon`。以本账号 schema 和该工具说明为准，并标出官方 FAQ 的矛盾。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 60 | `retrieve_listing_by_asin` | 按 ASIN 取回当前 Listing，供后续生成 / 改写使用 | 读 | 见本账号工具 schema（业务输入通常含 marketplace、asin）；未展开字段名记为未公开 |
| 61 | `analyze_product_images` | 分析产品图片，供 Listing 生成使用 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 62 | `get_keyword_performance_with_competitors` | 结合竞品查看关键词表现，供 Listing 用词参考 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 63 | `find_keywords_with_multi_source` | 从多个来源合并找词，供 Listing 用词 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 64 | `request_image_upload_url` | 申请图片上传地址，把图片传到 Helium 10 Listing Builder（不是直接改亚马逊主图） | 写（H10） | 见本账号工具 schema；未展开字段名记为未公开 |
| 65 | `generate_listing` | 在 Helium 10 Listing Builder 中生成 Listing 文案，不会自动发布到亚马逊 | 写（H10） | 见本账号工具 schema；未展开字段名记为未公开 |
| 66 | `rewrite_listing_section` | 改写 Listing 的某一段，仍留在 Helium 10，不会自动发布 | 写（H10） | 见本账号工具 schema；未展开字段名记为未公开 |
| 67 | `sync_listing_to_amazon` | **破坏性写入**：经 SP-API 把 title / item_highlight / bullets / description 推到 Seller Central，并回报 `sync_status`。必须先得到用户明确的“发布”同意；只审阅或认可文案不算同意。至少要带 title、item_highlight、bullets、description 中的一项。Amazon 已接受的内容不要自动重试，否则可能写两次。bullets 是整字段替换，少传会删掉未包含的原有要点。title 与 item_highlight 要成对提交。 | 写（亚马逊） | 必填：`marketplace`（US/CA/MX/DE/ES/IT/FR/UK/IN/NL/AU/JP/BE/BR）、`asin`、`sku`。可选：`request_id`（来自 generate / rewrite）、`title`（最长 200）、`item_highlight`（最长 125）、`bullets`（最多 10 条、每条最长 700）、`description`（最长 4000） |

## 六、My List（4 个，其中 2 个为写）

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 68 | `list_saved_products` | 列出已保存到 My List 的产品 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 69 | `list_my_products` | 列出我的产品 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 70 | `add_saved_product` | 把产品加入 My List | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 71 | `delete_saved_product` | 从 My List 删除产品 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |

## 七、广告 Ads / PPC（22 个，其中 2 个为写）

广告查询、活动管理、变更日志、规则和标签、预算使用率。官方帮助中心广告类写 24 个，本账号实到 22 个。标签只能读，不能在 MCP 里创建或打标签，需到 Helium 10 Ads 界面操作（官方说明）。

活动管理是三步：`get_ads_campaign_mgmt_platforms` → `get_ads_campaign_mgmt_schema` → `execute_ads_campaign_mgmt_actions`。数据查询类似：platforms → list / schema / materials → `execute_ads_query`。规则是四步：platforms → schema → 查询材料 / 读规则 → `manage_ads_rule`。

官方《Using Ads Write Capabilities》写明：每次确认批次最多 **20** 个 action，每类 action 最多 **10** 个 item，必须先明确确认。本账号 `execute_ads_campaign_mgmt_actions` 的 schema 则写上限以 `get_ads_campaign_mgmt_platforms` 返回的 `maxActionsPerRequest` 为准。同一篇文章还写不能删除活动、只能暂停；但该写入工具的说明里出现了 create / update / delete。两处官方表述不一致，这里并列写出，不以其中一处覆盖另一处。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 72 | `get_ads_query_platforms` | 列出可查询的广告平台 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 73 | `get_ads_query_list` | 列出可执行的广告查询 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 74 | `get_ads_query_schema` | 获取某条广告查询的请求 schema | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 75 | `get_ads_query_materials` | 获取查询所需材料（含 profile 等 ID） | 读 | 见本账号工具 schema（说明中常见 `materialType` / `level=profile`）；未展开字段名记为未公开 |
| 76 | `execute_ads_query` | 执行广告数据查询 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 77 | `get_ads_campaign_mgmt_platforms` | 列出可管理的广告平台，以及允许的 actionType、`maxActionsPerRequest` | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 78 | `get_ads_campaign_mgmt_schema` | 获取某一 actionType 的请求体形状 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 79 | `execute_ads_campaign_mgmt_actions` | **终端写入**：向单个 platform + profileId 提交一条或多条广告管理动作（工具说明写 create / update / delete campaigns、ad groups、keywords 等）。调用前必须把 platform、profileId 和全部 actions 展示给用户并得到明确确认，不能靠推断，也不能打包确认。 | 写 | 必填：`platform`、`profile_id`、`actions[]`（每项必填 `type`、`request_body`）。条数不得超过该平台的 `maxActionsPerRequest`。`request_body` 的精确形状由 schema 决定，可能是对象或对象数组，数组上限见该 schema 的 `maxRequestBodyItems` |
| 80 | `get_ads_changelog_platforms` | 列出变更日志可用的平台 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 81 | `get_ads_changelog_schema` | 获取变更日志查询 schema | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 82 | `execute_ads_changelog_query` | 执行广告变更日志查询 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 83 | `get_ads_rule_platforms` | 列出规则可用的平台和 rule_type | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 84 | `get_ads_rule_schema` | 获取某规则类型的字段目录和草稿骨架 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 85 | `get_ads_rule` | 读取单条广告规则 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 86 | `list_ads_rules` | 列出广告规则 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 87 | `manage_ads_rule` | **写入**：用 `action` 选择操作。`draft_open` / `draft_save` / `draft_discard` 只动草稿；`draft_commit` 把草稿变成线上规则（`automated=true` 会改出价、日预算或投放位置等，`automated=false` 只建议不改线上）；`rule_delete` 永久删除线上规则。后两步必须先确认。带 Tactic 的 BID 规则不能通过 MCP 编辑（`UNSUPPORTED.RULE_HAS_TACTIC`），要到 Helium 10 Ads Rules & Automation 页面处理。 | 写 | 必填：`action`。按 action 还需要：`platform`、`rule_type`（目前 schema 列举 BID、BUDGET、PLACEMENT、POSITIVE、NEGATIVE、DAYPARTING_SCHEDULE、BUDGET_SCHEDULE）、`profile_id`、`mode`（create / edit）、`source_rule_id`、`draft_id`、`rule_id`、`draft`、`expected_revision`。带 `unitHints` 的规则类型里，ACOS 12% 要写成 0.12；排期类规则用原始值（1% 写成 `'1'`） |
| 88 | `get_ads_tags_schema` | 获取广告标签 schema | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 89 | `list_ads_tags` | 列出广告标签 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 90 | `get_ads_tag` | 读取单个广告标签 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 91 | `get_ads_tags_for_entities` | 查询对象上已有的广告标签（MCP 不能创建或打标签） | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 92 | `get_amazon_ads_average_budget_utilization` | 查询亚马逊广告平均预算使用率 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 93 | `get_amazon_ads_current_budget_utilization` | 查询亚马逊广告当前预算使用率 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 八、亚马逊利润与成本（17 个，其中 5 个为写）

账户级 / 产品级 P&L 各 4 个，另加小时销退 1 个，以及成本 8 个。官方列表里的 `create_amazon_cogs` 未在本账号工具清单中见到；本账号是 `edit_amazon_cogs`（upsert）。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 94 | `get_account_profit_and_loss_summary` | 账户级 P&L 汇总 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 95 | `get_account_profit_and_loss_summary_series` | 账户级 P&L 汇总统序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 96 | `get_account_profit_and_loss_breakdown` | 账户级 P&L 拆分 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 97 | `get_account_profit_and_loss_breakdown_series` | 账户级 P&L 拆分时间序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 98 | `get_product_profit_and_loss_summary` | 产品级 P&L 汇总 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 99 | `get_product_profit_and_loss_summary_series` | 产品级 P&L 汇总统序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 100 | `get_product_profit_and_loss_breakdown` | 产品级 P&L 拆分 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 101 | `get_product_profit_and_loss_breakdown_series` | 产品级 P&L 拆分时间序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 102 | `get_amazon_hourly_sales_and_refunds` | 查询亚马逊按小时的销售和退款 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 103 | `list_amazon_cogs` | 列出亚马逊 COGS | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 104 | `edit_amazon_cogs` | 新增或更新亚马逊 COGS（upsert） | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 105 | `delete_amazon_cogs` | 删除亚马逊 COGS | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 106 | `list_amazon_indirect_cost_settings` | 列出亚马逊间接费用设置 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 107 | `list_amazon_indirect_costs` | 列出亚马逊间接费用 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 108 | `create_amazon_indirect_cost` | 创建亚马逊间接费用 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 109 | `edit_amazon_indirect_cost` | 编辑亚马逊间接费用 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 110 | `delete_amazon_indirect_costs` | 删除亚马逊间接费用 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |

## 九、亚马逊流量与库存（5 个，全部为读）

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 111 | `get_amazon_seller_traffic` | 查询卖家账户流量 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 112 | `get_amazon_product_traffic` | 查询产品流量 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 113 | `get_inventory_values` | 查询库存价值 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 114 | `get_fba_inventory_health_status` | 查询 FBA 库存健康状况 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 115 | `get_sales_velocity` | 查询销售速率 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 十、Search Query Performance（1 个，读）

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 116 | `get_search_query_performance` | 查询 Search Query Performance（搜索词表现） | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 十一、预警与账户健康（12 个，其中 1 个为写）

官方 Alerts 类写 9 个；本账号把监控、Buy Box、费用促销、B2B 报价、账户健康、尺码层建议等一并归在这里，共 12 个。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 117 | `get_monitoring_status` | 查询产品监控状态 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 118 | `edit_product_monitoring` | 修改产品监控设置 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 119 | `get_buybox_summary` | 获取 Buy Box 汇总 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 120 | `search_buybox_history` | 查询 Buy Box 历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 121 | `get_change_rollup` | 获取变更汇总 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 122 | `get_product_live_status` | 查询产品线上状态 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 123 | `get_seller_feedback` | 查询卖家反馈 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 124 | `get_seller_account_health` | 查询卖家账户健康 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 125 | `get_size_tier_suggestions` | 获取尺码层 / size tier 建议 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 126 | `search_alert_events` | 搜索预警事件 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 127 | `search_b2b_offer_change_notifications` | 搜索 B2B 报价变更通知 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 128 | `search_fee_promotion_notifications` | 搜索费用促销通知 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 十二、Follow-Up（6 个，全部为读）

官方帮助中心 Follow-Up 写 6 个但正文只列 4 个；本账号实到下面 6 个，且都是只读，不能在 MCP 里创建或改自动化发信规则。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 129 | `get_followup_account_status` | 查询 Follow-Up 账户状态 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 130 | `list_followup_automations` | 列出 Follow-Up 自动化 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 131 | `get_followup_automation` | 读取单条 Follow-Up 自动化 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 132 | `get_followup_automation_performance` | 查询自动化表现 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 133 | `get_followup_email_stats` | 查询 Follow-Up 邮件统计 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 134 | `get_followup_review_request_stats` | 查询索评统计 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 十三、Market Tracker（5 个，全部为读）

官方列表中的 `get_market_overall` 未在本账号工具清单中见到；本账号有 `get_market_trends`。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 135 | `list_user_markets` | 列出用户已跟踪的市场 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 136 | `get_market_leaderboard` | 获取市场排行榜 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 137 | `get_market_products` | 获取市场中的商品 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 138 | `get_market_statistics` | 获取市场统计 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 139 | `get_market_trends` | 获取市场趋势 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 十四、账号与用量（5 个，全部为读）

官方 Account Management 里的 `get_current_user_info` 未在本账号工具清单中见到。`get_mcp_usage_info` 的说明写明：调用它本身不消耗配额。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 140 | `get_my_amazon_seller_accounts` | 列出已关联的亚马逊卖家账户 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 141 | `get_my_wmt_seller_accounts` | 列出已关联的沃尔玛卖家账户 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 142 | `get_my_tt_seller_accounts` | 列出已关联的 TikTok Shop 卖家账户 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 143 | `get_brand_seller_profiles` | 获取品牌卖家档案 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 144 | `get_mcp_usage_info` | 查询当前周期的 MCP 调用配额：上限、已用、剩余。调用本身不占配额 | 读 | 本账号 schema 未见额外业务参数；其他未展开字段记为未公开 |

## 十五、亚马逊红人 Influencers（10 个，其中 5 个为写）

官方该类写 9 个；本账号实到 10 个。写入只改 Helium 10 里的红人文件夹，不是改广告或 Listing。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 145 | `search_amazon_influencers` | 搜索亚马逊红人 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 146 | `search_amazon_influencer_videos` | 搜索红人视频 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 147 | `get_amazon_influencer_profile` | 获取红人资料 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 148 | `list_amazon_influencer_folders` | 列出红人文件夹 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 149 | `list_amazon_influencers_in_folder` | 列出某文件夹中的红人 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 150 | `create_amazon_influencer_folder` | 新建红人文件夹 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 151 | `rename_amazon_influencer_folder` | 重命名红人文件夹 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 152 | `delete_amazon_influencer_folders` | 删除红人文件夹 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 153 | `add_amazon_influencers_to_folder` | 把红人加入文件夹 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 154 | `remove_amazon_influencers_from_folder` | 从文件夹移除红人 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |

## 十六、Walmart 利润、成本与库存（18 个，其中 5 个为写）

结构与亚马逊财务工具平行。

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 155 | `get_wmt_account_profit_and_loss_summary` | 沃尔玛账户级 P&L 汇总 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 156 | `get_wmt_account_profit_and_loss_summary_series` | 沃尔玛账户级 P&L 汇总统序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 157 | `get_wmt_account_profit_and_loss_breakdown` | 沃尔玛账户级 P&L 拆分 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 158 | `get_wmt_account_profit_and_loss_breakdown_series` | 沃尔玛账户级 P&L 拆分时间序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 159 | `get_wmt_product_profit_and_loss_summary` | 沃尔玛产品级 P&L 汇总 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 160 | `get_wmt_product_profit_and_loss_summary_series` | 沃尔玛产品级 P&L 汇总统序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 161 | `get_wmt_product_profit_and_loss_breakdown` | 沃尔玛产品级 P&L 拆分 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 162 | `get_wmt_product_profit_and_loss_breakdown_series` | 沃尔玛产品级 P&L 拆分时间序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 163 | `list_wmt_cogs` | 列出沃尔玛 COGS | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 164 | `edit_wmt_cogs` | 新增或更新沃尔玛 COGS | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 165 | `delete_wmt_cogs` | 删除沃尔玛 COGS | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 166 | `list_wmt_indirect_cost_settings` | 列出沃尔玛间接费用设置 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 167 | `list_wmt_indirect_costs` | 列出沃尔玛间接费用 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 168 | `create_wmt_indirect_cost` | 创建沃尔玛间接费用 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 169 | `edit_wmt_indirect_cost` | 编辑沃尔玛间接费用 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 170 | `delete_wmt_indirect_costs` | 删除沃尔玛间接费用 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 171 | `get_wmt_inventory_values` | 查询沃尔玛库存价值 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 172 | `get_wmt_sales_velocity` | 查询沃尔玛销售速率 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 十七、TikTok 利润、成本与库存（18 个，其中 5 个为写）

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 173 | `get_tt_account_profit_and_loss_summary` | TikTok 账户级 P&L 汇总 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 174 | `get_tt_account_profit_and_loss_summary_series` | TikTok 账户级 P&L 汇总统序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 175 | `get_tt_account_profit_and_loss_breakdown` | TikTok 账户级 P&L 拆分 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 176 | `get_tt_account_profit_and_loss_breakdown_series` | TikTok 账户级 P&L 拆分时间序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 177 | `get_tt_product_profit_and_loss_summary` | TikTok 产品级 P&L 汇总 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 178 | `get_tt_product_profit_and_loss_summary_series` | TikTok 产品级 P&L 汇总统序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 179 | `get_tt_product_profit_and_loss_breakdown` | TikTok 产品级 P&L 拆分 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 180 | `get_tt_product_profit_and_loss_breakdown_series` | TikTok 产品级 P&L 拆分时间序列 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 181 | `list_tt_cogs` | 列出 TikTok COGS | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 182 | `edit_tt_cogs` | 新增或更新 TikTok COGS | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 183 | `delete_tt_cogs` | 删除 TikTok COGS | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 184 | `list_tt_indirect_cost_settings` | 列出 TikTok 间接费用设置 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 185 | `list_tt_indirect_costs` | 列出 TikTok 间接费用 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 186 | `create_tt_indirect_cost` | 创建 TikTok 间接费用 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 187 | `edit_tt_indirect_cost` | 编辑 TikTok 间接费用 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 188 | `delete_tt_indirect_costs` | 删除 TikTok 间接费用 | 写 | 见本账号工具 schema；未展开字段名记为未公开 |
| 189 | `get_tt_inventory_values` | 查询 TikTok 库存价值 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 190 | `get_tt_sales_velocity` | 查询 TikTok 销售速率 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 十八、TikTok 商品研究（6 个，全部为读）

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 191 | `search_tiktok_products` | 搜索 TikTok 商品 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 192 | `search_tiktok_influencers` | 搜索 TikTok 红人 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 193 | `search_tiktok_hot_videos` | 搜索 TikTok 热门视频 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 194 | `get_tiktok_product_influencers` | 查询某个 TikTok 商品的红人 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 195 | `get_tiktok_product_gmv_history` | 查询 TikTok 商品 GMV 历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |
| 196 | `get_tiktok_product_items_sold_history` | 查询 TikTok 商品销量历史 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

## 十九、其他（1 个，读）

| # | 工具名 | 说明 | 读/写 | 关键参数 |
|---|---|---|---|---|
| 197 | `report_missing_tool` | 向 Helium 10 反馈缺失的工具。官方说明：不消耗配额 | 读 | 见本账号工具 schema；未展开字段名记为未公开 |

---

## 写入类工具汇总（45 个）

调用这些工具会在 Helium 10 或亚马逊侧改数据。项目既有规则仍然有效：没有用户按批次明确确认，不得调用。下面按影响范围分组。

**会改亚马逊广告账户（需额外条件，见后文）：**

- `execute_ads_campaign_mgmt_actions`：终端写入。官方文章：每批最多 20 个 action、每类最多 10 个 item，必须先确认。工具 schema：上限以 `maxActionsPerRequest` 为准。官方文章写不能删除活动、只能暂停；工具说明里出现了 delete，两处不一致。
- `manage_ads_rule`：`draft_commit` 可把规则变成线上自动化（`automated=true` 会改出价 / 预算 / 投放位置或创建目标），`rule_delete` 会永久删除规则。

**会改亚马逊 Listing：**

- `sync_listing_to_amazon`：经 SP-API 发布 title / item_highlight / bullets / description，必须有明确的发布同意。

**只改 Helium 10 Listing Builder，不会自动发布：**

- `request_image_upload_url`、`generate_listing`、`rewrite_listing_section`

**改 Helium 10 成本与监控（会影响后续 P&L / 预警，不直接改广告）：**

- 亚马逊：`edit_amazon_cogs`、`delete_amazon_cogs`、`create_amazon_indirect_cost`、`edit_amazon_indirect_cost`、`delete_amazon_indirect_costs`
- 沃尔玛：`edit_wmt_cogs`、`delete_wmt_cogs`、`create_wmt_indirect_cost`、`edit_wmt_indirect_cost`、`delete_wmt_indirect_costs`
- TikTok：`edit_tt_cogs`、`delete_tt_cogs`、`create_tt_indirect_cost`、`edit_tt_indirect_cost`、`delete_tt_indirect_costs`
- 监控：`edit_product_monitoring`

**改 Keyword Tracker、My List、红人文件夹：**

- 跟踪词 / 竞品 / 备注 / 标签 / 文件夹：`add_untracked_product_keywords`、`add_keyword_competitors`、`remove_keyword_competitors`、`add_keyword_note`、`update_keyword_note`、`remove_keyword_note`、`delete_tracked_keywords`、`delete_tracked_product`、`link_keyword_tag`、`unlink_keyword_tag`、`add_keywords_to_folder`、`create_keyword_folder`、`delete_keyword_folder`、`delete_keywords_from_folder`、`move_keywords_between_folders`、`rename_keyword_folder`
- My List：`add_saved_product`、`delete_saved_product`
- 红人文件夹：`create_amazon_influencer_folder`、`rename_amazon_influencer_folder`、`delete_amazon_influencer_folders`、`add_amazon_influencers_to_folder`、`remove_amazon_influencers_from_folder`

---

## 接入方式

1. **端点。** 官方 GitHub README 写明 MCP 端点是 `https://mcp.helium10.com/mcp`（streamable HTTP）。一个服务器，连接时按账户权益发现工具。
2. **鉴权。** 官方 README 写的是 OAuth 2.1 Authorization Code + PKCE，以及 Dynamic Client Registration（DCR），并且写明不需要额外的 headers 块。官方帮助中心在架构说明里写的是 “OAuth 2.0”。官方博客写“via OAuth”连接一次即可。这里按原文并列记录，不自行判断哪一个版本号是最终标准。本账号这边已经完成连接：`user-helium10` 状态为 connected。
3. **客户端。** 官方 README 和产品页列出的兼容客户端包括 Claude、ChatGPT、Cursor、Codex、自定义 Agent 等。帮助中心有分客户端的接入步骤。
4. **套餐门槛。** 官方产品页和帮助中心都写：Helium 10 MCP 向 **Diamond 或更高** 套餐提供，**不另收 MCP 功能费**（包含在 Diamond 内）。官方定价页 Diamond 项下也写：“Helium 10 MCP: Included free (up to 1,000 calls)”。
5. **广告权限是另一步。** 官方 FAQ：Helium 10 Ads 档案必须是 **Managed** 模式，不能是 Disable 或 Analytics-only，否则可能出现 `FEATURE.ADS_ACCESS_DISABLED`。Managed 模式会对通过 Helium 10 Ads 管理的广告花费收取 **2%** 费用（官方定价页 FAQ 与帮助中心均有此说明）。本回合 `GetMcpServerStatus` 只显示 `user-helium10` 已连接、197 个工具，**状态接口没有单独列出 Ads PPC token**。先前会话记录该 token 尚未连接；本回合未能从状态接口独立验证这一点。广告类工具已经出现在 197 个工具清单里，但查询或写入能否真正执行，还取决于 Ads 档案模式和账户侧授权是否完整。

## 价格

以下价格来自官方定价页 [https://www.helium10.com/pricing/](https://www.helium10.com/pricing/)（2026-11-14 抓取）和官方帮助中心 Getting Started。金额为美元，结账时另加适用税费。

| 项目 | 官方表述 |
|---|---|
| Diamond 月付 | $359 / 月 |
| Diamond 年付 | $279 / 月（官方定价页写年付可省 $960；按 $279×12 为 $3,348 / 年） |
| Enterprise | 起步价 $1,499 / 月，按年计费（官方定价页写 Starting Price. Billed Annually） |
| MCP 功能费 | Diamond 包含，不另收功能费 |
| MCP 包含配额 | 官方帮助中心和定价页：每月 **1,000** 次调用；与子账号共享（帮助中心） |
| MCP 扩容 | 官方帮助中心：加购 **$50** 可增加每月 **500** 次调用。官方定价页在附加项里另写 “MCP … Add-on Starting at $50/mo”。这两处是否完全是同一项，官方页面没有写清，这里并列记录，不自行合并 |
| Helium 10 Ads | Diamond 可用。通过 Helium 10 Ads 管理的广告花费收取 **2%** 管理费 |

Platinum（月付 $129，年付 $99 / 月）的定价页正文没有把 MCP 列进该档的包含项；官方多次写 MCP 需要 Diamond 或更高。是否允许 Platinum 只买 $50 MCP 附加项：**未证实**。

## 限制与缺口

- **配额。** 包含 1,000 次 / 月。`get_mcp_usage_info` 和 `report_missing_tool` 的说明写明不消耗配额。大批量研究（很多 ASIN、很多关键词批次）会很快用掉额度。
- **广告写入前置条件。** Ads 档案需为 Managed（2% 费用）；需用户按批次确认；官方文章还有每批 20 个 action、每类 10 个 item 的上限。未满足时可能出现 `FEATURE.ADS_ACCESS_DISABLED`。
- **广告标签不能在 MCP 里写。** 只能读，创建和打标签要到 Helium 10 Ads 界面。
- **官方“不能做什么”与实到工具并不完全一致。** 帮助中心写过 MCP 不能直接进 Seller Central、不能改 Listing 图片 / 视频 / A+，有的 FAQ 还写不能改 Listing 内容；但本账号有 `sync_listing_to_amazon`（改标题、卖点、要点、描述，不改主图 / 视频 / A+）。以工具 schema 为准，同时保留官方 FAQ 原文差异。
- **官方工具列表与本账号清单有出入。** 见上文 `create_amazon_cogs`、`get_current_user_info`、`get_market_overall`；以及官方分类计数与列举不全的问题。
- **产品页“40+ 工具”与帮助中心“200 工具”、本账号 197 个工具不一致。**
- **Follow-Up、广告标签创建、主图 / A+ / 视频修改等，不在本账号 MCP 的写入范围。**

## 和本项目的关系（ring banner，B0H6J55GBP）

本项目是 AutoSell，主推 ring banner，ASIN **B0H6J55GBP**，售价约 $13.99，最大 CPA $8，盈亏平衡 ACOS 约 57%，目标 ACOS 约 45–50%，广告账户为 Trendy Treasures Store。下面只根据已核实的工具能力来写，不把“建议”写成已经执行的操作。

- **可以做关键词和竞品研究（只读）。** 用 `get_keywords_by_asin`、`compare_asin_keywords`、`search_amazon_brand_analytics`、`get_keywords_search_volume_history`、`search_competitors_by_asin`、`get_competitor_overview` 等，复核 B0H6J55GBP 和竞品的词、ABA、历史搜索量和竞品结构。这和极目（Jiimore）MCP 的只读研究可以对照，但两边口径不同，不能直接把数字当同一套指标。
- **可以看本账户的流量、利润、库存和搜索词表现（只读）。** `get_amazon_product_traffic`、`get_search_query_performance`、产品级 P&L、`get_sales_velocity`、`get_fba_inventory_health_status` 适合用来解释流量和利润变化。这些要消耗每月 1,000 次配额，查询前应先看 `get_mcp_usage_info`（它本身不占配额）。
- **可以生成 Listing 文案，但不能在未确认时同步到亚马逊。** `generate_listing` / `rewrite_listing_section` 只写在 Helium 10；`sync_listing_to_amazon` 会改线上 Listing，必须单独得到发布同意。
- **广告结构搭建还不能当成已经可执行。** 清单里已有查询和写入工具，但：1）官方要求 Ads 档案为 Managed，并收取 2% 费用；2）状态接口本回合没有单独列出 Ads PPC token，先前会话记录为尚未连接，本回合未能独立验证；3）即使授权完整，每次写入仍必须按批次确认，不能自动建 SP 或改出价。在这些条件满足之前，只适合用 `execute_ads_query` 做诊断（若查询也被权限拦住，应把官方错误原文记下来，不要改用写入工具去试）。
- **和极目 MCP 的分工。** 极目 17 个工具全部是只读市场数据；Helium 10 覆盖本账户广告、利润、库存、跟踪词，并带写入能力。研究阶段可以两边对照；改广告、改 Listing、改 COGS，只走 Helium 10，而且要先确认。

## 来源

- 官方 MCP 产品页：https://www.helium10.com/tools/mcp/
- 官方定价页：https://www.helium10.com/pricing/
- 官方博客（2026-07-16 发布，2026-09-02 修改）：https://www.helium10.com/blog/helium-10-mcp/
- 官方帮助中心 Getting Started（页面标注更新时间 November 13, 2026 22:33，来源时区未标明）：https://kb.helium10.com/hc/en-us/articles/51580564409883-Getting-Started-with-Helium-10-MCP
- 官方帮助中心 Ads Write Capabilities（页面标注 August 28, 2026 17:19，来源时区未标明）：https://kb.helium10.com/hc/en-us/articles/55042225325083-Using-Ads-Write-Capabilities-with-Helium-10-MCP
- 官方 GitHub README：https://raw.githubusercontent.com/helium10/Helium10-MCP/main/README.md
- 官方 MCP 端点：https://mcp.helium10.com/mcp
- 本账号已连接的工具目录：MCP 命名空间 `user-helium10`（2026-11-14，北京时间，197 个工具，状态 connected）
