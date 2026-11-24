# M4 – Ownership Claim and Verification: SRS and Backlog

**Project:** Campus Lost and Found Portal
**Module:** M4 – Ownership claim and verification
**Module owner:** Ceren Torun (@ccerentrn), QA Engineer
**Story ID range:** US-11 to US-19
**Status:** Sprint 2 draft

> Endpoint paths and status values below follow `api/openapi.yaml`. Any value marked *(proposed)* must be checked against the OpenAPI file before merge.

---

## 1. Module Scope

M4 covers everything a claimant does after finding their lost item in a found listing: submitting a claim with verification details, tracking its status, and seeing claims on listings they own.

| In M4 | Handled by another module |
|---|---|
| Create claim, verification fields, duplicate and own-listing rules | Registration and login (M1) |
| Claim status view, "my claims", incoming claims list | Creating and editing listings (M2) |
| Access control on claim data | Notification e-mails (M3) |
| | Staff approve/reject decision, handover record, audit log (M5) |

---

## 2. Functional Requirements

| ID | Requirement | Priority | Story | Endpoint |
|---|---|---|---|---|
| FR-M4-01 | An authenticated user shall be able to submit an ownership claim for a found listing whose status is Found. | High | US-11 | `POST /items/{id}/claims` |
| FR-M4-02 | The claim form shall collect verification details: a free-text description (required) and optional color, brand, and distinguishing contents/marks *(proposed fields)*. | High | US-12 | `POST /items/{id}/claims` |
| FR-M4-03 | A user shall not be able to claim a found listing that they created themselves. | Medium | US-13 | `POST /items/{id}/claims` |
| FR-M4-04 | A user shall not be able to submit a second claim for the same listing. | High | US-14 | `POST /items/{id}/claims` |
| FR-M4-05 | A claimant shall be able to view the status of a claim (Pending, Approved, Rejected) *(proposed values, align with ClaimStatus)*. | High | US-15 | `GET /users/me/claims` |
| FR-M4-06 | A user shall be able to list all of their own claims. | Medium | US-16 | `GET /users/me/claims` |
| FR-M4-07 | A listing owner and staff/admin shall be able to list incoming claims for a listing. | High | US-17 | `GET /items/{id}/claims` |

---

## 3. Use Cases

### UC-M4-01 Submit an ownership claim
- **Actor:** Authenticated user (claimant)
- **Preconditions:** User is signed in. The found listing has status Found and was not created by the user. The user has no existing claim on it.
- **Main flow:**
  1. User opens a found listing detail page.
  2. User selects "This is mine".
  3. System shows the claim form.
  4. User enters description and optional color, brand, contents.
  5. User submits the form.
  6. System validates the input and stores the claim with status Pending.
  7. System shows a confirmation with the claim status.
- **Alternative flows:**
  - A1. Description is empty: system shows a field error and does not store the claim.
  - A2. User already claimed this listing: system rejects with a duplicate-claim error and links to the existing claim.
  - A3. User owns the listing: the "This is mine" action is hidden and the API rejects the call.
  - A4. Listing status is no longer Found (for example Returned or Closed): system rejects the claim and explains why.
- **Postcondition:** A claim with status Pending exists and is visible to the claimant, the listing owner, and staff.

### UC-M4-02 Track claim status
- **Actor:** Authenticated user (claimant)
- **Preconditions:** User has at least one claim.
- **Main flow:**
  1. User opens "My claims".
  2. System lists the user's claims with item title, submission date, and status.
  3. User opens one claim.
  4. System shows the claim details and current status.
- **Alternative flows:**
  - A1. User has no claims: system shows an empty state with a link to browse found items.
  - A2. Claim was rejected: system shows the status; the staff reason is displayed if M5 provides one.
- **Postcondition:** User knows the current state of each claim.

---

## 4. Refined Product Backlog (M4)

Estimates use story points (1, 2, 3, 5, 8). Priority is High / Medium / Low.

