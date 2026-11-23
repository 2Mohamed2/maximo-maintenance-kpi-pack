# Maximo Maintenance KPI Pack (SMRP)

*Created by Mohamed Chihaoui*

One BIRT report for IBM Maximo 7.6 (and Maximo Manage) that measures the maintenance and reliability KPIs of the
**Society for Maintenance & Reliability Professionals (SMRP) Best Practices metrics** from Maximo's own data:
work orders, assets, asset downtime, labour, inventory and storerooms. It covers all five SMRP pillars. It is
read-only and runs on **SQL Server, Oracle and Db2** from the same file.

You import only `maximo_smrp_kpi.rptdesign` into Maximo. `smrp_sql.js` is the SQL source that `build.py` builds into
the report: it is kept as its own file so the statements can be tested on each database and reviewed by a DBA.

## What is in it

| Section | Content |
|---|---|
| Header | Period, site, Maximo version, database. Eight headline tiles: maintenance cost / RAV, PM & PdM compliance, planned work, emergency work, schedule compliance, MTBF, MTTR, planned backlog |
| Scorecard | 40 KPIs by SMRP pillar, each with its value, a common reference value, a status (On target, Watch, Info, No data, Outside Maximo) and how it is measured in Maximo |
| 1 Business & management | Cost by work category: labour, materials, services, tools, share of hours and cost, planned share. Monthly hours by category (chart) |
| 2 Equipment reliability | Bad actors (top 12 assets by failures, with MTBF, MTTR, cost, failure class) and failure classes |
| 3 Work management | Work types, KPIs by site (PM and schedule compliance, emergency share, backlog), open work by age and status, the most overdue preventive work orders |
| 4 Stores | Storerooms: value, inactive stock, stock-outs, issues, turns |
| Appendix | Method, definitions and the tables read |

### KPIs

- **Business & management:** replacement asset value (RAV), total maintenance cost, maintenance cost as % of RAV, RAV per craft worker, stores value as % of RAV, and maintenance unit cost (outside Maximo).
- **Manufacturing process reliability:** OEE and TEEP (outside Maximo: they need production data).
- **Equipment reliability:** criticality coverage, total downtime, unscheduled downtime %, MTBF, MTTR, failures with a failure class.
- **Organization & leadership:** rework (repeat failures within 30 days), contractor cost %, overtime %.
- **Work management:**
  - **Hours and cost mix:** preventive, predictive, corrective and emergency hours %; preventive and corrective cost %.
  - **Planning:** planned work %; actual-to-estimate hours and cost.
  - **Compliance and PM yield:** schedule compliance (work orders and hours), PM & PdM compliance, PM & PdM overdue, PM & PdM yield.
  - **Open work:** work order cycle time, aging, planned and ready backlog (weeks).
  - **Stores:** inventory turns, stock-outs, inactive stock, storeroom records accuracy, consignment items.

## Parameters

| Parameter | Default | Meaning |
|---|---|---|
| Site | blank | One site, or all sites |
| Months | 12 | Length of the period |
| End_Month | blank | Last month of the period (YYYY-MM). Blank: the last complete month. If the database has no regular work in the last 12 months (a test or archive copy), the period ends at the last month with regular activity, and the header says so |
| Preventive_Types | PM,PMCAL,CAL | Work types counted as preventive (PM-generated work orders always are) |
| Predictive_Types | PDM,CBM | Predictive and condition-based work types |
| Corrective_Types | CM,CMCAL | Corrective work types |
| Emergency_Types | EM,EMCAL | Emergency (reactive) work types |

Set the work type parameters to your own work types before you read the percentages.

## Install

1. Reports (Administration) > New Report: file name `maximo_smrp_kpi.rptdesign`, application of your choice (for example WOTRACK), save.
2. Import Report, choose the file, then Generate Request Page.
3. Preview, or add it to the application's report menu.

No database objects and no DBA rights are needed: every statement is a SELECT that runs with the Maximo database login.

## Notes

- Statuses are read through their synonyms (SYNONYMDOMAIN), so renamed statuses count correctly.
- Common reference values are industry rules of thumb often quoted with the SMRP metrics, not SMRP targets. Agree targets locally and watch the trend.
- SMRP metric numbering differs between editions, so the report uses the metric names.
- Tested inside Maximo 7.6 on SQL Server, Oracle and Db2.

## Build

```
python build.py            # writes dist/maximo_smrp_kpi.rptdesign
```

Created by Mohamed Chihaoui.
