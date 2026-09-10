# PV-Related Dashboards — Conceptual Spec (about item_pv/url_pv)

---
##  Core concepts behind  item_pv

| Concept                  | What it means                                                            | RAT field                                         |
| ------------------------ | ------------------------------------------------------------------------ | ------------------------------------------------- |
| **PV (Page View)**       | One item page load = one RAT event                                       | 1 row in `v_item_pv`                              |
| **Session**              | A continuous visit sequence by the same user (grouped by time proximity) | `session_id`                                      |
| **UU (Unique User)**     | Distinct visitors — includes non-members (by cookie) or members only     | `rat_cookie_id` / `user_id_masked`                |
| **Member vs non-member** | Whether the user was logged in at the time of the PV                     | `user_id_masked ≠ '0'` = member                   |
| **Referrer / Inflow**    | The traffic source that led the user to the item page                    | `inflow_name`, `inflow_area`                      |
| **Search word**          | The keyword the user typed before landing on the item page               | `search_word`                                     |
| **Time of access**       | When the item page was viewed                                            | `access_datetime`, `access_yyyymm`, `access_week` |

---

## The four analytics on item_pv

### 1. `item_pv_daily_access` — アクセス状況
> **"How much traffic did my brand's item pages receive, and how did it trend over time?"**

The foundational access dashboard. Shows PV / Session / UU trend over days, weeks, or months. Analysts use it to spot spikes (campaign effect), drops (listing issues), or seasonal patterns.

Key insight: **Search count within PV** — how many of those page views came from an in-site search, surfacing how often users are actively seeking the brand vs. passively browsing.

---

### 2. `item_pv_overview` — 商品ページ分析
> **"How does traffic break down across any two dimensions I choose?"**

A free-form cross-analysis view. The analyst picks two breakdown axes (e.g. brand × device, category × age group) and one metric (PV / UU / search-based PV), producing a matrix view.

This is the most flexible of the four — it answers "which segment drives traffic?" across 20 possible dimensions including brand hierarchy, user demographics, device, member rank, geography, and shop group.

---

### 3. `item_pv_referrer` — 参照元分析
> **"Where did users come from before they viewed my brand's item pages?"**

Inflow / referrer analysis. Shows which traffic sources (search engines, Rakuten internal links, direct, campaign banners, etc.) are driving visitors to the brand's items, and how each source's contribution trends over time.

Unique to this dashboard: the **inflow area** dimension (`inflow_area`) groups referrers by source zone (e.g. Rakuten search, external search, Rakuten campaign, etc.), and analysts can optionally exclude unknown referrers.

---

### 4. `item_pv_search` — 検索ワード
> **"What words are users typing to find (or not find) my brand's items?"**

Two complementary views:
- **Inclusion keywords** — words users searched and then *landed* on the brand's item page → what's driving discovery.
- **Exclusion keywords** — words users searched but *did not* land → what the brand is missing or competing against.
- **Trend view** — how a specific keyword's PV contribution changes over time.

This is the most brand-strategy-oriented of the four: it surfaces vocabulary gaps between how the brand describes its products and how users actually search.

##  Core concepts behind url_pv

While `v_url_pv` covers **specific URLs registered by the client** — typically brand microsites, campaign landing pages, or official brand pages hosted within the Rakuten ecosystem.

| Concept                | What it means                                 | RAT field                                       |
| ---------------------- | --------------------------------------------- | ----------------------------------------------- |
| **PV / UU / Session**  | Same as `v_item_pv`                           | `rat_cookie_id`, `user_id_masked`, `session_id` |
| **Dwell time**         | How long the user stayed on the page          | `staying_time_seconds`                          |
| **Exit / Bounce rate** | Whether the user left without further action  | `leave_flg`                                     |
| **Traffic source**     | How the user arrived at the brand URL         | `traffic_source_name`                           |
| **Page / URL class**   | Which specific URL or page group was accessed | `page_class_name`, `url` (from `v_d_url_page`)  |


## The two analytics on url_pv
### `url_pv_access_list` — アクセス状況一覧表
> **"How did my brand site URLs perform over time, and are users actually engaging once they arrive?"**

A time-series list view per URL. Unlike `item_pv_daily_access` which measures reach (how many came), this dashboard adds **engagement depth**: how long users stayed (`平均滞在時間`) and how many left immediately (`離脱率`). A brand can distinguish between URLs that attract traffic but fail to hold attention vs. those with genuine engagement.

The `:basis_selection` switch (PVベース / UUベース) lets analysts choose whether averages are computed per page view or per unique user — useful for separating visit-level vs. person-level engagement.

Extra filter unique to this dashboard: **`:source_filter` → `traffic_source_name`** — which traffic source brought users to the brand URL (e.g. Rakuten internal, external search, campaign).

---

### `url_pv_brand_site` — ブランドサイト分析
> **"How do brand site metrics break down across any two dimensions I choose?"**

The URL-level equivalent of `item_pv_overview` — free-form cross-analysis with two selectable axes. The key distinction is that **`URL` itself is a breakdown axis** here (`page_class_name`), letting analysts compare engagement across different pages within the brand site in a single view.

Metrics extend beyond simple PV/UU to include session count, average dwell time, and exit rate per breakdown combination — enabling segment-level engagement quality analysis (e.g. "which age group stays longest on the campaign page?").