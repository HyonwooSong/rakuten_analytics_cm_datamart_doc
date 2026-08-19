# CM Migration — Four Focus Areas: Origin File Deep-Dive

> **Project:** CONRAT-44285 [DS] RMPREP × CategoryMart  
> **Author:** Song, Hyon Woo (Erin)  
> **Date:** 2026-08-19  
> **Purpose:** Trace each CM migration gap to its **very origin data file**, distinguish ope-master (hand-maintained TSV) vs. pipeline-computed vs. reporting-side YAML, and clarify what needs to be built/ported for CM.

---

## ope_master_data — Full File List

`rep-batch-data/ope_master_data/` contains all hand-maintained master TSVs loaded into the GATD pipeline.

### Structure

```
ope_master_data/
├── nsl/          ← 20 files (production master TSVs, loaded by batch)
│   ├── nm201 〜 nm230, nm243  (active)
│   └── nm027, nm028           (.abolished — 廃止)
├── rd/
│   └── dm003                  (yoridori choice filter, 55 rows)
└── asl/
    ├── hive/    ← ad004〜ad019 (10 files, Hive/BQ serving dims)
    └── oracle/  ← ad004〜ad019 (10 files, Oracle/Tableau serving dims — same content)
```

**Total unique master files: 21 active (nsl) + 1 (rd/dm003) + 10 (asl dims) = 32 files**  
(+ 2 abolished in nsl/ · + reporting-side `shop_groups/*.yml` in a separate Databricks ETL repo — not part of ope_master_data)

---

### nsl/ — NSL layer (contract & brand masters)

| File | Table | Rows | Content |
|---|---|---:|---|
| `nm201_tbl_maker_master_v2.tsv` | nm201 | 499 | メーカーマスタ |
| `nm202_tbl_product_brand_master_v2.tsv` | nm202 | 2,297 | 商品ブランドマスタ（管理対象＋競合） |
| `nm203_tbl_product_master_v2.tsv` | nm203 | **99,708** | 商品マスタ（RANコード） |
| `nm204_tbl_contracted_bu_master_v2.tsv` | nm204 | 19 | 契約BUマスタ（Tableau/RA アカウント、`process_type` 含む） |
| `nm205_tbl_contracted_brand_master_v2.tsv` | nm205 | 214 | 契約ブランドマスタ（`rank_aggregation_period` 含む） |
| `nm206_tbl_competing_brand_master_v2.tsv` | nm206 | 2,016 | 競合ブランドマスタ＋表示マスク（`brand_disp_name` / `maker_disp_name`） |
| `nm207_tbl_product_group_master_v2.tsv` | nm207 | 564 | 商品グループ階層 |
| `nm208_tbl_brand_layer_master_v2.tsv` | nm208 | **20,915** | ブランド層階層（brand/sub_brand/sub_sub_brand/RAN leaf、`layer_level_no` 1-4） |
| `nm209_tbl_brand_loyalty_rank_master_v2.tsv` | nm209 | 4,704 | ロイヤルティランク閾値（MONETARY/REPEAT × L/M/H/ExH） |
| `nm210_tbl_contract_product_brand_mapping_master.tsv` | nm210 | 307 | 契約ブランド × 商品ブランド対応 |
| `nm218_tbl_brand_page_master_v2.tsv` | nm218 | 88 | ブランドページURL → brand_layer_id |
| `nm219_tbl_device_master_v2.tsv` | nm219 | 12 | デバイスコード → `device_name` + **`os_name`** |
| `nm220_tbl_page_class_master_v2.tsv` | nm220 | 5 | ページ分類（ドメイン/パスタイプ） |
| `nm223_tbl_member_attribute_master_v2.tsv` | nm223 | 1,029 | 会員属性（年齢層/性別/都道府県） |
| `nm225_tbl_service_master_v2.tsv` | nm225 | 1 | 楽天グループサービス分類 |
| `nm226_tbl_selling_form_master_v2.tsv` | nm226 | 4 | 販売形態（中古/並行輸入/アウトレット/通常） |
| `nm227_tbl_brand_layer_filterset_v2.tsv` | nm227 | 1,498 | brand_layer_id → filtersetcode（AnTARES） |
| `nm228_tbl_brand_group_filterset_v2.tsv` | nm228 | 3,485 | (product_group_id, product_brand_id) → filtersetcode |
| `nm230_tbl_brand_group_master_v2.tsv` | nm230 | 4,289 | 商品グループ × ブランド対応 |
| `nm243_tbl_app_type_master_v2.tsv` | nm243 | 30 | アプリタイプ（Webアプリ等） |
| ~~`nm027_tbl_brand_dict_master.tsv.abolished`~~ | nm027 | ~~9,311~~ | ⛔ 廃止 — 旧ブランド辞書マスタ |
| ~~`nm028_tbl_brand_group_dict_master.tsv.abolished`~~ | nm028 | ~~15,850~~ | ⛔ 廃止 — 旧ブランドグループ辞書 |

