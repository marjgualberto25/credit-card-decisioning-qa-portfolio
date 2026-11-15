# Requirements Traceability Matrix

[← Back to README](../README.md)

**Coverage: 13 of 13 acceptance criteria covered by 43 test cases.**

| Story | AC ID | Acceptance criterion | Linked test cases | # TCs | Coverage |
|---|---|---|---|---|---|
| US-01 | AC-01.1 | An application missing a required field returns Refer with R01, and the bureau is not called. | TC-001 | 1 | Covered |
| US-01 | AC-01.2 | When the bureau returns hit code 9, the engine retries exactly once and continues with the retry result (1 or 0). | TC-002, TC-003 | 2 | Covered |
| US-01 | AC-01.3 | When the retry also returns 9, the decision is Error with E01, no knockout is evaluated, and the applicant sees a generic message. | TC-004, TC-005 | 2 | Covered |
| US-01 | AC-01.4 | Each failed bureau attempt is written to the log file with application ID, timestamp, attempt number, error type and response time; no technical detail is shown to the applicant. | TC-006 | 1 | Covered |
| US-02 | AC-02.1 | Hit code 0 declines with D01; hit code 1 does not trigger KO-01. | TC-007, TC-008 | 2 | Covered |
| US-02 | AC-02.2 | Each knockout KO-02 to KO-09 declines with its reason code when true, and values at the threshold that should pass do pass. | TC-009, TC-010, TC-011, TC-012, TC-013, TC-014, TC-015, TC-016, TC-017, TC-018, TC-019, TC-020, TC-021, TC-022 | 14 | Covered |
| US-02 | AC-02.3 | Knockouts run in order and stop at the first one that is true; only that reason code is returned. | TC-023, TC-024, TC-025 | 3 | Covered |
| US-03 | AC-03.1 | Score below 640 declines with D10; 640–719 refers with R02; 720 and above passes. | TC-026, TC-027, TC-028, TC-029 | 4 | Covered |
| US-04 | AC-04.1 | DTI = monthly debt ÷ (annual income ÷ 12). DTI up to 40% passes; above 40% up to 50% refers with R03; above 50% declines with D11. | TC-030, TC-031, TC-032, TC-033 | 4 | Covered |
| US-05 | AC-05.1 | When no knockout fires, the most severe Stage 2–3 result wins (Declined > Refer > Approved) and every triggered reason code is returned. | TC-034, TC-035, TC-036 | 3 | Covered |
| US-05 | AC-05.2 | Approved applicants with score 760+ get 3 × monthly income; 720–759 get 2 × monthly income. | TC-037, TC-038, TC-039 | 3 | Covered |
| US-05 | AC-05.3 | The limit is capped at 500,000 and rounded down to the nearest 1,000. | TC-040, TC-041 | 2 | Covered |
| US-05 | AC-05.4 | The response contains application ID, decision and reason code(s); a credit limit appears only when the decision is Approved. | TC-042, TC-043 | 2 | Covered |
