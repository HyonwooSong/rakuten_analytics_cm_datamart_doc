# rakuten_analytics_cm_datamart_doc

Public reference documentation for the **RMPREP × CategoryMart (RA-CM)** migration project.

> **Project:** [CONRAT-44285 [DS] RMPREP × CategoryMart](https://jira.rakuten-it.com/jira/browse/CONRAT-44285)  
> **Owner:** Song, Hyon Woo

---

## Documents

| File | What it covers |
|---|---|
| [`ope_master_update_procedure_bilingual.md`](ope_master_update_procedure_bilingual.md) | **ope-master update & maintenance** — bilingual (EN/JA) full procedure: team roles, AIO tasks, update matrix, AnTARES, validation, BQ load, pipeline re-execution |
| [`ope_master_update_flowchart.md`](ope_master_update_flowchart.md) | **Visual flowcharts** — overall flow (Mermaid), team sequence diagram, update frequency chart, FK dependency graph |
| [`ope_master_sample_case_with_flow.md`](ope_master_sample_case_with_flow.md) | **Sample case: P&G new client onboarding** — end-to-end procedure including Product Group addition request, SBX master updates, pipeline trigger, timeline Gantt chart |
| [`cm_migration_gap_analysis_origin_files.md`](cm_migration_gap_analysis_origin_files.md) | **CM migration gap analysis** — four focus areas (shop_group / sub_brand / competitor definition / loyalty rank), each traced to its very origin file (ope-master TSV or reporting YAML) |
| [`ra_cm_design_overview.md`](ra_cm_design_overview.md) | **RA-CM overall design overview** |

---

## Quick Reference — Team Roles

| Team | Person | Role |
|---|---|---|
| **AIO** | 佐藤 Shinjiro (Shinn) | Requirements, AIO SBX master authoring (monthly), numeric diff verification, dashboard update |
| **DKD** | 小林 | AnTARES filter creation, filtersetcode issuance, Product Group additions (2–4 weeks) |
| **GATD/AMD** | 大西 | Production TSV update, validation, BQ load, pipeline execution |

---

## Key Rules

| Rule | Detail |
|---|---|
| `deleteflg = 1` | Rows with deleteflg=1 in AIO SBX are **excluded** when loading to production |
| Update frequency | AIO SBX master updates: **monthly (基本月1回)** |
| PG addition lead time | Product Group requests processed by DKD in **2–4 weeks** |
| Validation | All 13 masters validated in FK dependency order before BQ load — any failure = abort |

---

## Related Resources

| Resource | Link |
|---|---|
| AIO Procedure Doc | https://confluence.rakuten-it.com/confluence/spaces/AIO/pages/6863144149/ |
| Product Group Request Manual | https://confluence.rakuten-it.com/confluence/spaces/DM/pages/5973552358/ |
| Product Group Request Form | https://confluence.rakuten-it.com/confluence/x/ksDkYwE |
| AnTARES Manual | https://rak.app.box.com/s/shtjxz3tzefwokml5wewfb2n415r0m7t |
| CM Gap Analysis (Confluence) | https://confluence.rakuten-it.com/confluence/spaces/~hyonwoo.song/pages/6857878012 |
| Asahi Beer Pilot (CONRAT-44595) | https://jira.rakuten-it.com/jira/browse/CONRAT-44595 |
| P&G New Client (CONRAT-44597) | https://jira.rakuten-it.com/jira/browse/CONRAT-44597 |
