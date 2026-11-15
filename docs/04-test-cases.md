# Test Cases

[← Back to README](../README.md)

Total: **43 test cases** · Status options: Not Run, Pass, Fail, Blocked

**Standard steps** (all test cases):

1. Load the applicant's inputs from Test Data into the test harness.
2. Set the bureau stub to return the listed hit code(s).
3. Submit the application to the decision engine.
4. Capture the decision response.


## US-01 · Data checks & bureau retry

| TC ID | AC ID | Scenario | Type | Applicant | Expected result |
|---|---|---|---|---|---|
| TC-001 | AC-01.1 | Missing required field refers without bureau call | Negative | CA-02 | Decision Refer, R01. Bureau call log shows no request for this application. |
| TC-002 | AC-01.2 | Bureau timeout then hit on retry | Positive | CA-03 | Exactly 2 bureau attempts logged (1 failed, 1 successful). Application continues and is Approved. |
| TC-003 | AC-01.2 | Bureau timeout then no hit on retry | Positive | CA-39 | Exactly 2 bureau attempts logged. Retry result (hit code 0) is used: Declined, D01. |
| TC-004 | AC-01.3 | Bureau fails twice returns Error | Negative | CA-04 | Decision Error, E01. Exactly 2 attempts (no third). Applicant message: 'We're unable to process your application right now. Please try again later.' |
| TC-005 | AC-01.3 | Error takes precedence over knockouts | Negative | CA-38 | Decision Error, E01, even though age 17 would trigger KO-02. No knockout reason code is returned. |
| TC-006 | AC-01.4 | Error details written to log file | Positive | CA-04 | Log has 2 entries for CA-04, each with application ID, timestamp, attempt number (1, 2), error type (e.g. timeout / HTTP 503) and response time. Applicant response shows no error type, endpoint or stack trace. |

## US-02 · Knockout rules

| TC ID | AC ID | Scenario | Type | Applicant | Expected result |
|---|---|---|---|---|---|
| TC-007 | AC-02.1 | Hit code 1 does not trigger KO-01 | Positive | CA-01 | Decision **Approved**; reason —; credit limit 150,000. |
| TC-008 | AC-02.1 | Hit code 0 declines | Negative | CA-05 | Decision **Declined**; reason D01; no credit limit. |
| TC-009 | AC-02.2 | Age 17 declines | Boundary | CA-06 | Decision **Declined**; reason D02; no credit limit. |
| TC-010 | AC-02.2 | Age 18 passes | Boundary | CA-07 | Decision **Approved**; reason —; credit limit 150,000. |
| TC-011 | AC-02.2 | Age 65 passes | Boundary | CA-08 | Decision **Approved**; reason —; credit limit 150,000. |
| TC-012 | AC-02.2 | Age 66 declines | Boundary | CA-09 | Decision **Declined**; reason D02; no credit limit. |
| TC-013 | AC-02.2 | Watchlist match declines | Negative | CA-10 | Decision **Declined**; reason D03; no credit limit. |
| TC-014 | AC-02.2 | Bankruptcy flag declines | Negative | CA-11 | Decision **Declined**; reason D04; no credit limit. |
| TC-015 | AC-02.2 | Write-off flag declines | Negative | CA-12 | Decision **Declined**; reason D05; no credit limit. |
| TC-016 | AC-02.2 | Max DPD 59 passes | Boundary | CA-13 | Decision **Approved**; reason —; credit limit 150,000. |
| TC-017 | AC-02.2 | Max DPD 60 declines | Boundary | CA-14 | Decision **Declined**; reason D06; no credit limit. |
| TC-018 | AC-02.2 | Internal delinquent account declines | Negative | CA-15 | Decision **Declined**; reason D07; no credit limit. |
| TC-019 | AC-02.2 | Income 249,999 declines | Boundary | CA-16 | Decision **Declined**; reason D08; no credit limit. |
| TC-020 | AC-02.2 | Income 250,000 passes | Boundary | CA-17 | Decision **Approved**; reason —; credit limit 62,000. |
| TC-021 | AC-02.2 | 5 inquiries passes | Boundary | CA-18 | Decision **Approved**; reason —; credit limit 150,000. |
| TC-022 | AC-02.2 | 6 inquiries declines | Boundary | CA-19 | Decision **Declined**; reason D09; no credit limit. |
| TC-023 | AC-02.3 | No file + age 17 returns only D01 | Positive | CA-20 | Decision **Declined**; reason D01; no credit limit. |
| TC-024 | AC-02.3 | Age 17 + bankruptcy returns only D02 | Positive | CA-21 | Decision **Declined**; reason D02; no credit limit. |
| TC-025 | AC-02.3 | DPD 60 + 6 inquiries returns only D06 | Positive | CA-22 | Decision **Declined**; reason D06; no credit limit. |