---

### rd/ — RD layer

| File | Table | Rows | Content |
|---|---|---:|---|
| `dm003_tbl_yoridori_choice_filter_master.tsv` | dm003 | 55 | よりどり choice 除外 regex パターン（set/yoridori 検出） |

---

### asl/hive/ and asl/oracle/ — ASL layer (serving dimension tables)

Both folders contain identical files — `hive/` = Hive/BQ serving, `oracle/` = Oracle/Tableau serving.

| File | Table | Rows | Content |
|---|---|---:|---|
| `ad004_tbl_age_gender.tsv` | ad004 | 27 | 年齢層 × 性別ディメンション |
| `ad005_tbl_rakuten_rank.tsv` | ad005 | 7 | 楽天会員ランク（ダイヤ/プラチナ/ゴールド/シルバー/レギュラー…） |
| `ad006_tbl_residence.tsv` | ad006 | 50 | 居住地（都道府県） |
| `ad007_tbl_loyalty_rank.tsv` | ad007 | 6 | ロイヤルティランク区分（L/M/H/ExH + 非会員等） |
| `ad008_tbl_new_repeat.tsv` | ad008 | 5 | 新規/スイッチ/リピート区分（1/2/3） |
| `ad010_tbl_selling_form.tsv` | ad010 | 24 | 販売形態ディメンション（ASL版、nm226より詳細） |
| `ad016_tbl_device.tsv` | ad016 | 3 | デバイス区分（PC/モバイル/…） |
| `ad017_tbl_os.tsv` | ad017 | 3 | OS区分 |
| `ad018_tbl_app_type.tsv` | ad018 | 3 | アプリタイプ区分 |
| `ad019_tbl_traffic_class.tsv` | ad019 | 9 | トラフィック分類（流入元） |

---

## Quick Reference — Origin File Summary

| Focus area | Very origin file | Repo / layer | ope-master TSV? |
|---|---|---|:---:|
| **shop_group** | `resources/shop_groups/{client}.yml` | RMP **Databricks reporting** ETL | ❌ YAML |
| **sub_brand / sub_sub_brand** | `nm208_tbl_brand_layer_master_v2.tsv` | GATD `ope_master_data/nsl/` | ✅ |
| **Competitor definition** | `nm206_tbl_competing_brand_master_v2.tsv` | GATD `ope_master_data/nsl/` | ✅ |
| **Monetary / Repeat rank thresholds** | `nm209_tbl_brand_loyalty_rank_master_v2.tsv` | GATD `ope_master_data/nsl/` | ✅ (definition only) |
| **Per-user rank value** | `ns215_loyalty_rank_history_v2` (computed) | GATD NSL pipeline | ❌ computed |
| **new / repeat / switch** | computed from `ns215` + `ns249` (needs `nm206`) | GATD NSL pipeline | ❌ computed |
| **OS name** | `nm219_tbl_device_master_v2.tsv` (`os_name` col) | GATD `ope_master_data/nsl/` | ✅ |
| **App type** | `nm243_tbl_app_type_master_v2.tsv` | GATD `ope_master_data/nsl/` | ✅ |
| **Selling form** | `nm226_tbl_selling_form_master_v2.tsv` | GATD `ope_master_data/nsl/` | ✅ |
| **Set / yoridori flags** | `dm003_tbl_yoridori_choice_filter_master.tsv` | GATD `ope_master_data/rd/` | ✅ (RD layer) |

Two distinct source roots exist:
- **GATD pipeline masters:** `rep-batch-data/ope_master_data/nsl/*.tsv` (+ `rd/` for dm003)
- **RMP reporting (Databricks):** `databricks-maker-sight-analytics-etl/resources/shop_groups/*.yml`

---

## 1. shop_group

### Origin: per-client YAML — NOT ope-master

`shop_group` does **not** exist anywhere in the GATD pipeline (confirmed: no `shop_group` / `v_m_shop_group` reference in all of `rep-batch-data`). The GATD shop master chain carries only:

