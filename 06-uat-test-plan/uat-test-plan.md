# BA Deliverable 6: UAT Test Plan

*Sarika · November 2026*

**All 10 data tests pass and every value shown on the dashboard (7 of 7 display checks) matches the data, but two Must/Should requirements are not yet met: low-volume states are not flagged, and freight % of item value is not shown.** Six interactive tests remain for the business tester to run on the live dashboard.

Full test log: [uat-test-cases.csv](uat-test-cases.csv) · Dashboard: [Tableau Public](https://public.tableau.com/app/profile/sarika.gaind6780/viz/AWSDeliveryAnalysis/DeliveryPerformance)

## Scope

| | Detail |
| --- | --- |
| System under test | Delivery Performance dashboard v1 on Tableau Public, built on the order-level extract (96,470 delivered orders) |
| In scope | KPI tiles (on-time rate, delivered orders, average delay, freight per order), late rate by state map, reviews by delivery status, 10 worst sellers, state click filter, public access |
| Out of scope for v1 | Dispatch-band view (US-04), route view (US-05) and at-risk flag (US-07). Their data is tested (DT-07 to DT-09) so the views can be built in Sprint 1 and 2 |
| Test data | `tableau_orders.csv`, `tableau_seller_orders.csv`, `b5_seller_performance.csv` from the pipeline |
| Expected results | Verified project findings and the BRD KPI definitions |

## Approach

| Test type | What it checks | Who runs it | Count |
| --- | --- | --- | --- |
| Data (DT) | Numbers behind the dashboard match the KPI definitions | Scripted against the extract | 10 |
| Display (DV) | Each tile and chart shows the right value and wording | Checked against the dashboard screenshot | 9 |
| Interactive (UI) | Filter, tooltips, public access, phone layout | Business tester on the live dashboard | 6 |
| Non-functional (NF) | Refresh, privacy | Evidence review | 2 |

**Entry criteria:** pipeline build complete, all 90 dbt tests passing, dashboard published.
**Exit criteria:** all Must tests pass; any open defect has an owner and a target sprint.

## Results summary

| Status | Count |
| --- | --- |
| Pass | 17 |
| Pass with defect | 1 |
| Fail | 2 |
| To run | 6 (one is expected to fail, linked to DEF-02) |
| Blocked | 1 (daily refresh: AWS resources were deleted after the project; evidence on file) |
| **Total** | **27** |

## Test cases (summary)

| ID | User story | Test | Expected | Status |
| --- | --- | --- | --- | --- |
| DT-01 to DT-03 | US-01 | Scope, late orders, on-time rate | 96,470; 6,534; 93.23% | Pass |
| DT-04 | US-02 | Average and median delay | 10.62 days; 7 | Pass |
| DT-05 | US-08 | Freight per order and % of item value | R$ 22.79; 16.63% | Pass |
| DT-06 | US-06 | Reviews by status | 2.27 vs 4.29 | Pass |
| DT-07, DT-09 | US-04 | Dispatch band rates; sellers with 20+ orders | 3.81% to 21.39%; 804 | Pass |
| DT-08 | US-05 | Route late rates | 8.03% vs 4.52% | Pass |
| DT-10 | US-01 | Workbook reconciliation | 18 of 18 | Pass |
| DV-01 to DV-04 | US-01, 02, 08 | KPI tiles | 93.23%; 96,470; 10.62 days; R$22.79 | Pass |
| DV-05 | US-02 | Map color range | 2.76% (AM) to 21.41% (AL) | Pass |
| DV-06 | US-06 | Reviews chart | 2.27 vs 4.29, association wording | Pass with defect (DEF-01) |
| DV-07 | US-04 | 10 worst sellers | Top 33.3% (SP); 7 of 10 in SP | Pass |
| DV-08 | US-03 | Low-volume flag on map | RR, AP, AC flagged | **Fail (DEF-02)** |
| DV-09 | US-08 | Freight % of item value shown | 16.63% | **Fail (DEF-03)** |
| UI-01 | US-02 | Click AL | 78.59%; 397 orders; 9.54 days; R$38.58 | To run |
| UI-02 | US-02 | Clear filter | Back to 93.23% and 96,470 | To run |
| UI-03 | US-02 | Hover SP | 4.49% late | To run |
| UI-04 | US-03 | Click RR | 36.40-day delay from 5 late orders, with a warning | To run (expected Fail) |
| UI-05 | US-01 | Public link, signed out | Loads without sign-in | To run |
| UI-06 | US-01 | Phone layout | Readable, no overlap | To run |
| NF-01 | US-01 | Daily refresh | DAG succeeds | Blocked (evidence on file) |
| NF-02 | US-01 | No customer data | None shown | Pass |

## Defect log

| ID | Severity | Found in | Description | Expected | Owner | Target |
| --- | --- | --- | --- | --- | --- | --- |
| DEF-01 | Low | DV-06 | Reviews chart title says late deliveries "cut review scores in half", which reads as a proven cause | Association wording, e.g. "Late orders average 2.27★ vs 4.29★ on time" | Data / BI Team | Next publish |
| DEF-02 | Medium | DV-08, UI-04 | Low-volume states (RR 41, AP 67, AC 80 orders) have no flag. Clicking RR shows a 36.40-day average delay based on only 5 late orders | Visible low-volume flag (BR-04) | Data / BI Team | Sprint 1 (US-03) |
| DEF-03 | Low | DV-09 | Freight as % of item value (16.63%) is not shown | Shown next to freight per order (BR-08) | Data / BI Team | Sprint 2 (US-08) |


## Traceability

| User story | Tests | Result |
| --- | --- | --- |
| US-01 KPI definitions | DT-01 to DT-03, DT-10, DV-01, DV-02, UI-05, UI-06, NF-01, NF-02 | Pass; 2 to run; refresh blocked |
| US-02 On-time and delay overview | DT-04, DV-03, DV-05, UI-01 to UI-03 | Pass; 3 to run |
| US-03 Low-volume flag | DV-08, UI-04 | Fail (DEF-02) |
| US-04 Seller scorecard | DT-07, DT-09, DV-07 | Data ready; full scorecard in Sprint 1 |
| US-05 Route comparison | DT-08 | Data ready; view in Sprint 1 |
| US-06 Review impact | DT-06, DV-06 | Pass with defect (DEF-01) |
| US-07 At-risk flag | — | Not in v1; Sprint 2 |
| US-08 Freight by state | DT-05, DV-04, DV-09 | Partial (DEF-03) |

## Sign-off

| Role | Name | Decision | Date |
| --- | --- | --- | --- |
| Business owner (Operations Lead) | | Accept v1 with DEF-02 and DEF-03 scheduled | |
| Data / BI Team | | | |
| Business Analyst | Sarika | Prepared the plan | 2026-11-17 |

Recommendation: accept v1 for KPI monitoring; do not use state rankings for decisions until DEF-02 is fixed in Sprint 1.