## US-03 · Bureau score

| TC ID | AC ID | Scenario | Type | Applicant | Expected result |
|---|---|---|---|---|---|
| TC-026 | AC-03.1 | Score 639 declines | Boundary | CA-23 | Decision **Declined**; reason D10; no credit limit. |
| TC-027 | AC-03.1 | Score 640 refers | Boundary | CA-24 | Decision **Refer**; reason R02; no credit limit. |
| TC-028 | AC-03.1 | Score 719 refers | Boundary | CA-25 | Decision **Refer**; reason R02; no credit limit. |
| TC-029 | AC-03.1 | Score 720 passes | Boundary | CA-26 | Decision **Approved**; reason —; credit limit 100,000. |

## US-04 · Affordability

| TC ID | AC ID | Scenario | Type | Applicant | Expected result |
|---|---|---|---|---|---|
| TC-030 | AC-04.1 | DTI exactly 40.00% passes | Boundary | CA-29 | Decision **Approved**; reason —; credit limit 150,000. |
| TC-031 | AC-04.1 | DTI 40.01% refers | Boundary | CA-30 | Decision **Refer**; reason R03; no credit limit. |
| TC-032 | AC-04.1 | DTI exactly 50.00% refers | Boundary | CA-31 | Decision **Refer**; reason R03; no credit limit. |
| TC-033 | AC-04.1 | DTI 50.01% declines | Boundary | CA-32 | Decision **Declined**; reason D11; no credit limit. |

## US-05 · Final decision & credit limit

| TC ID | AC ID | Scenario | Type | Applicant | Expected result |
|---|---|---|---|---|---|
| TC-034 | AC-05.1 | Refer score + decline DTI = Declined | Positive | CA-33 | Decision **Declined**; reason R02 D11; no credit limit. |
| TC-035 | AC-05.1 | Refer score + refer DTI = Refer | Positive | CA-34 | Decision **Refer**; reason R02 R03; no credit limit. |
| TC-036 | AC-05.1 | Decline score + refer DTI = Declined | Positive | CA-35 | Decision **Declined**; reason D10 R03; no credit limit. |
| TC-037 | AC-05.2 | Score 720 gets Tier 2 limit | Boundary | CA-26 | Decision **Approved**; reason —; credit limit 100,000. |
| TC-038 | AC-05.2 | Score 759 gets Tier 2 limit | Boundary | CA-27 | Decision **Approved**; reason —; credit limit 100,000. |
| TC-039 | AC-05.2 | Score 760 gets Tier 1 limit | Boundary | CA-28 | Decision **Approved**; reason —; credit limit 150,000. |
| TC-040 | AC-05.3 | Limit capped at 500,000 | Boundary | CA-36 | Decision **Approved**; reason —; credit limit 500,000. |
| TC-041 | AC-05.3 | Limit rounded down to nearest 1,000 | Positive | CA-37 | Decision **Approved**; reason —; credit limit 101,000. |
| TC-042 | AC-05.4 | Approved response includes credit limit | Positive | CA-01 | Response contains application ID CA-01, decision Approved, reason '—' and credit limit 150,000. |
| TC-043 | AC-05.4 | Refer response has no credit limit | Negative | CA-24 | Response contains application ID CA-24, decision Refer, reason R02, and no credit limit field (or an empty one). |
