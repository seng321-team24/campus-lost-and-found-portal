# SRS M2 - Listing Management

**Project:** Campus Lost and Found Portal (Team 24)  
**Sprint:** 2  
**Module owner:** Tugba Kezban Cay (K2 - Scrum Master)  
**Related issue:** #47  
**Status:** Draft for team review - aligned with `api/openapi.yaml` (Sprint 1 contract)

## 1. Purpose and Scope

M2 allows authenticated campus users to create lost-item and found-item listings, upload photos, edit their own listings, close or delete eligible listings, and view their own listings. Search and public browsing are handled by M3; ownership claims and approval workflows are handled by M4 and M5.

Field names, enum values, routes and response codes in this document follow the current OpenAPI contract (`api/openapi.yaml`). Items that are **not yet in the contract** are marked **[Proposed - OpenAPI change needed]** and are tracked in Section 8.

## 2. Actors and Preconditions

- **Primary actor:** Authenticated university student or staff member.
- **Supporting actor:** Moderator (M5), who may delete inappropriate or misleading listings.
- **Supporting services:** Authentication (M1), listing API and persistence, photo storage.
- **General precondition:** The user is logged in (bearer token) before creating, editing, closing, deleting, or managing their own listings.
- **Authorization rule:** A user may modify only listings they own (`ownerId`). Requests by other ordinary users are rejected with `403 Forbidden`; unauthenticated requests receive `401 Unauthorized`.

## 3. Domain Terms (from OpenAPI)

| Concept | OpenAPI name | Values | Meaning |
| --- | --- | --- | --- |
| Listing type | `ItemType` | `LOST`, `FOUND` | Whether the item was lost or found. Set at creation. |
| Listing status | `ItemStatus` | `OPEN`, `CLAIM_PENDING`, `CLAIMED`, `CLOSED` | `OPEN` while active, `CLAIM_PENDING` while a claim awaits review, `CLAIMED` after a claim is accepted, `CLOSED` when the listing is closed. |

`LOST` / `FOUND` is the listing **type**, not a status. A new listing of either type starts with status `OPEN`.

**Status transitions relevant to M2:**

| From | To | Triggered by | Module |
| --- | --- | --- | --- |
| (new) | `OPEN` | Listing created | M2 |
| `OPEN` | `CLOSED` | Owner closes the listing | M2 |
| `OPEN` | `CLAIM_PENDING` | A claim is submitted | M4 |
| `CLAIM_PENDING` | `CLAIMED` / `OPEN` | Claim accepted / rejected | M5 |

## 4. Functional Requirements

| ID | Functional requirement | Verification / acceptance evidence |
| --- | --- | --- |
| **FR-M2-01** | The system shall allow an authenticated user to create a listing of type `LOST` with a title (max 120 characters), description (max 1000 characters), category and location (place where the item was lost). The approximate date (`eventDate`) is optional. Required fields shall be validated before saving. | A valid request to `POST /items` returns `201` and a listing with status `OPEN` owned by the user; a missing required field returns `400` and no listing is created. |
| **FR-M2-02** | The system shall allow an authenticated user to create a listing of type `FOUND` with the same fields as FR-M2-01 (location = place where the item was found) and a **handover point** where the finder left the item (e.g. security office). **[Proposed - OpenAPI change needed: `handoverPoint` field, required when `type = FOUND`]** | A valid found-item request creates a `FOUND` listing with status `OPEN`; a missing required field (including the handover point) returns `400`. |
| **FR-M2-03** | The system shall allow a listing owner to edit the title, description, category, location and date of their own listing, and shall reject edits by other ordinary users. | `PUT /items/{itemId}` by the owner returns `200` with updated data; a non-owner receives `403`. |
| **FR-M2-04** | The system shall allow a listing owner to close their listing by changing its status from `OPEN` to `CLOSED`. Closing a listing whose status is `CLAIM_PENDING` or `CLAIMED` shall be rejected. | `PUT /items/{itemId}` with `status: CLOSED` on an `OPEN` listing returns `200`; on a `CLAIM_PENDING`/`CLAIMED` listing it returns `400`. |
| **FR-M2-05** | The system shall allow a listing owner (or a moderator) to delete a listing after confirmation. | `DELETE /items/{itemId}` by the owner or a moderator returns `204`; other users receive `403`; an unknown id returns `404`. |
| **FR-M2-06** | The system shall allow a listing owner to attach a JPEG or PNG photo of at most 5 MB to their listing and shall reject other files with an informative error. **[5 MB limit proposed in NFR-M2-03 - pending team approval]** | `POST /items/{itemId}/photo` with a valid file returns `200` and the listing has a `photoUrl`; an unsupported type returns `415`, an oversized file returns `413`, and the listing is unchanged. |
| **FR-M2-07** | The system shall allow an authenticated user to view their own listings with each listing's type and current status. | `GET /users/me/items` returns only listings whose `ownerId` is the current user. |

