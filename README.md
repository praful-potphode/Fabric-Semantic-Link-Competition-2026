# Fabric-Semantic-Link-Competition-2026
🗄️ Programmatic version control for Power BI semantic models — unlimited snapshots, field-level diff, and RLS auditing via Semantic Link &amp; Delta Lake on Microsoft Fabric.

# 🗄️ Semantic Model Version Control & Change Diff

![Microsoft Fabric](https://img.shields.io/badge/Microsoft%20Fabric-0E7A6A?style=flat&logo=microsoft&logoColor=white)
![Semantic Link](https://img.shields.io/badge/Semantic%20Link-SemPy-1AAF87?style=flat)
![Delta Lake](https://img.shields.io/badge/Delta%20Lake-OneLake-3ECFAA?style=flat)
![Python](https://img.shields.io/badge/Python-PySpark-blue?style=flat&logo=python&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat)

> Programmatic version control for Power BI semantic models — unlimited snapshot history, field-level diff, and RLS security auditing, built entirely within Microsoft Fabric.

---

## 🚫 The problem

Power BI's built-in version history is UI-only — capped at 5 snapshots, 14-day retention, with **no REST API, XMLA, or SemPy surface**. There is no `GET /semanticModels/{id}/versions` endpoint. Enterprise teams have no programmatic way to list, compare, or audit model changes.

---

## 💡 The solution

This Fabric notebook uses `sempy.fabric` to capture point-in-time metadata snapshots of all five artifact types and persists them as **Delta tables in OneLake** via Apache Spark. A field-level diff engine then compares any two run IDs and classifies every change as `ADDED`, `REMOVED`, or `MODIFIED` with before/after values.

**Architecture:**
`Semantic Model` → `SemPy + DMV` → `Snapshot Engine` → `Delta Lake (OneLake)` → `Change Diff`

---

## ✨ Key features

| Feature | Detail |
|---|---|
| 🔗 Dual-path extraction | `fabric.list_*()` API with automatic DMV fallback — works across PPU, Premium & Fabric capacities |
| ♾️ Unlimited history | Delta append mode — no 5-version or 14-day limit |
| 🔍 Field-level diff | Compares DAX expressions, format strings, cardinality, RLS filter expressions |
| 🔒 RLS security audit | Captures role definitions + member assignments — detects unauthorized changes |
| 🎨 Fabric-themed UI | ipywidgets + Fabric-branded HTML output with live progress bars and collapsible diff panels |
| ⏰ Schedulable | Embed in a Fabric Data Pipeline for automated daily governance snapshots |

---

## 🔍 Diff output

Each `compute_diff()` call returns three change types:

- ➕ **ADDED** — artifact exists in new snapshot but not in old
- ➖ **REMOVED** — artifact exists in old snapshot but not in new
- ✏️ **MODIFIED** — same key, different field values (shows field name + before/after)

---

## 📦 Artifacts covered

| Artifact | Key columns | Compare columns |
|---|---|---|
| 📐 Measures | `Name, Table` | `Expression, Description, FormatString, IsHidden` |
| 🗂️ Columns | `Name, Table` | `DataType, ColumnType, Expression, IsHidden` |
| 🔗 Relationships | `FromTable, FromColumn, ToTable, ToColumn` | `FromCardinality, ToCardinality, CrossFilter, IsActive` |
| 📋 Tables | `Name` | `IsHidden, StorageMode` |
| 🔒 RLS Roles | `Name` | `ModelPermission, FilterExpression` |

---

## 🚀 Quick start

```python
# 1. Attach a Lakehouse to the notebook (Explorer panel → Add data items)

# 2. Install dependency
%pip install semantic-link-labs -q

# 3. Set your model
DATASET_NAME   = "YourModelName"
WORKSPACE_NAME = "YourWorkspace"

# 4. Take a snapshot
run_id, results = take_snapshot()

# 5. After making model changes, take another snapshot, then diff
run_new, run_old = _get_last_two_runs()
compute_diff(run_new, run_old)
```

---

## 🛠️ Requirements

- Microsoft Fabric workspace (F SKU, P SKU, or PPU)
- Lakehouse attached to the notebook (for Delta table writes)
- XMLA read access on the semantic model
- Runtime: Synapse PySpark — Spark 3.4+
- Package: `semantic-link-labs` (`%pip install semantic-link-labs`)

---

## 🗂️ Delta tables created

| Table name | Contents |
|---|---|
| `sm_snapshot_runs` | Run registry — one row per snapshot execution |
| `sm_snapshot_measures` | All DAX measures with expressions |
| `sm_snapshot_columns` | Column schema including data types |
| `sm_snapshot_relationships` | Full relationship graph |
| `sm_snapshot_tables` | Table list and storage modes |
| `sm_snapshot_roles` | RLS role definitions and member assignments |

---

## 🏆 Competition

Submitted to the **Microsoft Fabric Semantic Link Developer Experience Competition** — Notebook Gallery, Category: Community Contests.

---

## 👤 Author

**Praful Potphode** · Microsoft Fabric Community Super User · [LinkedIn](https://www.linkedin.com/in/praful-p-912349241/)
      
