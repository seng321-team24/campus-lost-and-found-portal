# SRS – Module M3: Discovery and Notification

**Project:** Campus Lost and Found Portal | **Team:** Team 24
**Module owner:** Aslıhan Kaçmaz (K3, Architect) | **Sprint:** 2 | **Status:** Draft for review
**Related documents:** `docs/srs/00-main.md` (K1), `docs/nfr/m3.md`, `docs/ux/flows/m3.md`, `docs/architecture/m3-uml.md`, `api/openapi.yaml`

## 1. Scope

M3 covers how users **find** listings (list, search, filter, details, categories) and how the system **informs** users by e-mail (claim result, listing status change).

Out of scope for M3: creating and editing listings and photos (M2), claim submission (M4), staff decisions, handover and audit (M5), registration and login (M1).

**Numbering:** FR-M3-nn (functional), NFR-M3-nn (see `docs/nfr/m3.md`), UC-M3-nn (use cases), stories US-05 to US-08 (existing) and US-40 to US-49 (new, agreed M3 range).

## 2. Functional requirements

| ID | Requirement | Story | API |
|---|---|---|---|
| FR-M3-01 | The system shall list all listings with pagination (default 20 per page, maximum 100), newest first. | US-05 | `GET /items` |
| FR-M3-02 | The system shall search listings by keyword in title and description, case-insensitive. | US-06 | `GET /items?q=` |
| FR-M3-03 | The system shall filter listings by category. | US-07 | `GET /items?categoryId=` |
| FR-M3-04 | The system shall filter listings by event date range, location text, type (lost or found) and status; all filters shall be combinable with each other and with FR-M3-02 and FR-M3-03. | US-40 | `GET /items?dateFrom=&dateTo=&location=&type=&status=` |
| FR-M3-05 | The system shall display listing details (title, type, description, category, location, event date, status, photo, created date) and shall not display name, e-mail or phone number of any user. | US-08 | `GET /items/{itemId}` |
| FR-M3-06 | The system shall provide the category list for filters and listing forms. | US-07 | `GET /categories` |
| FR-M3-07 | The system shall send an e-mail to the registered address of (a) a claimant when the claim is approved or rejected and (b) a listing owner when the status of the listing changes by another user's action. | US-41, US-42 | internal, triggered by `POST /items/{itemId}/claims` and `POST /claims/{claimId}/decision` |
| FR-M3-08 | A failed e-mail shall not fail or roll back the action that triggered it; failed sends shall be retried up to 3 times and every failure shall be logged. | US-41, US-42 | internal |

## 3. Use cases

### UC-M3-01 Search and filter listings

| | |
|---|---|
| **Actors** | Visitor, registered user |
| **Precondition** | The portal is reachable. Login is not required (assumption D1). |
| **Trigger** | The user opens the main list or enters a keyword or filter. |
| **Postcondition** | No data is changed. |

**Main flow**
1. The system shows the first page of listings (FR-M3-01) and the category list (FR-M3-06).
2. The user enters a keyword.
3. The user selects category, date range, location, type or status.
4. The system returns the matching page and the total number of results.
5. The user moves to the next or previous page.

**Alternative flows**
- A1. No result: the system shows "No listings found" and a "Clear filters" action.
- A2. Invalid filter (date range reversed or unknown category): the system returns 400, shows a field message and keeps the previous results.
- A3. Empty keyword: treated as no keyword.
- A4. Service unavailable: the system shows an error message with a retry action.

### UC-M3-02 View listing details and continue to claim

| | |
|---|---|
| **Actors** | Visitor, registered user |
| **Precondition** | At least one listing exists. |
| **Trigger** | The user selects a listing from the results. |
| **Postcondition** | No data is changed. The user may be taken to the claim form (M4). |

**Main flow**
1. The system shows the listing details and photo (FR-M3-05).
2. The user selects "Claim this item".
3. The system opens the claim form of module M4 for this listing.

**Alternative flows**
- A1. User not logged in: the system opens the login screen (M1) and returns to the listing after login.
- A2. Listing not found or deleted: the system returns 404 and shows a page with a link to the list.
- A3. Listing status is not OPEN: the claim action is disabled and the reason is shown.
- A4. The listing belongs to the user: the claim action is hidden.
- A5. No photo: a placeholder image is shown.

## 4. Refined backlog entries (for merging into the single backlog)

Story points use the same scale as the Sprint 1 backlog (1, 2, 3, 5, 8). Priority: High, Medium, Low.

