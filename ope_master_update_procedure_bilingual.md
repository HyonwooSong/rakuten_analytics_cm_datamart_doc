# ope-master Update & Maintenance Procedure / ope-master 更新・メンテナンス手順

> **Source / 一次ソース:** [AIO 佐藤対応内容説明](https://confluence.rakuten-it.com/confluence/spaces/AIO/pages/6863144149/)  
> **Project / プロジェクト:** [CONRAT-44285 [DS] RMPREP × CategoryMart](https://jira.rakuten-it.com/jira/browse/CONRAT-44285)  
> **Owner:** Song, Hyon Woo (Erin / ECFD)

---

<!-- ============================================================ -->
<!-- ENGLISH VERSION                                               -->
<!-- ============================================================ -->

# 🇬🇧 English

## Team Roles

| Team | Person | Role |
|---|---|---|
| **AIO** | Sato, Shinjiro (Shinn) | Requirements spec check, filter spec decision, AIO SBX master authoring (monthly), numeric diff verification, dashboard update |
| **DKD** | Kobayashi | AnTARES filter creation, filtersetcode issuance, Product Group additions |
| **GATD → ECFD** | Onishi → Erin | Production TSV update, validation, BQ load, pipeline execution |

---

## What AIO (Sato) Does — Phase 1 Detail

### 1. Requirements Spec Check
- Check for inconsistencies with previously registered content
- Check for logical breaks in the hierarchy structure
- Verify the brand is already sold on the market
- **Note: No formal checklist — judgment-based review**

### 2. Filter Spec Decision
- Decide the boundary between **filtersetcode** (AnTARES) and **GTIN (RAN code)** per requirement
- Determine scope: what goes into a filter vs. what is determined by GTIN

### 3. Master Update (monthly — 基本月1回)
- Update AIO SBX master tables in `spdb-sbx.sbx_icb_homelife.*`
- Refer to the update matrix below for which tables to update per operation type

### 4. Numeric Diff Verification
- After pipeline runs, verify where numeric differences occur per the requested change

### 5. Product Group Addition / Change Spec
- When a new client cannot be handled with existing product groups, coordinate with DKD
- **Request manual:** https://confluence.rakuten-it.com/confluence/display/DM/Product+Group+%7C+00+%7C+REQUEST+MANUAL
- **Request form:** https://confluence.rakuten-it.com/confluence/display/DM/Product+Group+%7C+01+%7C+REQUEST+FORM

---

## AIO SBX Master Tables (`spdb-sbx.sbx_icb_homelife.*`)

All tables share common audit columns: `reg_datetime`, `upd_datetime`, `deleteflg`.

> **⚠️ CRITICAL: Rows with `deleteflg = 1` are EXCLUDED when loading SBX → production.**

| No | Physical name | Logical name | DSMS update target |
|---|---|---|:---:|
| 1 | nm201_tbl_maker_master_v2 | Maker master | 〇 |
| 2 | nm202_tbl_product_brand_master_v2 | Brand master | 〇 |
| 3 | nm203_tbl_product_master_v2 | GTIN (RAN code) master | 〇 |
| 4 | nm204_tbl_contracted_bu_master_v2 | Contracted client BU master | 〇 |
| 5 | nm205_tbl_contracted_brand_master_v2 | Contracted brand master | 〇 |
| 6 | nm206_tbl_competing_brand_master_v2 | Competitor brand master | 〇 |
| 7 | nm207_tbl_product_group_master_v2 | Product group master | 〇 |
| 8 | nm208_tbl_brand_layer_master_v2 | Brand layer hierarchy master | 〇 |
| 9 | nm209_tbl_brand_loyalty_rank_master_v2 | Brand loyalty rank master | 〇 |
| 10 | nm210_tbl_contract_product_brand_mapping_master | Contract × brand mapping | 〇 |
| 11 | nm218_tbl_brand_page_master_v2 | Brand page master | 〇 |
| 12 | nm219_tbl_device_master_v2 | Device master | — |
| 13 | nm220_tbl_page_class_master_v2 | Page class master | — |
| 14 | nm223_tbl_member_attribute_master_v2 | Member attribute master | — |
| 15 | nm225_tbl_service_master_v2 | Service master | — |
| 16 | nm226_tbl_selling_form_master_v2 | Selling form master | — |
| 17 | nm227_tbl_brand_layer_filterset_v2 | Brand layer filterset master | 〇 |
| 18 | nm228_tbl_brand_group_filterset_v2 | Brand group filterset master (contract) | 〇 |
| 19 | nm230_tbl_brand_group_master_v2 | Brand group master (general) | 〇 |
| 20 | nm243_tbl_app_type_master_v2 | Brand group master | 〇 |

---

## Update Matrix — Which Tables to Update per Operation Type

〇 = Required　△ = Conditional　— = Not required

| Physical name | New client contract | GTIN addition | Hierarchy add/change | Brand add/change (own) | URL addition | Competitor brand add/change |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| nm201 Maker | 〇 | — | — | — | — | △ |
| nm202 Brand | 〇 | — | — | △ | — | △ |
| nm203 GTIN | 〇 | 〇 | △ | 〇 | — | — |
| nm204 Client BU | 〇 | — | — | — | — | — |
| nm205 Contract brand | 〇 | — | — | 〇 | — | — |
| nm206 Competitor brand | 〇 | — | — | 〇 | — | 〇 |
| nm207 Product group | △ | — | — | — | — | — |
| nm208 Brand layer | 〇 | 〇 | — | 〇 | — | — |
| nm209 Loyalty rank | 〇 | — | — | 〇 | — | — |
| nm210 Contract×brand mapping | 〇 | — | — | 〇 | — | — |
| nm218 Brand page | 〇 | — | — | — | 〇 | — |
| nm227 Brand layer filterset | 〇 | — | 〇 | — | — | — |
| nm228 Brand group filterset | 〇 | — | 〇 | — | — | — |
| nm230 Brand group (general) | 〇 | — | — | — | — | △ |
| nm243 Brand group master | 〇 | 〇 | 〇 | 〇 | — | △ |

---

## What is AnTARES?

- **Brand filter definition tool** (successor to the former "FilterMaker")
- Filters defined in AnTARES are automatically fed into:
  - `branded_orders_ichiba` (branded order data)
  - AR (Rakuten Analytics = RMP)
  - Each Category Mart (CM)
- **AIO does not create filters directly** — DKD (Kobayashi) handles this
- User manual: https://rak.app.box.com/s/shtjxz3tzefwokml5wewfb2n415r0m7t

---

## Full Procedure

### Phase 1 — Definition (AIO + DKD) ← Monthly basis

1. AIO receives client requirements → spec check (no formal checklist)
2. AIO decides GTIN vs. filter boundary
3. DKD registers filters in AnTARES → issues filtersetcode
4. AIO updates AIO SBX master tables based on the update matrix above
5. AIO runs SBX verification queries to confirm registration

### Phase 2 — Production master update

> **Executed: tool by AIO operation based on client & DKD confirmation**

6. Reflect AIO SBX content (excluding deleteflg=1) into production TSV files
   (`rep-batch-data/ope_master_data/nsl/`)
7. Run validation — `exe_nsl_ope_master_validation.sh`

```
Validation order (FK dependency chain):
  nm201 → nm202 → nm203
  nm207 → nm230 → nm208 → nm227
  nm204 → nm205 → nm206 / nm208 / nm209 / nm210 / nm218
→ ALL PASS → proceed  /  ANY FAIL → abort
```

8. Run BQ load — `exe_load_nsl_hive_ope_master_data.sh`

```
Per TSV: empty check → column count check → upload GCS
         → TRUNCATE → BQ LOAD → row count check
```

### Phase 3 — Pipeline re-execution (GATD / ECFD)

9. Trigger brand_maint flows manually:
   - `brand_maint_01` — new client / brand definition change (25-month re-aggregation)
   - `brand_maint_03` — full 25-month re-aggregation (quarterly)
   - `brand_maint_04` — daily incremental catch-up

### Phase 4 — QA and dashboard update

10. GATD / ECFD checks aggregation results → reports to AIO
11. AIO verifies numeric diff, issues corrections if needed
12. AIO updates Databricks dashboards

---

## SBX Verification Queries

### 1. GTIN Registration Check
```sql
SELECT a.*, c.maker_name, b.product_brand_name
  ,d.product_group1_name, d.product_group2_name, d.product_group3_name, d.product_group4_name
FROM spdb-sbx.sbx_icb_homelife.nm203_tbl_product_master_v2 AS a
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm202_tbl_product_brand_master_v2 AS b ON a.product_brand_id = b.product_brand_id
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm201_tbl_maker_master_v2 AS c ON b.maker_id = c.maker_id
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm207_tbl_product_group_master_v2_deployment_fix AS d ON a.product_group_id = d.product_group_id
WHERE a.deleteflg = 0
```

### 2. Brand Layer Hierarchy Check (nm208 levels 1→4)
```sql
-- Joins nm208 4 times (layer_level_no 1/2/3/4) via parent_brand_layer_id chain
-- Full query in AIO doc: https://confluence.rakuten-it.com/confluence/spaces/AIO/pages/6863144149/
SELECT i.report_name, a.brand_layer_name AS lv1, f.brand_layer_name AS lv2
  ,g.brand_layer_name AS lv3, h.brand_layer_name AS lv4, h.ran_body_part
FROM (SELECT * FROM `spdb-sbx.sbx_icb_homelife.nm208_tbl_brand_layer_master_v2` WHERE layer_level_no=1 AND deleteflg=0) AS a
-- ... (see full query in source doc)
ORDER BY a.contract_brand_id, a.layer_level_no
```

### 3. Brand Registration Status Check
```sql
SELECT b.maker_name, a.product_brand_name, d.product_group1_name, d.product_group2_name
  ,e.filtersetcode, e.deleteflg
FROM spdb-sbx.sbx_icb_homelife.nm202_tbl_product_brand_master_v2 AS a
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm201_tbl_maker_master_v2 AS b ON a.maker_id = b.maker_id
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm230_tbl_brand_group_master_v2 AS c ON a.product_brand_id = c.product_brand_id
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm207_tbl_product_group_master_v2_deployment_fix AS d ON c.product_group_id = d.product_group_id
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm228_tbl_brand_group_filterset_v2 AS e ON c.product_group_id = e.product_group_id AND a.product_brand_id = e.product_brand_id
WHERE a.deleteflg = 0 AND a.product_brand_id >= 1000
ORDER BY b.maker_name, a.product_brand_name
```

---

## FK Dependency Map

```
nm201 ←── nm202 ←── nm203
              ↑         ↑
nm207 ←── nm230     nm208 ←── nm227
  (self-ref)    ↑    (self-ref)
nm204 ←── nm205 ───────╯ ←── nm209
              ↑                nm210
            nm206
nm204 ←── nm218 ←── nm208
```

---

## CM Migration Changes

| Item | Current (RMP/GATD) | Post-CM migration |
|---|---|---|
| Master definition | AIO (Sato) → SBX authoring | AIO (Sato) → unchanged |
| Master update | GATD (Onishi) | tool by AIO operation based on client & DKD confirmation |
| Filter registration | DKD (Kobayashi) → AnTARES | DKD (Kobayashi) → unchanged (incl. CM filterset) |
| Pipeline | Azkaban brand_maint | Databricks Job (equivalent design) |
| shop_group | `resources/shop_groups/{client}.yml` | Port YAMLs to CM shop-group master |

---

---

<!-- ============================================================ -->
<!-- JAPANESE VERSION                                              -->
<!-- ============================================================ -->

# 🇯🇵 日本語

## チーム役割

| チーム | 担当者 | 役割 |
|---|---|---|
| **AIO** | 佐藤 Shinjiro（Shinn） | 依頼仕様チェック、フィルター仕様決め、AIO SBXマスタ更新（基本月1）、数値差分確認、ダッシュボード更新 |
| **DKD** | 小林 | AnTARES フィルター作成、filtersetcode 発行、プロダクトグループ追加 |
| **GATD → ECFD** | 大西 → Erin | 本番 TSV 更新、バリデーション、BQ ロード、パイプライン実行 |

---

## AIO（佐藤）の作業詳細 — Phase 1

### 1. 依頼仕様詳細チェック
- 過去登録内容との不整合がないかチェック
- 階層として論理破綻していないかチェック
- 市場で既に販売しているブランドかどうか確認
- **注意：チェックリストなし — 判断ベースのレビュー**

### 2. フィルター仕様決め
- **filtersetcode**（AnTARES）と **GTIN（RANコード）** の境界を決定
- どこまでをフィルターで作成し、どこからを GTIN で判定するかを決める

### 3. マスタ更新（基本月1回）
- `spdb-sbx.sbx_icb_homelife.*` の AIO SBX マスタテーブルを更新
- 操作種別ごとの更新対象テーブルは下記の更新マトリクスを参照

### 4. 数値差分確認
- パイプライン実行後、依頼内容に応じて数値の差分がどこで出ているかを検証

### 5. プロダクトグループ追加・変更仕様決め
- 既存プロダクトグループで対応できない新規クライアントが来た場合に対応（DKD と連携）
- **依頼マニュアル：** https://confluence.rakuten-it.com/confluence/display/DM/Product+Group+%7C+00+%7C+REQUEST+MANUAL
- **依頼フォーム：** https://confluence.rakuten-it.com/confluence/display/DM/Product+Group+%7C+01+%7C+REQUEST+FORM

---

## AIO SBX マスタテーブル（`spdb-sbx.sbx_icb_homelife.*`）

全テーブル共通の監査カラム：`reg_datetime`、`upd_datetime`、`deleteflg`

> **⚠️ 重要：`deleteflg = 1` の行は SBX → 本番取り込み時に必ず除外される。**

| No | 物理名 | 論理名（佐藤） | DSMS更新対象 |
|---|---|---|:---:|
| 1 | nm201_tbl_maker_master_v2 | メーカーマスタ | 〇 |
| 2 | nm202_tbl_product_brand_master_v2 | ブランドマスタ | 〇 |
| 3 | nm203_tbl_product_master_v2 | GTINマスタ | 〇 |
| 4 | nm204_tbl_contracted_bu_master_v2 | 契約クライアントマスタ | 〇 |
| 5 | nm205_tbl_contracted_brand_master_v2 | 契約ブランドマスタ | 〇 |
| 6 | nm206_tbl_competing_brand_master_v2 | 競合ブランドマスタ | 〇 |
| 7 | nm207_tbl_product_group_master_v2 | プロダクトグループマスタ | 〇 |
| 8 | nm208_tbl_brand_layer_master_v2 | ブランド階層マスタ | 〇 |
| 9 | nm209_tbl_brand_loyalty_rank_master_v2 | 契約ブランド会員ランク管理マスタ | 〇 |
| 10 | nm210_tbl_contract_product_brand_mapping_master | 契約ブランド対応マスタ | 〇 |
| 11 | nm218_tbl_brand_page_master_v2 | ブランドページマスタ | 〇 |
| 12 | nm219_tbl_device_master_v2 | deviceマスタ | — |
| 13 | nm220_tbl_page_class_master_v2 | ページクラスマスタ | — |
| 14 | nm223_tbl_member_attribute_master_v2 | 性・年代・都道府県組み合わせマスタ | — |
| 15 | nm225_tbl_service_master_v2 | サービスマスタ | — |
| 16 | nm226_tbl_selling_form_master_v2 | 販売種別マスタ | — |
| 17 | nm227_tbl_brand_layer_filterset_v2 | ブランド階層フィルターマスタ | 〇 |
| 18 | nm228_tbl_brand_group_filterset_v2 | ブランド名寄せ辞書マスタ対応（契約） | 〇 |
| 19 | nm230_tbl_brand_group_master_v2 | ブランド名寄せ辞書マスタ（汎用） | 〇 |
| 20 | nm243_tbl_app_type_master_v2 | ブランドグループマスタ | 〇 |

---

## ⑤ 更新マトリクス — 操作種別×テーブル更新対象

〇=必須　△=場合による　—=不要

| 物理名 | 新規メーカー契約 | GTIN追加 | 階層追加/変更 | ブランド追加/変更（自社） | URL追加 | 競合ブランド追加/変更 |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| nm201 メーカー | 〇 | — | — | — | — | △ |
| nm202 ブランド | 〇 | — | — | △ | — | △ |
| nm203 GTIN | 〇 | 〇 | △ | 〇 | — | — |
| nm204 契約クライアント | 〇 | — | — | — | — | — |
| nm205 契約ブランド | 〇 | — | — | 〇 | — | — |
| nm206 競合ブランド | 〇 | — | — | 〇 | — | 〇 |
| nm207 プロダクトグループ | △ | — | — | — | — | — |
| nm208 ブランド階層 | 〇 | 〇 | — | 〇 | — | — |
| nm209 会員ランク管理 | 〇 | — | — | 〇 | — | — |
| nm210 契約ブランド対応 | 〇 | — | — | 〇 | — | — |
| nm218 ブランドページ | 〇 | — | — | — | 〇 | — |
| nm227 ブランド階層フィルター | 〇 | — | 〇 | — | — | — |
| nm228 ブランド名寄せ（契約） | 〇 | — | 〇 | — | — | — |
| nm230 ブランド名寄せ（汎用） | 〇 | — | — | — | — | △ |
| nm243 ブランドグループ | 〇 | 〇 | 〇 | 〇 | — | △ |

---

## ③ AnTARES とは

- **ブランドフィルター定義ツール**（旧称：フィルターメーカー）の最新版
- AnTARES で定義したフィルターをフィード設定することで以下に自動連携：
  - `branded_orders_ichiba`（ブランド別注文データ）
  - AR（Rakuten Analytics = RMP）
  - 各種カテゴリーマート（CM）
- **AIO はフィルターを直接作成しない** — DKD（小林）が担当
- ユーザーマニュアル：https://rak.app.box.com/s/shtjxz3tzefwokml5wewfb2n415r0m7t

---

## 全体手順

### Phase 1 — 定義（AIO + DKD）← 基本月1回

1. AIO が依頼仕様を受領 → 仕様チェック（チェックリストなし）
2. AIO が GTIN vs フィルターの境界を決定
3. DKD が AnTARES にフィルター登録 → filtersetcode 発行
4. AIO が上記更新マトリクスに基づき AIO SBX マスタを更新
5. AIO が SBX 検証クエリで登録内容を確認

### Phase 2 — 本番マスタ反映

> **実施者：クライアント・DKD 確認に基づき AIO がツール操作で実施**

6. AIO SBX の内容（deleteflg=1 除外）を本番 TSV に反映
   （`rep-batch-data/ope_master_data/nsl/`）
7. バリデーション実行 — `exe_nsl_ope_master_validation.sh`

```
バリデーション順序（FK依存チェーン）：
  nm201 → nm202 → nm203
  nm207 → nm230 → nm208 → nm227
  nm204 → nm205 → nm206 / nm208 / nm209 / nm210 / nm218
→ 全 PASS → 次へ　/ 1件でも FAIL → abort
```

8. BQ ロード実行 — `exe_load_nsl_hive_ope_master_data.sh`

```
各TSVごと: 空ファイルチェック → 列数チェック → GCSアップロード
           → TRUNCATE → BQ LOAD → 行数チェック
```

### Phase 3 — パイプライン再実行（GATD / ECFD）

9. brand_maint フローを手動トリガー：
   - `brand_maint_01` — 新規クライアント / ブランド定義変更（25ヶ月再集計）
   - `brand_maint_03` — 25ヶ月フル再集計（四半期）
   - `brand_maint_04` — 日次インクリメンタル catch-up

### Phase 4 — QA・ダッシュボード更新

10. GATD / ECFD が集計結果の品質チェック → AIO に報告
11. AIO が数値差分を確認、修正指示があれば対応
12. AIO が Databricks ダッシュボードを更新

---

## SBX 検証クエリ

### 1. 汎用JAN登録内容確認クエリ
```sql
SELECT a.*, c.maker_name, b.product_brand_name
  ,d.product_group1_name, d.product_group2_name, d.product_group3_name, d.product_group4_name
FROM spdb-sbx.sbx_icb_homelife.nm203_tbl_product_master_v2 AS a
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm202_tbl_product_brand_master_v2 AS b ON a.product_brand_id = b.product_brand_id
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm201_tbl_maker_master_v2 AS c ON b.maker_id = c.maker_id
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm207_tbl_product_group_master_v2_deployment_fix AS d ON a.product_group_id = d.product_group_id
WHERE a.deleteflg = 0
```

### 2. 登録階層チェッククエリ（nm208 level 1→2→3→4）
```sql
-- nm208 を parent_brand_layer_id チェーンで4回JOIN して階層全体を確認
-- 完全クエリは AIO ドキュメント参照：https://confluence.rakuten-it.com/confluence/spaces/AIO/pages/6863144149/
SELECT i.report_name, a.brand_layer_name AS lv1, f.brand_layer_name AS lv2
  ,g.brand_layer_name AS lv3, h.brand_layer_name AS lv4, h.ran_body_part
FROM (SELECT * FROM `spdb-sbx.sbx_icb_homelife.nm208_tbl_brand_layer_master_v2` WHERE layer_level_no=1 AND deleteflg=0) AS a
-- ... （完全クエリはソースドキュメント参照）
ORDER BY a.contract_brand_id, a.layer_level_no
```

### 3. 汎用ブランド登録状況チェッククエリ
```sql
SELECT b.maker_name, a.product_brand_name, d.product_group1_name, d.product_group2_name
  ,e.filtersetcode, e.deleteflg
FROM spdb-sbx.sbx_icb_homelife.nm202_tbl_product_brand_master_v2 AS a
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm201_tbl_maker_master_v2 AS b ON a.maker_id = b.maker_id
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm230_tbl_brand_group_master_v2 AS c ON a.product_brand_id = c.product_brand_id
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm207_tbl_product_group_master_v2_deployment_fix AS d ON c.product_group_id = d.product_group_id
LEFT JOIN spdb-sbx.sbx_icb_homelife.nm228_tbl_brand_group_filterset_v2 AS e ON c.product_group_id = e.product_group_id AND a.product_brand_id = e.product_brand_id
WHERE a.deleteflg = 0 AND a.product_brand_id >= 1000
ORDER BY b.maker_name, a.product_brand_name
```

---

## FK 依存マップ

```
nm201 ←── nm202 ←── nm203
              ↑         ↑
nm207 ←── nm230     nm208 ←── nm227
 (自己参照)    ↑    (自己参照)
nm204 ←── nm205 ───────╯ ←── nm209
              ↑                nm210
            nm206
nm204 ←── nm218 ←── nm208
```

---

## CM 移行後の変化点

| 項目 | 現状（RMP/GATD） | CM移行後 |
|---|---|---|
| マスタ定義 | AIO（佐藤） → SBXオーサリング | AIO（佐藤） → 変わらず |
| マスタ反映 | GATD（大西） | クライアント・DKD確認に基づきAIOがツール操作で実施 |
| フィルター登録 | DKD（小林） → AnTARES | DKD（小林） → 変わらず（CM filterset含む） |
| パイプライン | Azkaban brand_maint | Databricks Job（同等設計） |
| shop_group | `resources/shop_groups/{client}.yml` | CM shop-group master に移管 |

---

## 関連リンク / Related Links

| リソース | URL |
|---|---|
| AIO 手順書（一次ソース） | https://confluence.rakuten-it.com/confluence/spaces/AIO/pages/6863144149/ |
| AnTARES ユーザーマニュアル | https://rak.app.box.com/s/shtjxz3tzefwokml5wewfb2n415r0m7t |
| Product Group 依頼マニュアル | https://confluence.rakuten-it.com/confluence/display/DM/Product+Group+%7C+00+%7C+REQUEST+MANUAL |
| Product Group 依頼フォーム | https://confluence.rakuten-it.com/confluence/display/DM/Product+Group+%7C+01+%7C+REQUEST+FORM |
| Jira Epic | https://jira.rakuten-it.com/jira/browse/CONRAT-44285 |
| CM gap analysis (Confluence) | https://confluence.rakuten-it.com/confluence/spaces/~hyonwoo.song/pages/6857878012 |
