# Memo: Fix seller dispatch first

| | |
| --- | --- |
| **To** | COO (Executive Sponsor); Operations Lead |
| **Cc** | Logistics Manager, Seller Success Manager, Customer Experience Lead, Finance / FP&A |
| **From** | Sarika, Business Analyst |
| **Date** | November 17, 2026 |
| **Re** | Three prioritized actions to reduce late deliveries |

**Bottom line:** 93.23% of 96,470 delivered orders arrive on time, but the 6,534 late orders average 10.62 days late and are associated with much worse reviews (2.27★ vs 4.29★). I recommend starting with a seller dispatch SLA: it reaches the most late orders, costs no freight, and is fully within the marketplace's control.

## What the data shows

- **Slow seller dispatch is the strongest signal.** Orders handed to the carrier after more than 7 days are late 21.39% of the time, vs 3.81% within a day.
- **Cross-state shipping carries most lateness.** Cross-state orders are late 8.03% of the time (same-state: 4.52%) and make up 76.1% of late orders.
- **Lateness hurts satisfaction.** 62.42% of late orders get 1–2 star reviews, vs 9.27% of on-time orders.

All findings are associations, not proven causes; the estimates below assume late rates follow dispatch bands and state targets.

## Recommendations, in priority order

| # | Action | Estimated impact | Cost |
| --- | --- | --- | --- |
| 1 | **Seller dispatch SLA with scorecards.** Coach sellers in the 7+ day band first; escalate repeat misses. | About 432 late orders avoided; on-time rate 93.23% → 93.67% (50% seller adoption). Range: 216 to 864 at 25% to 100% adoption. | No freight cost; Seller Success time |
| 2 | **Early-warning alerts on 7+ day dispatch orders**, plus promised-date buffers on cross-state routes. | Alerts reach 6,503 orders (6.74% of all) that contain 1,391 late orders (21.29% of all late), so CX can contact customers before the date is missed. Buffer effect to be measured in a pilot. | CX contact time; possible conversion effect from longer promises |
| 3 | **Faster service in AL, MA, SE, PI and CE** (on-time 78.59% to 86.24% today). | About 286 late orders avoided if these states reach the 93.23% average; on-time rate +0.30 points. | About R$ 12,696 extra freight at an assumed +10% (about R$ 44 per late order avoided) |

**Refinement to the BRD:** the at-risk rule in BR-06 (7+ day dispatch *or* cross-state) would flag 66.44% of all orders, too many to contact. I recommend the 7+ day dispatch rule for alerts and handling cross-state risk through promised-date buffers.

## Risks and assumptions

- Seller adoption is assumed at 50%; the sensitivity range is shown above.
- No carrier field exists, so recommendation 3's cost is an assumption until carriers quote real rates.
- Longer promised dates may reduce conversion; test them on cross-state routes first.
- Two dashboard defects remain open (low-volume flag, freight %); state rankings should not drive decisions until the low-volume flag ships in Sprint 1.

## Decision requested

Approve a one-quarter pilot of recommendation 1 with a matched control group of sellers, and recommendation 2's alerts in Sprint 2. Revisit recommendation 3 once carrier quotes are in.

*Sources: verified pipeline findings; what-if model in [05-excel-analysis](../05-excel-analysis/excel-analysis.md); stakeholder deck in this folder.*
