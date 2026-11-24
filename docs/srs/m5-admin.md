# Module 5 (M5) — Approval, Moderation and Audit Specification
 
**Module Owner:** Berra Bayındır (DevOps Engineer)  
**Module Focus:** Staff approval or rejection of ownership claims, handover records, listing status updates, administrator moderation, category and user management, and audit logging.
 
**Terminology:** The course task description speaks of "staff" and "admin". In the OpenAPI contract (`api/openapi.yaml`) the roles are `STUDENT`, `STAFF` and `MODERATOR`. In this document "administrator" means a user with the `MODERATOR` role. Endpoints marked **(proposed)** are not yet in the OpenAPI draft and are requested from the Architect (see Section 5).
 
---
 
## 1. Functional Requirements (FR)
 
### `FR-M5-01`: Claim Review (Approve / Reject)
Users with the `STAFF` or `MODERATOR` role can list pending claims and approve or reject each claim. A rejection must contain a reason (note). A claim that is already decided cannot be decided again.  
*Endpoint:* `POST /claims/{claimId}/decision` (`decideClaim`), `GET /items/{itemId}/claims` (`listItemClaims`).
 
### `FR-M5-02`: Handover Record
After a claim is approved, staff record the handover: to whom the item was given, who recorded it and when.  
*Endpoint:* `POST /claims/{claimId}/handover` **(proposed)**.
 
### `FR-M5-03`: Listing Status Update
The listing status follows the claim process: `OPEN` → `CLAIM_PENDING` (claim submitted) → `CLAIMED` (claim approved) → `CLOSED` (handover recorded). A rejected claim sets the listing back to `OPEN`. Status values are the `ItemStatus` values of the OpenAPI contract.  
*Endpoint:* status is changed by the decision and handover operations; `GET /items/{itemId}` returns the current status.
 
### `FR-M5-04`: Removal of Inappropriate Listings (Moderation)
An administrator can remove a listing that violates the rules. A removal reason is mandatory and is stored in the audit log.  
*Endpoint:* `DELETE /items/{itemId}` (`deleteItem`) with moderator permission.
 
### `FR-M5-05`: Category Management
An administrator can create, rename and deactivate categories. Existing listings keep their category.  
*Endpoint:* `GET /categories` (existing); `POST /categories`, `PATCH /categories/{categoryId}` **(proposed)**.
 
### `FR-M5-06`: User Management
An administrator can list users, change a user's role and suspend or reactivate an account.  
*Endpoint:* `GET /admin/users`, `PATCH /admin/users/{userId}` **(proposed)**.
 
### `FR-M5-07`: Audit Log
Every decision, status change, handover, removal, category change and role change creates an audit record: who (actor), when, which action, which target, and the value before and after. Audit records cannot be edited or deleted through the API. Administrators can view them.  
*Endpoint:* `GET /admin/audit-logs` **(proposed)**.
 
---
 
## 2. Use Cases
 
### Use Case `UC-M5-01`: Staff Evaluates an Ownership Claim
* **Primary Actor:** Staff member (`STAFF` / `MODERATOR`)
* **Secondary Actors:** Claimant, E-mail Service
* **Preconditions:** The staff member is logged in. At least one claim with status `PENDING` exists.
* **Postconditions:** The claim is `APPROVED` or `REJECTED`; the listing status is updated; an audit record exists; the claimant is notified.
#### Main Success Scenario
1. The staff member opens the "Pending Claims" screen.
2. The system lists claims with status `PENDING`.
3. The staff member selects a claim and reads the message and proof.
4. The staff member chooses **Approve** and optionally enters a note.
5. The system checks the role and saves the decision (`POST /claims/{claimId}/decision`).
6. The system sets the claim to `APPROVED` and the listing to `CLAIMED`.
7. The system writes an audit record and sends an e-mail to the claimant.
8. The system shows a confirmation and offers "Record Handover".
#### Alternative Flows
* **4a. Reject:** The staff member chooses **Reject**. The system requires a note. After the note is entered the claim becomes `REJECTED` and the listing returns to `OPEN`.
* **5a. Not authorized:** A user without staff role calls the endpoint. The system returns `403 Forbidden` and no data is changed.
* **5b. Already decided:** Another staff member decided the claim first. The system returns `409 Conflict` and shows the current status.
* **5c. Note missing on rejection:** The system returns `400 Bad Request` and highlights the note field.
### Use Case `UC-M5-02`: Administrator Moderates the Portal
* **Primary Actor:** Administrator (`MODERATOR`)
* **Preconditions:** The administrator is logged in.
* **Postconditions:** The change is applied and an audit record exists.
#### Main Success Scenario
1. The administrator opens the "Admin Panel".
2. The administrator chooses a section: Listings, Categories or Users.
3. The administrator selects an inappropriate listing and chooses **Remove**.
4. The system asks for a reason.
5. The administrator enters the reason and confirms.
6. The system removes the listing and writes an audit record.
7. The administrator opens "Audit Log" and sees the new record (actor, time, action, target).
#### Alternative Flows
* **5a. Reason empty:** The system rejects the request and asks for a reason.
* **3a. Listing does not exist:** The system returns `404 Not Found`.
* **2a. Category or user change:** The same steps apply to categories (rename, deactivate) and users (change role, suspend).
---
 
## 3. Product Backlog Items — Module 5 User Stories
 
*(Story numbers US-20 to US-29 are reserved for M5. Dependencies on M4 stories use the M4 numbering range US-11 to US-19 and must be confirmed with the M4 owner.)*
 
