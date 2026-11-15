# Test Data – Applicants

[← Back to README](../README.md)

Each applicant starts from a **base profile** and changes one thing (or a deliberate combination). Base: age 35, annual income 600,000, monthly debt 10,000, bureau hit (code 1), no flags, max DPD 0, 1 inquiry, score 780.

The Notes column says what was changed from the base profile.

| ID | Scenario | Notes | Age | Annual income | Monthly debt | Bureau hit code(s) | Score | DTI | Expected decision | Reason code(s) | Credit limit |
|---|---|---|---|---|---|---|---|---|---|---|---|
| CA-01 | Base approved | Clean profile, score 780 | 35 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Approved** | — | 150,000 |
| CA-02 | Missing age | Age not provided | (missing) | 600,000 | 10,000 | 1 | 780 |  | **Refer** | R01 |  |
| CA-03 | Bureau retry succeeds | Attempt 1 timeout, attempt 2 hit | 35 | 600,000 | 10,000 | 9 → 1 | 780 | 20.00% | **Approved** | — | 150,000 |
| CA-04 | Bureau fails twice | Both attempts time out | 35 | 600,000 | 10,000 | 9 → 9 | 780 |  | **Error** | E01 |  |
| CA-05 | No bureau file | Hit code 0 | 35 | 600,000 | 10,000 | 0 | 780 | 20.00% | **Declined** | D01 |  |
| CA-06 | Age 17 | Below minimum age | 17 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Declined** | D02 |  |
| CA-07 | Age 18 | At minimum age | 18 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Approved** | — | 150,000 |
| CA-08 | Age 65 | At maximum age | 65 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Approved** | — | 150,000 |
| CA-09 | Age 66 | Above maximum age | 66 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Declined** | D02 |  |
| CA-10 | Watchlist | KYC watchlist match | 35 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Declined** | D03 |  |
| CA-11 | Bankruptcy | Bankruptcy flag | 35 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Declined** | D04 |  |
| CA-12 | Write-off | Write-off flag | 35 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Declined** | D05 |  |
| CA-13 | DPD 59 | Just below DPD knockout | 35 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Approved** | — | 150,000 |
| CA-14 | DPD 60 | At DPD knockout | 35 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Declined** | D06 |  |
| CA-15 | Internal delinquent | Delinquent account with lender | 35 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Declined** | D07 |  |
| CA-16 | Income 249,999 | Just below income minimum | 35 | 249,999 | 10,000 | 1 | 780 | 48.00% | **Declined** | D08 |  |
| CA-17 | Income 250,000 | At income minimum | 35 | 250,000 | 5,000 | 1 | 780 | 24.00% | **Approved** | — | 62,000 |
| CA-18 | Inquiries 5 | At inquiry limit | 35 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Approved** | — | 150,000 |
| CA-19 | Inquiries 6 | Above inquiry limit | 35 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Declined** | D09 |  |
| CA-20 | No file + age 17 | Two knockouts: KO-01 first | 17 | 600,000 | 10,000 | 0 | 780 | 20.00% | **Declined** | D01 |  |
| CA-21 | Age 17 + bankruptcy | Two knockouts: KO-02 first | 17 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Declined** | D02 |  |
| CA-22 | DPD 60 + inquiries 6 | Two knockouts: KO-06 first | 35 | 600,000 | 10,000 | 1 | 780 | 20.00% | **Declined** | D06 |  |
| CA-23 | Score 639 | Just below decline cut-off | 35 | 600,000 | 10,000 | 1 | 639 | 20.00% | **Declined** | D10 |  |
| CA-24 | Score 640 | At refer band start | 35 | 600,000 | 10,000 | 1 | 640 | 20.00% | **Refer** | R02 |  |
| CA-25 | Score 719 | At refer band end | 35 | 600,000 | 10,000 | 1 | 719 | 20.00% | **Refer** | R02 |  |
| CA-26 | Score 720 | At approve cut-off (Tier 2) | 35 | 600,000 | 10,000 | 1 | 720 | 20.00% | **Approved** | — | 100,000 |
| CA-27 | Score 759 | Top of Tier 2 | 35 | 600,000 | 10,000 | 1 | 759 | 20.00% | **Approved** | — | 100,000 |
| CA-28 | Score 760 | Start of Tier 1 | 35 | 600,000 | 10,000 | 1 | 760 | 20.00% | **Approved** | — | 150,000 |
| CA-29 | DTI 40.00% | At refer threshold | 35 | 600,000 | 20,000 | 1 | 780 | 40.00% | **Approved** | — | 150,000 |
| CA-30 | DTI 40.01% | Just above refer threshold | 35 | 600,000 | 20,005 | 1 | 780 | 40.01% | **Refer** | R03 |  |
| CA-31 | DTI 50.00% | At decline threshold | 35 | 600,000 | 25,000 | 1 | 780 | 50.00% | **Refer** | R03 |  |
| CA-32 | DTI 50.01% | Just above decline threshold | 35 | 600,000 | 25,005 | 1 | 780 | 50.01% | **Declined** | D11 |  |
| CA-33 | Score 700 + DTI 55% | Refer + Decline → Declined | 35 | 600,000 | 27,500 | 1 | 700 | 55.00% | **Declined** | R02 D11 |  |
| CA-34 | Score 700 + DTI 45% | Refer + Refer → Refer | 35 | 600,000 | 22,500 | 1 | 700 | 45.00% | **Refer** | R02 R03 |  |
| CA-35 | Score 600 + DTI 45% | Decline + Refer → Declined | 35 | 600,000 | 22,500 | 1 | 600 | 45.00% | **Declined** | D10 R03 |  |
| CA-36 | Limit cap | High income, Tier 1 | 35 | 2,400,000 | 10,000 | 1 | 800 | 5.00% | **Approved** | — | 500,000 |
| CA-37 | Limit rounding | Tier 2, uneven income | 35 | 610,000 | 10,000 | 1 | 730 | 19.67% | **Approved** | — | 101,000 |
| CA-38 | Retry fails + age 17 | Error takes precedence over knockouts | 17 | 600,000 | 10,000 | 9 → 9 | 780 |  | **Error** | E01 |  |
| CA-39 | Retry succeeds, no file | Attempt 1 timeout, attempt 2 no hit | 35 | 600,000 | 10,000 | 9 → 0 | 780 | 20.00% | **Declined** | D01 |  |

_In the Excel version, the expected decision, reason codes and limit are formulas driven by the Parameters tab, so changing a threshold recalculates every expected result._