```
rm003 (RSL raw) → nm029 (NSL) → am046 (ASL dim)
columns: shop_id, shop_name, shop_url, service_code
```

Shop grouping is defined and loaded entirely **on the RMP reporting / Databricks side**:

```
resources/shop_groups/{client}.yml          ← VERY ORIGIN (hand-maintained per client)
  → scripts/load_shop_groups.py
  → m_shop_group  (Delta table in rmp_datamart_clients_{env})
  → clients/*.sql  JOIN on shop_id
  → v_brand_sales_g.shop_group_name / v_m_shop_group view
```

### Format (sapporobeer.yml as example)
```yaml
- label: "01.楽天24"
  shop_ids:
    - 261122
- label: "02.楽天24ドリンク館"
  shop_ids:
    - 306273
- label: "他全店舗"   # catch-all, shop_ids: []
  shop_ids: []
```

### 19 client YMLs that exist
`asahi_beer` · `kirin_beer` · `sapporobeer` · `sapporo` · `kirin_beverage` · `kagome` · `kao` · `loreal` · `shiseido` · `johnson_and_johnson` · `royal_canin` · `mars` · `lego` · `ecoflow` · `ecovacs` · `otsuka_pharmaceutical` · `panasonic_beauty_appliances` · `panasonic_dental` · `panasonic_mens_shaver`

**Asahi Beer** (`asahi_beer.yml`): 3 groups — `01.楽天24` / `02.楽天24ドリンク館` / `他全店舗`.

### CM approach
Port these 19 YAMLs into a new **CM-side shop-group master** keyed on `shop_id`. Not a structural gap — just a new master table to build. P&G has no YAML → must be authored with the client.

---

## 2. sub_brand / sub_sub_brand

### Origin: `nm208_tbl_brand_layer_master_v2.tsv` (ope-master, 20,915 rows)

The entire `brand → sub_brand → sub_sub_brand` tree is defined in **one TSV** as a self-referencing parent-child hierarchy.

### nm208 columns (from DDL)
| Column | Type | Note |
|---|---|---|
| `brand_layer_id` | INT64 | PK |
| `parent_brand_layer_id` | INT64 | self-reference — defines the tree |
| `ran_class_code` | STRING | set only at layer 4 (leaf) |
| `ran_body_part` | STRING | set only at layer 4 (leaf) |
| `product_start_date` | STRING | |
| `brand_layer_name` | STRING | **the name at this tier** |
| `disp_order_no` | INT64 | display sort order |
| `contract_brand_id` | INT64 | FK → nm205 |
| `product_group_id` | INT64 | FK → nm207 |
| `layer_level_no` | INT64 | **tier key — see below** |
| `brand_layer_start_date` | STRING | SCD2 |
| `brand_layer_end_date` | STRING | SCD2 |
| `aggregation_start_date` | STRING | |
| `aggregation_end_date` | STRING | |

### `layer_level_no` = the tier
| level | meaning |
|---|---|
| 1 | **brand** (ブランド大分類) |
| 2 | **sub_brand** (ブランド中分類) |
| 3 | **sub_sub_brand** (ブランド小分類) |
| 4 | **RAN leaf** (item — carries `ran_class_code`/`ran_body_part`) |

### Computed flattening: `nm211_brand_layer_v2_merge_info` (not a source file)
Dashboard-ready denormalized view built from `nm208`. Key columns:

```
brand_layer_id, layer_level_no, contract_brand_id
brand_id, brand_name
sub_brand_id, sub_brand_name
sub_sub_brand_id, sub_sub_brand_name
ran_code, product_name
pg1_id–pg4_id, pg1–pg4
disp_order_no1–disp_order_no4     ← sort keys
rank_aggregation_period
contract_business_unit_id, maker_name
```

### Why CM sub_brand / sub_sub_brand is NULL
CM's `cm_item_brand_master` was populated from `nm208` **layer 1 only** (brand). Levels 2 and 3 were never ported. Fix = CONRAT-44598: port `nm208` tiers 2/3 (with `disp_order_no`) into the CM brand master.

---

## 3. Competitor Company Definition

### Origin: `nm206_tbl_competing_brand_master_v2.tsv` (ope-master, 2,016 rows)