## 5. Use Cases

### UC-M2-01 - Create a Lost Item Listing

- **Primary actor:** Authenticated campus user
- **Related requirements:** FR-M2-01, FR-M2-07
- **Precondition:** The user is signed in.
- **Trigger:** The user selects **New Listing** and chooses **Lost item**.

**Main success scenario:**
1. The system displays the lost-item form.
2. The user enters title, description, category and the place where the item was lost, and optionally the approximate date.
3. The user submits the form.
4. The system validates required fields and confirms that the user is authenticated.
5. The system saves the listing with type `LOST` and status `OPEN`, owned by the user.
6. The system displays a success confirmation and the listing appears in **My Listings**.

**Alternative / exception flows:**
- **A1 - Missing or invalid fields:** The system displays field-specific validation messages (`400`) and does not save the listing.
- **A2 - Session expired:** The system rejects the request (`401`) and asks the user to sign in.
- **A3 - Save failure:** The system displays an error and does not falsely confirm creation.

**Postcondition:** A `LOST` listing with status `OPEN` exists and belongs to the signed-in user.

### UC-M2-02 - Create a Found Item Listing and Add a Photo

- **Primary actor:** Authenticated campus user
- **Related requirements:** FR-M2-02, FR-M2-06, FR-M2-07
- **Precondition:** The user is signed in.
- **Trigger:** The user selects **New Listing** and chooses **Found item**.

**Main success scenario:**
1. The system displays the found-item form.
2. The user enters title, description, category, the place where the item was found and the handover point, and optionally the approximate date.
3. The user submits the form.
4. The system validates and saves the listing with type `FOUND` and status `OPEN`.
5. The user selects a photo for the saved listing.
6. The system validates the file type and size and attaches it to the listing.
7. The system displays the saved listing with its photo.

**Alternative / exception flows:**
- **A1 - Missing required details:** The system shows validation messages (`400`) and does not create the listing.
- **A2 - Unsupported file type:** The system rejects the file (`415`) and states that only JPEG and PNG are accepted.
- **A3 - File too large:** The system rejects the file (`413`) and states the maximum size.
- **A4 - Photo upload fails:** The saved listing remains available; the system reports the failure and allows the user to retry.
- **A5 - Session expired:** The system requires the user to sign in again (`401`).

**Postcondition:** A `FOUND` listing with status `OPEN` exists; the photo is attached if the upload succeeded.

## 6. Refined Product Backlog

The original Sprint 1 story IDs, priorities and story-point estimates (from `initial_product_backlog.md`) are preserved below. Newly proposed M2 stories use the reserved `US-30-39` range and require Product Owner/team approval.

### US-03 - Create a Lost Item Listing

**Original story:** As a user, I want to create a lost item listing so that I can report my missing belongings.  
**Priority:** High | **Estimate:** 5 SP | **Related FR:** FR-M2-01

**Acceptance criteria (Given-When-Then):**
- **Given** I am signed in and have entered a valid title, description, category and lost location, **when** I submit the form, **then** the system saves a `LOST` listing with status `OPEN` under my account and confirms success.
- **Given** I leave the date empty, **when** I submit an otherwise valid form, **then** the listing is still created.
- **Given** a required field is missing or invalid, **when** I submit the form, **then** the system shows a validation message and does not create the listing.

**Dependencies:** US-02 (login), category list (`GET /categories`).

### US-04 - Create a Found Item Listing

**Original story:** As a user, I want to create a found item listing so that I can inform others about items I have found.  
**Priority:** High | **Estimate:** 5 SP | **Related FR:** FR-M2-02

**Acceptance criteria (Given-When-Then):**
- **Given** I am signed in and have entered valid found-item details including the handover point, **when** I submit the form, **then** the system saves a `FOUND` listing with status `OPEN` and confirms success.
- **Given** the handover point or another required field is missing, **when** I submit, **then** the system shows a validation message and does not create the listing.

**Dependencies:** US-02 (login), category list, OpenAPI update for `handoverPoint` (see Section 8).

### US-09 - Upload an Item Photo

**Original story:** As a user, I want to upload an item photo so that I can help identify the item.  
**Priority:** Medium | **Estimate:** 3 SP | **Related FR:** FR-M2-06

**Acceptance criteria (Given-When-Then):**
- **Given** I own a saved listing and select a JPEG or PNG image of at most 5 MB, **when** I upload it, **then** the photo is attached and shown as a preview.
- **Given** the selected file is not JPEG or PNG, **when** I upload it, **then** the upload is rejected (`415`) with the message "Only JPG and PNG files are supported."
- **Given** the selected file is larger than 5 MB, **when** I upload it, **then** the upload is rejected (`413`) with a message stating the size limit.

**Dependencies:** US-03 or US-04 (listing exists), photo storage, team approval of the 5 MB limit.

### US-10 - View My Own Listings

