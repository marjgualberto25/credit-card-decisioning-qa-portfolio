# Decision Rules

[← Back to README](../README.md)

```
Application
   │
   ▼
Stage 0  Data & bureau checks ── missing field ──────────────► REFER (R01)
   │                         └─ bureau down, retry fails ────► ERROR (E01)
   ▼
Stage 1  Knockouts KO-01 → KO-09 ── first one true ──────────► DECLINED (D01–D09)
   ▼
Stage 2  Bureau score ── < 640 Decline (D10) · 640–719 Refer (R02) · ≥ 720 Pass
   ▼
Stage 3  Affordability (DTI) ── > 50% Decline (D11) · > 40% Refer (R03) · ≤ 40% Pass
   ▼
Final    Most severe of Stage 2–3 wins: DECLINED > REFER > APPROVED
   ▼
Stage 4  Credit limit (Approved only) ── 3× or 2× monthly income, cap 500,000, round down to 1,000
```

## Rules

| Stage | Rule ID | Rule | Result |
|---|---|---|---|
| Stage 0 | DC-01 | Required fields (ID, age, annual income, monthly debt) must be present | Missing any → Refer, R01 Incomplete application. Bureau is not called. |
| Stage 0 | BR-01 | Bureau hit code: 1 = hit (file found), 0 = no hit (no file), 9 = bureau unavailable / timeout | — |
| Stage 0 | BR-02 | If the bureau returns 9, the engine retries once | Retry returns 1 or 0 → continue with that result |
| Stage 0 | BR-03 | If the retry also returns 9 → Error, E01 Bureau unavailable | Applicant sees a generic message; technical details go to the log file |
| Stage 1 | KO-01 | bureau_hit_code = 0 | Declined, D01 No credit record |
| Stage 1 | KO-02 | input_age < 18 OR input_age > 65 | Declined, D02 Age not eligible |
| Stage 1 | KO-03 | kyc_watchlist_match = true | Declined, D03 Watchlist match |
| Stage 1 | KO-04 | bureau_bankruptcy_flag = true | Declined, D04 Bankruptcy |
| Stage 1 | KO-05 | bureau_writeoff_flag = true | Declined, D05 Prior write-off |
| Stage 1 | KO-06 | bureau_max_dpd_24m >= 60 | Declined, D06 Serious delinquency |
| Stage 1 | KO-07 | internal_delinquent_account = true | Declined, D07 Delinquent with lender |
| Stage 1 | KO-08 | input_annual_income < 250,000 | Declined, D08 Income below minimum |
| Stage 1 | KO-09 | bureau_inquiries_6m > 5 | Declined, D09 Too many recent inquiries |
| Stage 1 | KO-ORD | Knockouts run in the order KO-01 → KO-09 and stop at the first one that is true | Only that knockout's reason code is returned |
| Stage 2 | SC-01 | bureau_score < 640 | Declined, D10 Score below cut-off |
| Stage 2 | SC-02 | 640 ≤ bureau_score ≤ 719 | Refer, R02 Borderline score (DTI still checked) |
| Stage 2 | SC-03 | bureau_score ≥ 720 | Pass |
| Stage 3 | AF-00 | DTI = existing monthly debt payments ÷ (annual income ÷ 12) | — |
| Stage 3 | AF-01 | 40% < DTI ≤ 50% | Refer, R03 Affordability review |
| Stage 3 | AF-02 | DTI > 50% | Declined, D11 Debt-to-income too high |
| Stage 4 | CL-01 | Score ≥ 760: limit = 3 × monthly income | Approved applications only |
| Stage 4 | CL-02 | 720 ≤ score ≤ 759: limit = 2 × monthly income | Approved applications only |
| Stage 4 | CL-03 | Limit capped at 500,000 | — |
| Stage 4 | CL-04 | Limit rounded down to the nearest 1,000 | — |
| Final | FD-01 | If no knockout fired, the most severe Stage 2–3 result wins: Declined > Refer > Approved | All triggered Stage 2–3 reason codes are returned |
| Final | FD-02 | Decision response contains: application ID, decision, reason code(s), credit limit (Approved only) | — |

## Parameters (placeholder thresholds)

| Name | Description | Value | Used by |
|---|---|---|---|
| age_min | Minimum age | 18 | KO-02 |
| age_max | Maximum age | 65 | KO-02 |
| dpd_max | Max DPD (24m) that declines (≥) | 60 | KO-06 |
| income_min | Minimum annual income (PHP) | 250,000 | KO-08 |
| inq_max | Max bureau inquiries in 6 months | 5 | KO-09 |
| score_decline | Score below this declines | 640 | SC-01 |
| score_approve | Score at/above this passes | 720 | SC-02 |
| score_tier1 | Score at/above this gets Tier 1 limit | 760 | CL-01 |
| dti_refer | DTI above this refers | 40% | AF-01 |
| dti_decline | DTI above this declines | 50% | AF-02 |
| mult_t1 | Tier 1 limit multiple (× monthly income) | 3 | CL-01 |
| mult_t2 | Tier 2 limit multiple (× monthly income) | 2 | CL-02 |
| limit_cap | Maximum credit limit (PHP) | 500,000 | CL-03 |
| round_to | Limit rounded down to nearest (PHP) | 1,000 | CL-04 |
| retries | Bureau retries after a failed call | 1 | BR-02 |

## Reason codes

| Code | Decision | Meaning |
|---|---|---|
| R01 | Refer | Incomplete application |
| E01 | Error | Bureau unavailable after retry |
| D01 | Declined | No credit record |
| D02 | Declined | Age not eligible |
| D03 | Declined | Watchlist match |
| D04 | Declined | Bankruptcy |
| D05 | Declined | Prior write-off |
| D06 | Declined | Serious delinquency |
| D07 | Declined | Delinquent with lender |
| D08 | Declined | Income below minimum |
| D09 | Declined | Too many recent inquiries |
| D10 | Declined | Score below cut-off |
| D11 | Declined | Debt-to-income too high |
| R02 | Refer | Borderline score |
| R03 | Refer | Affordability review |

## Rule-writing convention

Every knockout is written as a **decline-if-true** condition, e.g. `bureau_hit_code = 0` → Decline, rather than a negative pass condition such as `hit_code != 1`. Negative logic is a common source of misread requirements and inverted rules; writing each rule one way gives every test an unambiguous expected result.