### nm206 columns (from DDL)
| Column | Type | Note |
|---|---|---|
| `contract_brand_id` | INT64 | PK — the contracted (own) brand |
| `product_group_id` | INT64 | PK — the product group scope |
| `product_brand_id` | INT64 | PK — the **competitor** brand |
| `process_type` | STRING | `antares` / `filtermaker` / `aio` |
| `competing_brand_start_date` | STRING | |
| `competing_brand_end_date` | STRING | |
| `aggregation_start_date` | STRING | |
| `brand_disp_name` | STRING | **masked display name** (e.g. "ブランドA") |
| `brand_disp_order_no` | INT64 | |
| `maker_disp_name` | STRING | **masked maker name** (e.g. "A社") |
| `maker_disp_order_no` | INT64 | |

This single TSV serves **two roles simultaneously**:
1. **Defines the competitor set** — which product_brand_ids are treated as competition for each own brand × product_group.
2. **Provides display masking** — the real competitor name (from `nm202`) is shown as the anonymized `brand_disp_name` / `maker_disp_name`.

### Example rows (anonymized in source data)
```
contract_brand=1, pg=1002, pb=1066 → "ブランドA" / "A社"
contract_brand=1, pg=1002, pb=1247 → "ブランドB" / "A社"
contract_brand=1, pg=1002, pb=1030 → "ブランドC" / "B社"
```

### Downstream computed tables that depend on nm206
| Table | Purpose |
|---|---|
| `nt232_brand_group_orders_v2` | competitor-tagged order facts |
| `ns249_competing_sales_history_v2` | per-user competitor purchase history |
| `as057/058_sos_genre_p` | SOS (own/competing/assortment/non-brand) |
| `dim_brand1_new_repeat_id` (SWITCH) | ns249 used for switch-detection |

### CM implication
CM has no `nm206` equivalent → SOS can neither define nor mask competitors. Raw `maker_name` is exposed as-is. **Port `nm206` for the 20 existing RMP clients; author a fresh competitor master with client/AIO for P&G** (which has no nm206 at all).

---

## 4. Other CM-Missing Data & Origins

### 4-A. Monetary / Repeat Rank

Two-part architecture:

#### (a) Threshold definition — `nm209_tbl_brand_loyalty_rank_master_v2.tsv` (ope-master, 4,704 rows)

| Column | Type | Note |
|---|---|---|
| `contract_brand_id` | INT64 | PK |
| `layer_level_no` | INT64 | PK — which brand tier the rank applies to |
| `rank_type` | STRING | PK — `MONETARY` or `REPEAT` |
| `rank_code` | STRING | PK — `L` / `M` / `H` / `ExH` |
| `rank_threshold` | INT64 | min value to reach this rank |
| `rank_threshold_unit_name` | STRING | 円 (monetary) or 回 (repeat) |

Actual demo data:
```
MONETARY: L≥1円 / M≥2041円 / H≥4081円 / ExH≥6121円
REPEAT:   L≥1回 / M≥...
```

#### (b) Per-user rank value — `ns215_loyalty_rank_history_v2` (COMPUTED, no source file)

```
nt231 (orders) × nm205.rank_aggregation_period (lookback months)
  → SUM(4b_subtotal)           = monetary_value
  → COUNT(DISTINCT order_no)  = repeat_count
  → JOIN nm209 thresholds
  → monetary_rank_code / repeat_rank_code  (L/M/H/ExH)
  + first_purchase_datetime, last_purchase_datetime
```

**CM substitute:** `brand_lapsed_days` (days since last purchase, alcohol view only) — not a rank; only a crude recency proxy.

### 4-B. new / repeat / switch 区分

Also computed (no source file); depends on `nm206`:

```
Step 1: ns215.first_purchase_datetime == today → NEW (1), else REPEAT (3)
Step 2: NEW + ns249.comp_first_purchase_datetime < today → SWITCH (2)
         (ns249 built from nm206 competitor set)
```

| | RMP | CM |
|---|---|---|
| NEW | `ns215.first_purchase_datetime = today` | `brand_lapsed_days IS NULL` (≈) |
| REPEAT | purchase history exists | `brand_lapsed_days IS NOT NULL` |
| SWITCH | competitor purchase prior to own brand (needs nm206) | ❌ not possible |

### 4-C. OS name — `nm219_tbl_device_master_v2.tsv` (ope-master)

```
device_code (INT, PK)  →  device_name (STRING)  +  os_name (STRING)
```
Sample rows:
```
0  PC        その他
1  モバイル   その他
2  モバイル   その他
```
CM has `use_device_code` but no `os_name` column — would require a CM-side join to `nm219`.

