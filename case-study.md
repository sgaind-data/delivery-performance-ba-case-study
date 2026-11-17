# Delivery Performance Case Study

*Sarika · November 2026*

**Seller dispatch time is the strongest signal of late delivery, and late orders are associated with sharply worse reviews (2.27★ vs. 4.29★).** This case study shows how I measured it and what a marketplace operator could do about it.

## Business problem

Olist connects small Brazilian sellers to large online marketplaces. Sellers ship their own orders, and customers see a promised delivery date at checkout. When that promise is broken, satisfaction suffers.

I set out to answer three questions:

1. How often are deliveries late?
2. What is lateness associated with?
3. Where should a marketplace operator act first?

## Data & approach

The analysis covers **96,470 delivered orders** from the public Olist dataset, purchased between 2016-09-15 and 2018-08-29. They come from 93,350 customers, 2,970 sellers and 27 states. I excluded 8 delivered orders with no delivery date and 2,963 orders in other statuses.

An order is **on time** if it was delivered on or before the promised date (comparing dates, not timestamps). **Delay** is the delivered date minus the promised date, for late orders only. **Dispatch time** is the gap between order approval and handoff to the carrier.

I built an automated pipeline on AWS:

- Raw files stored in S3 and loaded into Redshift Serverless.
- A dbt star schema of 16 models, protected by 90 data tests, with a Type 2 snapshot that tracks seller history.
- Reviews round-tripped through DynamoDB: 99,224 reviews with 0 mismatches against the source.
- A daily Airflow refresh, Python statistical tests, and a Tableau Public dashboard.

Total AWS cost was **$0.00**.

## Key findings

**1. Most orders arrive on time, but late ones are very late.** The on-time rate is **93.23%** (89,936 on time, 6,534 late). Late orders averaged **10.62 days** late, with a median of 7.