### `US-20`: Review Pending Claims
* **As a** staff member  
* **I want to** see all pending ownership claims  
* **So that** I can decide on them in order.  
* **Priority:** High  
* **Effort:** 3 Story Points  
* **Dependencies:** US-11 (claim creation), US-02 (login)  
* **Acceptance Criteria:**
```gherkin
Given I am logged in as staff and pending claims exist
When I open the Pending Claims screen
Then I see the pending claims with listing title, claimant and creation date.
```
 
### `US-21`: Approve or Reject a Claim
* **As a** staff member  
* **I want to** approve or reject a claim with a note  
* **So that** the item goes to the right person.  
* **Priority:** High  
* **Effort:** 5 Story Points  
* **Dependencies:** US-20  
* **Acceptance Criteria:**
```gherkin
Given a claim has status PENDING
When I approve it
Then the claim becomes APPROVED, the listing becomes CLAIMED and the response is 200 OK.
 
Given a claim has status PENDING
When I reject it without a note
Then I get 400 Bad Request and the claim stays PENDING.
 
Given a claim is already decided
When I try to decide it again
Then I get 409 Conflict.
 
Given I am a student
When I call the decision endpoint
Then I get 403 Forbidden.
```
 
### `US-22`: Record the Handover
* **As a** staff member  
* **I want to** record to whom and when an item was handed over  
* **So that** there is proof that the item was returned.  
* **Priority:** High  
* **Effort:** 5 Story Points  
* **Dependencies:** US-21  
* **Acceptance Criteria:**
```gherkin
Given a claim is APPROVED
When I enter the receiver name and confirm the handover
Then a handover record with my id and the time is saved and the listing becomes CLOSED.
 
Given a claim is not APPROVED
When I try to record a handover
Then I get 409 Conflict.
```
 
### `US-23`: See Updated Listing Status
* **As a** user  
* **I want to** see the current status of a listing  
* **So that** I know whether it is open, pending, claimed or closed.  
* **Priority:** Medium  
* **Effort:** 3 Story Points  
* **Dependencies:** US-21, US-22  
* **Acceptance Criteria:**
```gherkin
Given a claim was approved
When I open the listing
Then the status shows CLAIMED, and after the handover it shows CLOSED.
```
 
### `US-24`: Remove an Inappropriate Listing
* **As an** administrator  
* **I want to** remove a listing that violates the rules  
* **So that** the portal stays safe.  
* **Priority:** Medium  
* **Effort:** 3 Story Points  
* **Dependencies:** US-03, US-04 (listing creation)  
* **Acceptance Criteria:**
```gherkin
Given I am an administrator and a listing exists
When I remove it with a reason
Then the listing is no longer shown and an audit record contains my reason.
 
Given I am a student and not the owner
When I try to remove another user's listing
Then I get 403 Forbidden.
```
 
### `US-25`: Manage Categories
* **As an** administrator  
* **I want to** create, rename and deactivate categories  
* **So that** listings can be sorted in meaningful groups.  
* **Priority:** Low  
* **Effort:** 3 Story Points  
* **Dependencies:** US-07 (category filter)  
* **Acceptance Criteria:**
```gherkin
Given I am an administrator
When I create a category with a new name
Then it appears in GET /categories.
 
Given a category name already exists
When I create it again
Then I get 409 Conflict.
```
 
### `US-26`: Manage Users
* **As an** administrator  
* **I want to** change a user's role or suspend an account  
* **So that** I can manage staff and block misuse.  
* **Priority:** Low  
* **Effort:** 5 Story Points  
* **Dependencies:** US-01, US-02  
* **Acceptance Criteria:**
```gherkin
Given I am an administrator
When I suspend a user
Then that user can no longer log in and an audit record is created.
 
Given I am a staff member without administrator rights
When I call the user management endpoint
Then I get 403 Forbidden.
```
 
### `US-27`: View the Audit Log
* **As an** administrator  
* **I want to** see who changed what and when  
* **So that** disputes can be resolved.  
* **Priority:** Medium  
* **Effort:** 5 Story Points  
* **Dependencies:** US-21, US-22, US-24  
* **Acceptance Criteria:**
```gherkin
Given decisions, handovers and removals happened
When I open the Audit Log
Then I see for each action the actor, time, action, target and before/after values, newest first.
 
Given I am not an administrator
When I request the audit log
Then I get 403 Forbidden.
```
 
---
 
## 4. Traceability: FR ↔ User Story ↔ Endpoint
 
| FR | User Story | Endpoint | In OpenAPI |
|---|---|---|---|
| FR-M5-01 | US-20, US-21 | `GET /items/{itemId}/claims`, `POST /claims/{claimId}/decision` | Yes |
| FR-M5-02 | US-22 | `POST /claims/{claimId}/handover` | Proposed |
| FR-M5-03 | US-23 | `GET /items/{itemId}` | Yes |
| FR-M5-04 | US-24 | `DELETE /items/{itemId}` | Yes |
| FR-M5-05 | US-25 | `POST /categories`, `PATCH /categories/{categoryId}` | Proposed |
| FR-M5-06 | US-26 | `GET /admin/users`, `PATCH /admin/users/{userId}` | Proposed |
| FR-M5-07 | US-27 | `GET /admin/audit-logs` | Proposed |
 
## 5. Requested OpenAPI Additions (for the Architect)
 
1. `POST /claims/{claimId}/handover` — request: receiver name; responses 201, 400, 401, 403, 404, 409.
2. `POST /categories` and `PATCH /categories/{categoryId}` — responses 201/200, 400, 401, 403, 404, 409.
3. `GET /admin/users` and `PATCH /admin/users/{userId}` — responses 200, 401, 403, 404.
4. `GET /admin/audit-logs` — responses 200, 401, 403; schema `AuditLog` (id, actorId, action, targetType, targetId, before, after, createdAt).
