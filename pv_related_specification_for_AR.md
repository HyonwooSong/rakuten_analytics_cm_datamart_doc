# PV-Related Dashboards — Conceptual Spec (RAT Data Perspective)

---

##  Core RAT concepts behind `v_item_pv`

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

## The four analytics

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
