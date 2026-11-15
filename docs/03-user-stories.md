# User Stories & Acceptance Criteria

[← Back to README](../README.md)

**Epic EP-CCD:** Credit card application decisioning.

## US-01 · Data checks & bureau retry

> As a credit operations lead, I want incomplete applications and bureau outages handled before any rule runs, so that applicants are never declined because of missing data or a system problem.

| AC ID | Acceptance criterion | Rule ref |
|---|---|---|
| AC-01.1 | An application missing a required field returns Refer with R01, and the bureau is not called. | DC-01 |
| AC-01.2 | When the bureau returns hit code 9, the engine retries exactly once and continues with the retry result (1 or 0). | BR-02 |
| AC-01.3 | When the retry also returns 9, the decision is Error with E01, no knockout is evaluated, and the applicant sees a generic message. | BR-03 |
| AC-01.4 | Each failed bureau attempt is written to the log file with application ID, timestamp, attempt number, error type and response time; no technical detail is shown to the applicant. | BR-03 |

## US-02 · Knockout rules

> As a credit risk manager, I want applications that fail a hard policy rule declined immediately with a clear reason code, so that ineligible applicants are not scored.

| AC ID | Acceptance criterion | Rule ref |
|---|---|---|
| AC-02.1 | Hit code 0 declines with D01; hit code 1 does not trigger KO-01. | KO-01 |
| AC-02.2 | Each knockout KO-02 to KO-09 declines with its reason code when true, and values at the threshold that should pass do pass. | KO-02–KO-09 |
| AC-02.3 | Knockouts run in order and stop at the first one that is true; only that reason code is returned. | KO-ORD |

## US-03 · Bureau score

> As a credit risk manager, I want applicants routed by bureau score, so that low scores are declined and borderline scores get a manual review.

| AC ID | Acceptance criterion | Rule ref |
|---|---|---|
| AC-03.1 | Score below 640 declines with D10; 640–719 refers with R02; 720 and above passes. | SC-01–SC-03 |

## US-04 · Affordability

> As a credit risk manager, I want debt-to-income checked, so that applicants are not given credit they cannot afford.

| AC ID | Acceptance criterion | Rule ref |
|---|---|---|
| AC-04.1 | DTI = monthly debt ÷ (annual income ÷ 12). DTI up to 40% passes; above 40% up to 50% refers with R03; above 50% declines with D11. | AF-00–AF-02 |

## US-05 · Final decision & credit limit

> As a cards product owner, I want a single final decision and a correct credit limit, so that approved customers receive the right limit and every outcome is explained.

| AC ID | Acceptance criterion | Rule ref |
|---|---|---|
| AC-05.1 | When no knockout fires, the most severe Stage 2–3 result wins (Declined > Refer > Approved) and every triggered reason code is returned. | FD-01 |
| AC-05.2 | Approved applicants with score 760+ get 3 × monthly income; 720–759 get 2 × monthly income. | CL-01–CL-02 |
| AC-05.3 | The limit is capped at 500,000 and rounded down to the nearest 1,000. | CL-03–CL-04 |
| AC-05.4 | The response contains application ID, decision and reason code(s); a credit limit appears only when the decision is Approved. | FD-02 |

---

**Out of scope:** application front-end, document upload, KYC identity verification itself (only the watchlist result is consumed), card issuance.