### 4-D. App type — `nm243_tbl_app_type_master_v2.tsv` (ope-master)

```
app_type_code  →  source (RED_BASKET_FUNCTION_CODE)  →  app_type_name (Webアプリ, ...)
```
CM has no app_type column at all.

### 4-E. Selling form (販売形態) — `nm226_tbl_selling_form_master_v2.tsv` (ope-master)

```
selling_form_code  →  selling_form_name
1  中古
2  並行輸入
3  アウトレット
99 通常
```

RMP detects selling form from `dt008` item names (RLIKE patterns on `dm005`). CM has no column; workaround = `item_name RLIKE` approximation:

```sql
CASE
  WHEN item_name RLIKE '.*中古.*|.*USED.*|.*used.*'  THEN 'USED'
  WHEN item_name RLIKE '.*並行.*|.*parallel.*'        THEN 'PARALLEL'
  WHEN item_name RLIKE '.*アウトレット.*|.*outlet.*'   THEN 'OUTLET'
  ELSE 'NORMAL'
END AS selling_form_code
```

### 4-F. Set / yoridori flags — `dm003_tbl_yoridori_choice_filter_master.tsv` (ope-master RD layer, 55 rows)

```
filter_id  →  detect_regex  →  reg_datetime  →  upd_datetime  →  black_flg
1           [0-9]{1,2}ケース目   ...   false
2           [0-9]{1,2}個目       ...   false
3           [0-9]{1,2}種目       ...   false
```
55 regex patterns detect yoridori choice values in item names. The resulting `set_flg` / `yoridori_flg` are computed in `dt008 / nt231` processing — **CM carries neither flag**. CM groups these orders under the `"BrandAssorted"` brand name instead.

---

## Summary: ope-master vs. computed vs. reporting-side

```
ope_master_data/nsl/ (hand-maintained TSVs → loaded by Azkaban "ValidationMasterDataTsv" stage)
├── nm206_tbl_competing_brand_master_v2.tsv     ← competitor definition + display masking
├── nm208_tbl_brand_layer_master_v2.tsv         ← brand / sub_brand / sub_sub_brand tree
├── nm209_tbl_brand_loyalty_rank_master_v2.tsv  ← monetary/repeat rank thresholds
├── nm219_tbl_device_master_v2.tsv              ← device_code → device_name + os_name
├── nm226_tbl_selling_form_master_v2.tsv        ← selling_form_code → name
└── nm243_tbl_app_type_master_v2.tsv            ← app_type_code → name

ope_master_data/rd/
└── dm003_tbl_yoridori_choice_filter_master.tsv ← yoridori choice regex patterns

Pipeline-COMPUTED (no source file — built from ope-masters + order history)
├── nm211_brand_layer_v2_merge_info             ← flattened brand/sub_brand/sub_sub_brand + disp_order
├── nm233_brand_layer_item_v2                   ← item → brand_layer SCD2
├── ns215_loyalty_rank_history_v2               ← per-user monetary/repeat rank history
└── ns249_competing_sales_history_v2            ← per-user competitor purchase history

Reporting-side (NOT in GATD ope_master — in Databricks ETL repo)
└── resources/shop_groups/{client}.yml          ← shop_id list → group label (19 clients)
```

---

## CM Migration Action Items (per area)

| Area | Action | Owner | Jira |
|---|---|---|---|
| **sub_brand / sub_sub_brand** | Port `nm208` tiers 2/3 (+ `disp_order_no`) into CM Item Brand Master | DKD | CONRAT-44598 |
| **Competitor definition** | Port `nm206` for 20 existing clients; author fresh for P&G with client/AIO | AIO | — |
| **shop_group** | Port 19 `shop_groups/*.yml` into a new `cm_shop_group_master` (`shop_id → group_label`); author P&G fresh | AIO | — |
| **Monetary / Repeat rank** | Accept `brand_lapsed_days` proxy for new/repeat; monetary/repeat rank = out of scope for CM | AIO | CONRAT-44595 ランク分析 |
| **OS / app_type** | Confirm with DKD if addable to CM event master; else drop from CM dashboards | DKD | — |
| **Selling form** | Use `item_name` RLIKE approximation; discuss exact values with DKD | DKD | — |
| **Set / yoridori** | Accept `"BrandAssorted"` grouping; document as permanent gap | AIO | — |
