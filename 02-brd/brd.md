# BA Deliverable 2: Business Requirements Document

*Sarika · November 2026*

## Document control

| Field | Value |
| --- | --- |
| Project | Delivery Performance Analytics (Olist marketplace) |
| Document | Business Requirements Document (BRD) |
| Version | 1.0 (draft for stakeholder review) |
| Author | Sarika, Business Analyst |
| Business owner | Operations Lead |
| Executive sponsor | COO |
| Related documents | [Deliverable 1: Problem Statement & Stakeholder Map](../01-problem-statement-stakeholders/problem-statement-stakeholders.md); [Case Study](../case-study.md) |

## Executive summary

**The marketplace needs one trusted, automated view of delivery performance, plus a seller dispatch standard, to reduce late deliveries where they concentrate.** Today 93.23% of 96,470 delivered orders arrive on time, but the 6,534 late orders run 10.62 days late on average and are associated with sharply worse reviews (2.27★ vs. 4.29★).

This BRD defines what the business needs: the objectives, scope, requirements and KPIs that the dashboard, the dispatch SLA and promised-date changes must meet. Solution design and implementation details are out of scope for this document.

## Business objectives

Five objectives, each tied to a verified finding; every requirement and KPI below traces back to one of them.

| ID | Objective | Evidence (finding) | Owner |
| --- | --- | --- | --- |
| BO-1 | Reduce late deliveries associated with slow seller dispatch | 7+ day dispatch: 21.39% late vs. 3.81% at 0–1 days | Seller Success Manager |
| BO-2 | Align promised delivery dates with real transit times by route and state | Cross-state orders are 76.1% of late orders; AL is 78.59% on time | Logistics Manager |
| BO-3 | Protect customer satisfaction on orders at risk of being late | Late orders: 62.42% get 1–2 star reviews vs. 9.27% on time | CX Lead |
| BO-4 | Give all teams one automated, trusted view of delivery performance | No shared KPI definitions today; pipeline has 90 passing dbt tests to build on | Operations Lead |
| BO-5 | Keep freight cost under control while delivery improves | Freight averages R$ 22.79 per order, 16.63% of item value | Logistics Manager + Finance |

## Scope

**In scope**

- Delivered orders with a customer delivery date: 96,470 orders, purchased 2016-09-15 to 2018-08-29, across 27 states and 2,970 sellers.
- Delivery KPIs: on-time rate, delay, dispatch time, route (cross-state vs. same-state), freight, and review score.
- Views by customer state, seller, route and purchase month.
- A seller dispatch scorecard and an at-risk order flag.
- KPI definitions, data quality checks and the daily refresh of the existing pipeline.

**Out of scope**

- Orders in other statuses (2,963) and delivered orders without a delivery date (8).
- Carrier selection or carrier performance: no carrier field exists in the data.
- Changes to the checkout or promised-date algorithm itself; this BRD defines the need, not the build.
- Customer messaging tools; BR-06 defines the flag that such tools would use.
- Pricing, refunds and seller penalties beyond the dispatch SLA definition.

## Business requirements

