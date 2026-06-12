# Prompt template — Monthly Demand Review

Sử dụng prompt này đầu mỗi tháng (W1 IBP) để Cowork tự động prep Demand Review deck.

---

```
Huy: Cowork, prep Demand Review cho tháng <YYYYMM>.

Context:
- Cycle: IBP W1 — Demand Review prep
- Audience: Head of Merchandising + Planning Manager
- Layer: L1 cho slide tổng, L2 cho slide drill (Top 10)
- Consensus version: latest CONSENSUS lock

Yêu cầu:
1. Verify fact_sales đã close tháng N-1
2. Tính WMAPE/Bias/Hit Rate theo L1 (Channel × Category)
3. Identify Top 10 worst forecast + Top 10 under-forecast (L2)
4. Build deck 9 slides theo template
5. Output: D:\Claude\Planning\reports\<YYYYMM>\pre_demand_review_<YYYYMM>.pptx
6. Chat summary: 5 bullet

Reference: skill `demand-review-pack`.
```

---

## Variation cho ad-hoc

```
Huy: WMAPE tháng N-1 theo channel?
```

Cowork trả lời: tự pull fact_forecast_avf, group by channel, output table + chart trong chat.
