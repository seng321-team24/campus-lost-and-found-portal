# Non-Functional Requirements: Main Document

**Project:** Campus Lost and Found Portal
**Accountable:** Ceren Torun (@ccerentrn), QA Engineer
**Status:** Sprint 2 draft

## 1. Purpose
This document defines the quality attributes of the system, how each one is measured, and how the team tests them. Wording such as "should be fast" is not accepted; each NFR has a number, a unit, and a measurement method.

## 2. Quality Attributes

| Attribute | Meaning for this project | Example target |
|---|---|---|
| Performance | Response time of main operations | p95 < 2 s for search on 1000 listings |
| Security | Passwords, roles, input and file limits | Passwords hashed with bcrypt/Argon2; role checks on every protected endpoint |
| Privacy (KVKK) | Minimal personal data, no public contact data | No name, e-mail, or phone in lists or visitor views |
| Usability | Time to complete core tasks | Listing created in under 2 minutes |
| Accessibility | Usable by people with different abilities | Text contrast ratio of at least 4.5:1 *(proposed)* |
| Reliability | Data is not lost, consistent state on errors | 0 lost claims in 100 submissions |
| Maintainability | Rules defined once, readable structure | Role/permission rules defined in a single place |
| Portability | Works across browsers and devices | Latest versions of Chrome, Firefox, Edge, Safari; usable on mobile browsers |

## 3. Measurement Table
The module owners confirm or change the rows for their own modules. Rows marked *proposed* come from the Sprint 2 task plan.

| ID | Attribute | Requirement | Target | Method | Tool | Owner |
|---|---|---|---|---|---|---|
| NFR-M1-01 | Security | Passwords stored with strong hash; no plain text anywhere | 0 plain-text passwords | Code review and database check | Manual review | İrem *(proposed)* |
| NFR-M1-02 | Performance | Login request time | p95 < 1 s | Load test | k6 / JMeter | İrem *(proposed)* |
| NFR-M2-01 | Performance | Listing creation time | p95 < 3 s | Load test | k6 / JMeter | Tuğba *(proposed)* |
| NFR-M2-02 | Usability | Time to create a listing | < 2 min | User trial with 3–5 people | Stopwatch, notes | Tuğba *(proposed)* |
| NFR-M2-03 | Security | Photo type and size limits | JPG/PNG only, max 5 MB; else 415/413 | API test | Postman / automated tests | Tuğba *(proposed)* |
| NFR-M3-01 | Performance | Search time | p95 < 2 s on 1000 listings | Load test with sample data | k6 / JMeter | Aslıhan *(proposed)* |
| NFR-M3-02 | Privacy | No personal data in lists or visitor view | 0 personal fields | API response inspection | Automated API tests | Aslıhan *(proposed)* |
| NFR-M3-03 | Portability | Screens usable on mobile browsers | Works at 375 px width | Manual check | Browser dev tools | Aslıhan *(proposed)* |
| NFR-M4-01 | Privacy / access | Claim data only for authorized roles | 100% unauthorized requests get 403 | API test with 4 roles | Automated API tests | Ceren |
| NFR-M4-02 | Integrity | One claim per user per listing | 0 duplicates in 50 attempts; 409 | Integration test | Automated tests | Ceren |
| NFR-M4-03 | Reliability | No lost or partial claims | 100/100 stored; 0 partial after 10 failures | Integration test with injected failure | Automated tests | Ceren |
| NFR-M4-04 | Performance | Claim submission time | p95 < 2 s at 20 users | Load test | k6 / JMeter | Ceren |
| NFR-M5-01 | Security | Only staff/admin reach the decision endpoint | Others get 403 | Role-based API test | Automated tests | Berra *(proposed)* |
| NFR-M5-02 | Traceability | Every decision creates an undeletable audit record | 20 actions → 20 records | Count check | Database query | Berra *(proposed)* |
| NFR-M5-03 | Maintainability | Role and permission rules defined in one place | 1 definition location | Code review | Manual review | Berra *(proposed)* |

## 4. Test Strategy

| Level | What is tested | Who | Tools |
|---|---|---|---|
| Unit | Single functions: validation, status rules | Module owner | Test framework of the chosen stack |
| Integration | API with database: duplicates, roles, transactions | Module owner | API tests against a test database |
| Acceptance | Stories against Given-When-Then criteria | QA with module owner | Manual and automated scenarios |
| Non-functional | Performance, privacy, reliability | QA | k6 or JMeter, API tests |
| API contract | `api/openapi.yaml` is valid and consistent | QA | Spectral |

### Acceptance criteria format
Every user story has at least one criterion in this form:
- **Given** (starting state), **when** (action), **then** (observable result).

### Definition of Done link
A story is done when its acceptance criteria pass, its NFRs are measured or scheduled, the PR is reviewed and approved by at least one teammate, and the documentation is updated.

## 5. Numbering Rules
`FR-Mx-nn`, `NFR-Mx-nn`, `UC-Mx-nn`, `US-nn`. `x` is the module number (M1–M5).

## 6. OpenAPI Lint Evidence
See `docs/nfr/openapi-lint-evidence.md`. The file is added only after the lint run is completed and the real output is attached.