| ID | Epic | User story | Priority | Effort | Depends on |
|---|---|---|---|---|---|
| US-05 | Search and Discovery | As a user, I want to view all item listings so that I can find relevant items. | High | 3 | US-03 or US-04 (listings exist) |
| US-06 | Search and Discovery | As a user, I want to search for items by keyword so that I can find specific belongings. | High | 5 | US-05 |
| US-07 | Search and Discovery | As a user, I want to filter items by category so that I can narrow down the results. | Medium | 3 | US-05 |
| US-08 | Item Details | As a user, I want to view item details so that I can learn more about an item. | High | 2 | US-05 |
| US-40 | Search and Discovery | As a user, I want to filter items by date, location, type and status so that I can find items from a specific time and place. | Medium | 3 | US-05 |
| US-41 | Notifications (new epic) | As a claimant, I want an e-mail when my claim is approved or rejected so that I know the result without checking the portal. | Medium | 5 | M4 claim stories (US-11 to US-19), M5 decision stories (US-20 to US-29) |
| US-42 | Notifications (new epic) | As a listing owner, I want an e-mail when the status of my listing changes so that I can follow what happens to it. | Medium | 3 | US-03, US-04, M4 and M5 stories |

Total effort of M3: 24 points. **Owner of all stories above: Aslıhan Kaçmaz (K3).** Assignees for implementation are confirmed in sprint planning.

## 5. Acceptance criteria (Given-When-Then)

**US-05**
- Given listings exist, when the user opens the main list, then the first 20 listings are shown newest first with title, type, category, location, date and photo thumbnail.
- Given more than 20 listings exist, when the user opens the next page, then the following 20 listings are shown and the total count is visible.
- Given no listing exists, when the user opens the main list, then an empty-state message is shown.

**US-06**
- Given a listing titled "Blue wallet", when the user searches "wallet", then the listing is in the results.
- Given the same listing, when the user searches "WALLET", then the listing is in the results (case-insensitive).
- Given no listing matches, when the user searches, then the empty-state message is shown.

**US-07**
- Given categories exist, when the user selects the category "Keys", then only listings of that category are shown.
- Given a category filter is active, when the user clears it, then all listings are shown again.

**US-08**
- Given a listing exists, when the user opens it, then all fields of FR-M3-05 are shown and the photo is shown if one exists.
- Given a listing exists, when any user views it, then no name, e-mail or phone number appears.
- Given a listing id that does not exist, when the user opens it, then a "listing not found" page is shown.

**US-40**
- Given listings with different event dates, when the user sets a date range, then only listings with an event date inside the range are shown.
- Given the user enters a start date later than the end date, when the user applies the filter, then a validation message is shown and no search is run.
- Given a category and a location filter are both set, when the user applies them, then every result satisfies both.

**US-41**
- Given a pending claim, when staff approves it, then the claimant receives an e-mail within 60 seconds that contains the listing title and the result and no personal data of other users.
- Given a pending claim, when staff rejects it, then the claimant receives an e-mail with the result and the decision note if one was given.
- Given the e-mail provider is unavailable, when the decision is saved, then the decision still succeeds and the e-mail is retried up to 3 times.

**US-42**
- Given an open listing, when another user submits a claim, then the owner receives an e-mail that the listing status changed to CLAIM_PENDING.
- Given a listing, when its status changes to CLAIMED, RETURNED or CLOSED by another user's action, then the owner receives an e-mail with the new status.
- Given the owner changes the status of the own listing, when the change is saved, then no e-mail is sent to the owner.

## 6. Traceability (rows for the main matrix)

| FR | Story | OpenAPI operation | NFR / test |
|---|---|---|---|
| FR-M3-01 | US-05 | `GET /items` | NFR-M3-01, NFR-M3-03 |
| FR-M3-02 | US-06 | `GET /items?q=` | NFR-M3-01 |
| FR-M3-03 | US-07 | `GET /items?categoryId=` | NFR-M3-01 |
| FR-M3-04 | US-40 | `GET /items?dateFrom=&dateTo=&location=&type=&status=` | NFR-M3-01 |
| FR-M3-05 | US-08 | `GET /items/{itemId}` | NFR-M3-02, NFR-M3-03 |
| FR-M3-06 | US-07 | `GET /categories` | NFR-M3-03 |
| FR-M3-07 | US-41, US-42 | internal (triggers: `POST /claims/{claimId}/decision`, `POST /items/{itemId}/claims`) | NFR-M3-04 |
| FR-M3-08 | US-41, US-42 | internal | NFR-M3-04 |

## 7. Assumptions and open decisions

- **D1.** Browsing and search are public (no login), as in the current OpenAPI draft (`GET /items` and `GET /items/{itemId}` have no security). To be confirmed with K1.
- **D2.** E-mails go only to the registered address of the account. No SMS and no in-app notification in this sprint.
- **D3.** The status RETURNED (handover recorded in M5) must be added to `ItemStatus` in the OpenAPI draft (see the OpenAPI v0.2 change list).
- **D4.** Role names differ between the Sprint 2 plan (user, staff, admin) and the OpenAPI draft (STUDENT, STAFF, MODERATOR). To be unified with K1 and K5.