**2. Late orders are associated with much worse reviews.** A Welch t-test compared review scores between late and on-time orders (t = −100.79, df = 6,875.63, p < 0.001, Cohen's d = −1.71, a very large effect). It measures satisfaction, not the cause of lateness.

| Order group | Reviewed orders | Avg. review | 1–2 star share |
| --- | --- | --- | --- |
| Late | 6,381 | 2.27★ | 62.42% |
| On time | 89,443 | 4.29★ | 9.27% |

**3. Seller dispatch time is the strongest signal of lateness.** Late rates rise steadily as dispatch slows. Among the 804 sellers with 20+ delivered orders, average dispatch days correlates with on-time % at **−0.35**.

| Dispatch time | Delivered orders | Late rate |
| --- | --- | --- |
| 0–1 days | 29,937 | 3.81% |
| 1–3 days | 36,471 | 5.74% |
| 3–7 days | 23,544 | 8.10% |
| 7+ days | 6,503 | 21.39% |

**4. Cross-state shipping carries most of the lateness.** Cross-state orders account for **76.1%** of all late orders.

| Route | Orders | Late rate | Avg. days to deliver |
| --- | --- | --- | --- |
| Cross-state | 61,895 | 8.03% | 15.07 |
| Same-state | 34,575 | 4.52% | 7.89 |

**5. Location matters, but explains only part of lateness.** A chi-squared test found that lateness varies by customer state (χ² = 1,785.79, df = 26, p < 0.001). The effect is small-to-moderate (Cramér's V = 0.136).

- **Worst on-time rates:** AL 78.59% (397 orders), MA 82.57% (717), SE 84.78% (335), PI 86.13% (476), CE 86.24% (1,279).
- **Best large states:** PR 95.96% (4,923 orders), SP 95.51% (40,494), MG 95.43% (11,354).
- **RJ** produces **22.88%** of late orders on only 12.80% of volume, with a 12.11% late rate.
- **SP** produces 27.85% of late orders, but on 41.98% of volume, with a 4.49% late rate.

**6. Lateness spikes seasonally.** The overall late rate is 6.77%. It rose to **12.40%** in November 2017 (Black Friday period), **14.13%** in February 2018 and **18.96%** in March 2018.

**Freight.** The average freight cost is R$ 22.79 per order, or 16.63% of item value. By state, the highest averages are PB R$ 48.84 (517 orders), RR R$ 48.34 (41, low-volume), RO R$ 46.43 (243), AC R$ 45.55 (80, low-volume) and PI R$ 42.98 (476). The lowest is SP at R$ 17.33.

**Sellers.** The worst of the 804 sellers with 20+ orders runs at 66.67% on time (24 orders). Seven of the ten worst are in SP, but SP holds 59.7% of all sellers, so this is not evidence that SP sellers are worse.

## Recommendations (prioritized)

| Priority | Recommendation | Linked finding | Why this order |
| --- | --- | --- | --- |
| 1 | **Introduce a seller dispatch SLA with scorecards.** Target sellers who regularly take 7+ days to dispatch; coach first, then escalate. | #3: 21.39% late at 7+ days vs. 3.81% at 0–1 days | Strongest signal, and directly within the marketplace's control |
| 2 | **Recalibrate promised dates by route and state.** Add buffers for cross-state orders and the worst states (AL, MA, SE). | #4–5: cross-state orders are 76.1% of late orders; AL is 78.59% on time | Makes promises match real transit times without new partners |
| 3 | **Run a focused review of RJ deliveries.** | #5: RJ is 22.88% of late orders on 12.80% of volume | Highest-leverage single state to investigate |
| 4 | **Contact customers proactively on at-risk orders.** Flag slow-dispatch and cross-state orders, and message customers before the promise is missed. | #2: 62.42% of late orders get 1–2 star reviews | Protects satisfaction while root causes are fixed |
| 5 | **Plan peak-season capacity.** Warn sellers, tighten dispatch monitoring and widen promised dates ahead of November and Q1. | #6: late rate reached 12.40% (Nov 2017) and 18.96% (Mar 2018) | Seasonal, so it can be scheduled in advance |

## Expected impact (qualitative)

- Faster dispatch and more realistic promises should reduce late deliveries.
- Fewer late deliveries, given their strong association with low reviews, should improve ratings and reduce complaints.
- Focusing on slow sellers, cross-state routes and RJ keeps the effort targeted instead of company-wide.

No dollar or percentage impact is estimated, because no intervention was tested.

## Limitations

- **No carrier field exists in the data**, so carrier performance can't be separated from seller dispatch.
- Drivers overlap (peak months may also slow dispatch, for example). No causal test was run, so all findings are associations.
- Review averages cover reviewed orders only: 6,381 late and 89,443 on time.
- Freight % has two valid definitions: 16.63% as total freight over total item value, and 30.84% as the average of per-order ratios (pulled up by cheap items with flat freight). This study uses 16.63%.
- Edge months are unreliable; September 2016 has 1 order.
- Low-volume states (fewer than 100 orders): RR (41), AP (67), AC (80).
- Airflow end-to-end run time: **NOT AVAILABLE**, because it wasn't logged. The dbt build took 108.22 s.
- One seller's city was changed on purpose to demonstrate the SCD Type 2 snapshot; the state is unchanged, so results are unaffected.

## 60-second interview summary (STAR)

**Situation.** Olist is a Brazilian marketplace where small sellers ship their own orders against a promised delivery date. Late deliveries were a risk to satisfaction, but there was no clear view of what drove them.

**Task.** I wanted to measure delivery performance, test whether lateness actually matters to customers, and find where an operator should act first.

**Action.** I built a daily pipeline on AWS at zero cost: S3, Redshift Serverless, a dbt star schema with 90 data tests, and Airflow. On top of it I ran statistical tests in Python and published a Tableau dashboard covering 96,470 delivered orders.

**Result.** 93.23% of orders were on time. Late orders averaged 2.27 stars versus 4.29, a very large difference. The strongest signal was seller dispatch time: orders that took 7+ days to reach the carrier were late 21.39% of the time, versus 3.81% for 0–1 days. That made a seller dispatch SLA my top recommendation.

## Likely interview questions

**1. How do you know late delivery actually matters?**
Late orders averaged 2.27★ versus 4.29★, and 62.42% of them got 1–2 star reviews versus 9.27% of on-time orders. A Welch t-test gave p < 0.001 with Cohen's d = −1.71, a very large effect. That's a strong association with satisfaction, not proof of cause.

**2. Why call dispatch the strongest signal, rather than location?**
The late rate climbs steadily from 3.81% to 21.39% across dispatch buckets, and dispatch days correlates with on-time % at −0.35 across 804 sellers. State is significant, but Cramér's V is only 0.136, so location explains only part of lateness. Dispatch is also something the marketplace can directly influence.

**3. Freight is 16.63% of item value, but you also report 30.84%. Which is right?**
Both; they answer different questions. 16.63% is total freight over total item value, the business-level cost share. 30.84% averages each order's own ratio, which cheap items with flat freight pull upward. I lead with 16.63% and explain the gap, because metric definitions change conclusions.

**4. Seven of the ten worst sellers are in SP. Should Olist target SP sellers?**
No. SP holds 59.7% of all sellers, so it will dominate any seller list, and SP's late rate is low at 4.49%. The state worth investigating is RJ: 22.88% of late orders on 12.80% of volume.

**5. How did you make sure the numbers were trustworthy?**
90 dbt tests run on every build. The DynamoDB round-trip matched the source on all 99,224 reviews with 0 mismatches. I also defined the scope explicitly, including the 8 delivered orders without a date and the 2,963 in other statuses that I excluded, and compared dates rather than timestamps for on-time status.

## What I'd do next

- **Get carrier data**, to separate carrier performance from seller dispatch. It's also a prerequisite for any carrier-switch what-if.
- **Run a multivariable model** of late delivery on dispatch time, route, state and month together, to see which signals hold once overlaps are controlled for.
- **Pilot the dispatch SLA** with a test group of slow sellers and a comparable control group. Compare late rate and review score before and after.
- **Test promised-date buffers** on cross-state routes, watching conversion in case longer promises reduce sales.
- **Log Airflow run times** and add data-freshness alerts to the dashboard.

## Links

- [Tableau Public dashboard](https://public.tableau.com/app/profile/sarika.gaind6780/viz/AWSDeliveryAnalysis/DeliveryPerformance)
- [Pipeline repository](https://github.com/sgaind-data/delivery-performance-analytics-aws)
- Data: public Olist Brazilian E-Commerce dataset (Kaggle, CC BY-NC-SA 4.0)