**Original story:** As a user, I want to view my own listings so that I can manage the items I have reported.  
**Priority:** Medium | **Estimate:** 3 SP | **Related FR:** FR-M2-07

**Acceptance criteria (Given-When-Then):**
- **Given** I am signed in, **when** I open **My Listings**, **then** I see the listings I created with their type (`LOST`/`FOUND`) and status.
- **Given** I have not created any listings, **when** I open **My Listings**, **then** the system displays an empty state with a "Create listing" action.
- **Given** another user has listings, **when** I open **My Listings**, **then** their listings do not appear.

**Dependencies:** US-02 (login), listing ownership data.

### US-30 - Edit My Listing (New - Proposed)

**Story:** As a listing owner, I want to edit my listing so that I can correct or update its details.  
**Proposed priority:** Medium | **Proposed estimate:** 3 SP | **Related FR:** FR-M2-03

- **Given** I own a listing and provide valid changes, **when** I save them, **then** the updated details are stored and shown.
- **Given** I do not own the listing, **when** I attempt to edit it, **then** the system denies the action (`403`).

**Dependencies:** US-02, US-03 or US-04.

### US-31 - Close My Listing (New - Proposed)

**Story:** As a listing owner, I want to close my listing so that it is no longer treated as active.  
**Proposed priority:** Medium | **Proposed estimate:** 2 SP | **Related FR:** FR-M2-04

- **Given** I own a listing with status `OPEN`, **when** I close it, **then** its status becomes `CLOSED`.
- **Given** my listing has status `CLAIM_PENDING` or `CLAIMED`, **when** I try to close it, **then** the system rejects the action and explains why.

**Dependencies:** US-02, M4/M5 claim workflow (status values).

### US-32 - Delete My Listing (New - Proposed)

**Story:** As a listing owner, I want to delete my listing so that I can remove an unnecessary post.  
**Proposed priority:** Medium | **Proposed estimate:** 2 SP | **Related FR:** FR-M2-05

- **Given** I own a listing, **when** I confirm deletion in the confirmation dialog, **then** the listing is removed and no longer appears in **My Listings**.
- **Given** I cancel the confirmation dialog, **when** I return to the edit screen, **then** the listing is unchanged.
- **Given** I do not own the listing and I am not a moderator, **when** I request deletion, **then** the system rejects the action (`403`).

**Dependencies:** US-02, ownership checks, deletion policy (see Section 8).

## 7. Requirements Traceability

| Functional requirement | Use case | User story | API operation (`api/openapi.yaml`) |
| --- | --- | --- | --- |
| FR-M2-01 | UC-M2-01 | US-03 | `POST /items` (`createItem`), `type: LOST` |
| FR-M2-02 | UC-M2-02 | US-04 | `POST /items` (`createItem`), `type: FOUND` + proposed `handoverPoint` |
| FR-M2-03 | - | US-30 | `PUT /items/{itemId}` (`updateItem`) |
| FR-M2-04 | - | US-31 | `PUT /items/{itemId}` (`updateItem`) with `status: CLOSED` |
| FR-M2-05 | - | US-32 | `DELETE /items/{itemId}` (`deleteItem`) |
| FR-M2-06 | UC-M2-02 | US-09 | `POST /items/{itemId}/photo` (`uploadItemPhoto`), `413` / `415` |
| FR-M2-07 | UC-M2-01, UC-M2-02 | US-10 | `GET /users/me/items` |

## 8. Open Decisions (to resolve before merge)

| # | Topic | Current OpenAPI | This document | Action / owner |
| --- | --- | --- | --- | --- |
| 1 | Handover point for found items | No field | Required `handoverPoint` when `type = FOUND` | Add to `ItemCreate`/`ItemUpdate` in the OpenAPI update PR (Aslıhan) |
| 2 | Photo size limit | "To be agreed by the team"; JPEG/PNG assumed | 5 MB, JPEG/PNG (NFR-M2-03) | Team approval; then update the endpoint description |
| 3 | Closing with an active claim | Not specified | Rejected unless status is `OPEN` | Confirm with M4 (Ceren) and M5 (Berra) |
| 4 | Deletion policy | Owner or moderator, `204` | Hard delete after confirmation | Confirm whether listings with an active claim may be deleted |
| 5 | New stories US-30-32 | - | Priorities/estimates proposed | Product Owner (İrem) approval |

The date (`eventDate`) is optional, as in the current OpenAPI contract; the Figma forms show it without a required marker.

## 9. Definition of Done (M2 Documentation)

- All seven functional requirements have testable acceptance evidence.
- Both use cases include main and alternative flows.
- Original backlog story IDs and estimates are preserved; new stories are clearly marked as proposed.
- Field names, status values and routes match `api/openapi.yaml`; deviations are listed in Section 8.
- Dependencies and traceability are recorded.
- The documentation is reviewed and merged through a pull request linked to issue #47.
