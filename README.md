# Credit Card Application Decisioning – QA Portfolio

A QA package for a credit card decision engine: data checks with bureau retry handling, knockout rules, bureau score bands, debt-to-income affordability, credit limit assignment, and the final decision logic that combines them.

> Fictional lender with generic, industry-standard rules and placeholder thresholds, created for portfolio purposes. No real company or client rule logic is used.

## Decision flow

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

**Outcomes:** Approved · Declined · Refer (manual underwriting) · Error (bureau unavailable after one retry)

## What's inside

| # | Document | What it covers |
|---|---|---|
| 1 | [Decision Rules](docs/01-decision-rules.md) | 26 rules across 5 stages, parameters, reason codes, rule-writing convention |
| 2 | [Test Data](docs/02-test-data.md) | 39 test applicants with expected decision, reason codes and limit |
| 3 | [User Stories & Acceptance Criteria](docs/03-user-stories.md) | 5 user stories, 13 acceptance criteria |
| 4 | [Test Cases](docs/04-test-cases.md) | 43 test cases |
| 5 | [Traceability Matrix](docs/05-traceability-matrix.md) | AC → test case mapping, 100% coverage |
| – | [Excel workbook](excel/Credit_Card_Decisioning_QA.xlsx) | Same package with formula-driven expected results and execution tracking |

## Test coverage at a glance

| User story | Focus | ACs | Test cases |
|---|---|---|---|
| US-01 | Data checks & bureau retry | 4 | 6 |
| US-02 | Knockout rules | 3 | 19 |
| US-03 | Bureau score | 1 | 4 |
| US-04 | Affordability | 1 | 4 |
| US-05 | Final decision & credit limit | 4 | 10 |
| **Total** | | **13** | **43** |

By type: Boundary 22, Positive 12, Negative 9.

## Test design approach

- **Boundary value analysis** on every threshold: age 17/18/65/66, DPD 59/60, income 249,999/250,000, inquiries 5/6, score 639/640/719/720/759/760, DTI exactly 40.00%/40.01%/50.00%/50.01%
- **One change per applicant**: each test applicant differs from a clean base profile in one controlled way, so a failure points to one rule
- **Rule order and short-circuit testing**: applicants that trigger two knockouts confirm only the first reason code is returned
- **Decision combination testing**: Refer + Decline, Refer + Refer and Decline + Refer cases confirm the most-severe-wins rule
- **Resilience testing**: bureau timeout with a successful retry, a retry that returns no hit, and two failures ending in Error, including checks that the log holds the technical details and the applicant does not see them
- **Formula-driven expected results**: in the Excel version every expected decision is calculated from a single Parameters tab, so thresholds can change without rewriting test cases
- **Positive rule wording**: knockouts are written as decline-if-true conditions to avoid the double-negative logic (`!=`) that often causes misread requirements

## Author

**Marj Gualberto** – Business Analyst / QA with 7 years in insurance and financial services, including 5+ years building and testing credit decisioning logic (credit decision rules, portfolio and rule performance analysis, KYC, risk-based pricing).