| ID | User story | Points | Priority | Depends on |
|---|---|---|---|---|
| US-11 | As a signed-in user, I want to submit an ownership claim on a found item so that I can get my item back. | 5 | High | US-01, US-02, US-04 |
| US-12 | As a claimant, I want to enter verification details so that staff can confirm the item is mine. | 3 | High | US-11 |
| US-13 | As a finder, I want to be prevented from claiming my own found listing so that claims stay trustworthy. | 2 | Medium | US-11, US-04 |
| US-14 | As a claimant, I want duplicate claims on the same listing to be blocked so that the queue stays clean. | 2 | High | US-11 |
| US-15 | As a claimant, I want to see my claim status so that I know whether it was approved or rejected. | 3 | High | US-11, US-20 |
| US-16 | As a claimant, I want a "My claims" list so that I can track all of my requests in one place. | 3 | Medium | US-11 |
| US-17 | As a listing owner or staff member, I want to see incoming claims for a listing so that I can review them. | 5 | High | US-11, US-03 |
| US-18 | As a user, I want claim data to be visible only to authorized people so that my verification details stay private. | 3 | High | US-11, US-17 |

### US-11 Submit an ownership claim (5 pts, High)
- **Given** I am signed in and viewing a found listing (status Found) created by someone else, **when** I submit the claim form with a valid description, **then** a claim with status Pending is created and I see a confirmation.
- **Given** I am not signed in, **when** I try to submit a claim, **then** I am asked to sign in and no claim is created.
- **Given** the listing is closed or returned, **when** I try to submit a claim, **then** the request is rejected with a clear message.

### US-12 Enter verification details (3 pts, High)
- **Given** I open the claim form, **when** I leave the description empty and submit, **then** I see a field error and no claim is created.
- **Given** I fill in description, color, brand, and contents, **when** I submit, **then** all values are stored with the claim.

### US-13 Block claims on own listing (2 pts, Medium)
- **Given** I created a found listing, **when** I open its detail page, **then** I do not see the claim action.
- **Given** I created a found listing, **when** I call the claim endpoint for it directly, **then** the request is rejected with 403.

### US-14 Block duplicate claims (2 pts, High)
- **Given** I already submitted a claim for a listing, **when** I submit another claim for the same listing, **then** the request is rejected with 409 and no second claim is stored.
- **Given** I have a claim on listing A, **when** I submit a claim on listing B, **then** it is accepted.

### US-15 View claim status (3 pts, High)
- **Given** I have a Pending claim, **when** I open it, **then** I see status Pending.
- **Given** staff approved my claim, **when** I open it, **then** I see status Approved.
- **Given** staff rejected my claim, **when** I open it, **then** I see status Rejected.

### US-16 My claims list (3 pts, Medium)
- **Given** I have three claims, **when** I open "My claims", **then** I see exactly those three, newest first, each with item title and status.
- **Given** I have no claims, **when** I open "My claims", **then** I see an empty state with a link to found items.

### US-17 Incoming claims list (5 pts, High)
- **Given** I own a listing with two claims, **when** I open its claims, **then** I see both with submission date, status, and verification details; the claimant's identity is shown only to staff and admin (KVKK).
- **Given** I am staff or admin, **when** I open any listing's claims, **then** I see all of them.

### US-18 Claim data access control (3 pts, High)
- **Given** I am neither the claimant, the listing owner, nor staff/admin, **when** I request a claim, **then** I receive 403 and no verification details.
- **Given** I am a visitor who is not signed in, **when** I request claims, **then** I receive 401.

---

## 5. Traceability (M4)

| FR | User story | Endpoint | Test (to be linked) |
|---|---|---|---|
| FR-M4-01 | US-11 | `POST /items/{id}/claims` | Create claim, happy path |
| FR-M4-02 | US-12 | `POST /items/{id}/claims` | Validation test |
| FR-M4-03 | US-13 | `POST /items/{id}/claims` | Own-listing 403 test |
| FR-M4-04 | US-14 | `POST /items/{id}/claims` | Duplicate 409 test |
| FR-M4-05 | US-15 | `GET /users/me/claims` | Status values test |
| FR-M4-06 | US-16 | `GET /users/me/claims` | List test |
| FR-M4-07 | US-17, US-18 | `GET /items/{id}/claims` | Access control test |

## 6. Out of Scope for M4
Claim withdrawal, in-app chat between claimant and finder, image evidence upload for claims, and any mobile or SSO feature (see System Vision & Boundary).