Priorities use MoSCoW (Must, Should, Could, Won't). Each requirement traces to an objective; user stories in Deliverable 3 will break these down further.

| ID | Requirement | Priority | Objective | Stakeholder |
| --- | --- | --- | --- | --- |
| BR-01 | The business must see on-time rate and average delay overall, by customer state and by purchase month. | Must | BO-4 | Operations Lead |
| BR-02 | The business must see a seller dispatch scorecard showing average dispatch days, on-time rate and order count for each seller with 20+ delivered orders. | Must | BO-1 | Seller Success Manager |
| BR-03 | The business must compare late rate and average days to deliver for cross-state vs. same-state orders. | Must | BO-2 | Logistics Manager |
| BR-04 | Any state result based on fewer than 100 orders must be visibly flagged as low-volume. | Must | BO-4 | Data / BI Team |
| BR-05 | KPI definitions must be documented once and applied identically in every view. | Must | BO-4 | Data / BI Team |
| BR-06 | The business should be able to identify at-risk orders before the promised date. Proposed starting rule: dispatch in the 7+ day band, or a cross-state route; final rule to be agreed with CX and Logistics. | Should | BO-3 | CX Lead |
| BR-07 | The business should see average review score and 1–2 star share for late vs. on-time orders. | Should | BO-3 | CX Lead |
| BR-08 | The business should see average freight per order and freight % of item value by state. | Should | BO-5 | Finance / FP&A |
| BR-09 | The business could see late rate by purchase month, with peak periods highlighted for capacity planning. | Could | BO-1, BO-2 | Operations Lead |
| BR-10 | Carrier-level performance reporting. | Won't (this phase) | — | Logistics Manager |

BR-10 is deferred because the data has no carrier field.

## Non-functional requirements

| ID | Requirement | Priority |
| --- | --- | --- |
| NFR-01 | Data refreshes daily through the existing Airflow DAG. | Must |
| NFR-02 | All dbt data tests (currently 90) must pass before refreshed data is published. | Must |
| NFR-03 | The solution uses free-tier or free tools only (current AWS cost: $0.00). | Must |
| NFR-04 | The public dashboard shows no customer-identifying information. | Must |
| NFR-05 | Every published figure can be reproduced from a documented query. | Should |
| NFR-06 | End-to-end pipeline run time is logged (not logged today; dbt build alone takes 108.22 s). | Should |

## KPIs and definitions

All KPIs cover delivered orders with a customer delivery date, compare dates rather than timestamps, and use the customer's state unless stated otherwise. Targets are **proposed and directional**; numeric targets will be set by each owner after a one-quarter pilot.

| KPI | Definition | Formula | Baseline | Proposed target | Owner |
| --- | --- | --- | --- | --- | --- |
| On-time delivery rate | Share of orders delivered on or before the promised date | On-time orders ÷ delivered orders | 93.23% | Increase | Operations Lead |
| Average delay | Days late, for late orders only | Avg. (delivered date − promised date), late orders | 10.62 days (median 7) | Decrease | Operations Lead |
| Dispatch time | Days from order approval to carrier handoff | Carrier handoff date − approval date | 6,503 orders at 7+ days | Fewer orders at 7+ days | Seller Success Manager |
| Late rate by dispatch bucket | Late share within each dispatch band | Late orders ÷ orders, per band | 3.81% (0–1 d) to 21.39% (7+ d) | Decrease in 7+ day band | Seller Success Manager |
| Cross-state late rate | Late share of orders where seller and customer are in different states | Late cross-state ÷ cross-state orders | 8.03% (same-state: 4.52%) | Narrow the gap to same-state | Logistics Manager |
| Late-order review score | Avg. review score of reviewed late orders | Avg. review score, late orders | 2.27★ (on time: 4.29★) | Increase | CX Lead |
| 1–2 star share, late orders | Share of reviewed late orders scored 1 or 2 | 1–2 star late reviews ÷ reviewed late orders | 62.42% | Decrease | CX Lead |
| Freight % of item value | Freight cost relative to item value | SUM(freight) ÷ SUM(item price) | 16.63% | Hold or reduce | Logistics Manager + Finance |

**Note on freight %.** The average of per-order ratios is 30.84%, pulled up by cheap items with flat freight. This BRD uses the aggregate definition (16.63%) as the official KPI.

## Assumptions

- The promised (estimated) delivery date shown to customers is the right benchmark for on-time.
- Historical patterns (2016–2018) are representative enough to set direction; they are not forecasts.
- Review scores reflect the delivery experience, though they may also reflect product quality.
- Sellers can influence their own dispatch time through process changes.

## Constraints

- No carrier field exists, so carrier and seller effects cannot be separated.
- Only free tools may be used (draw.io, Google Sheets or Excel, free Jira or Trello, Tableau Public).
- The dataset is public under CC BY-NC-SA 4.0: non-commercial use only, with attribution.
- Findings are associations; no causal test has been run.

## Dependencies

- The existing pipeline: S3, Redshift Serverless, dbt (16 models, 90 tests) and the daily Airflow DAG.
- Tableau Public for the dashboard.
- Stakeholder agreement on the at-risk rule (BR-06) and on numeric targets.

## Risks and mitigations

| Risk | Impact | Mitigation |
| --- | --- | --- |
| Overlapping drivers (e.g., peak months also slow dispatch) lead to the wrong fix | Effort spent where it doesn't help | Pilot changes with a control group; run a multivariable model before scaling |
| Longer promised dates reduce conversion | Lost sales | Test buffers on cross-state routes first and monitor conversion |
| Sellers resist a dispatch SLA | Low adoption of BO-1 | Coach first, publish scorecards, escalate only after repeat misses |
| Low-volume states (RR 41, AP 67, AC 80 orders) produce misleading rates | Wrong priorities | BR-04 flags them; exclude from rankings |
| Seasonal spikes mask improvement (e.g., 18.96% late in March 2018) | KPIs misread month to month | Compare like-for-like periods, not consecutive months |

## Open questions

| # | Question | Owner |
| --- | --- | --- |
| 1 | What numeric targets should each KPI carry after the pilot quarter? | Operations Lead |
| 2 | What is the final at-risk rule for BR-06? | CX Lead + Logistics Manager |
| 3 | What dispatch SLA threshold applies, and what happens after repeat misses? | Seller Success Manager |
| 4 | Can carrier data be obtained so BR-10 can be delivered in a later phase? | Logistics Manager |

## Sign-off

| Role | Name | Decision | Date |
| --- | --- | --- | --- |
| Executive Sponsor (COO) | | | |
| Business Owner (Operations Lead) | | | |
| Logistics Manager | | | |
| Business Analyst | Sarika | Author | |
