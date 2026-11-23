# SRS M2 - Listing Management

**Project:** Campus Lost and Found Portal (Team 24)  
**Sprint:** 2  
**Module owner:** Tugba Kezban Cay (K2 - Scrum Master)  
**Related issue:** #40  
**Status:** Draft for team review and OpenAPI alignment

## 1. Purpose and Scope

M2 allows authenticated campus users to create lost-item and found-item listings, upload photos, edit their own listings, close or delete eligible listings, and view their own listings. Search and public browsing are handled by M3; ownership claims and approval workflows are handled by M4 and M5.

The functional requirements below describe intended behavior. Exact API field names, `ItemStatus` values, supported file formats, size limits, and deletion rules must be checked against the current OpenAPI contract and approved by the team before merging.

## 2. Actors and Preconditions

- **Primary actor:** Authenticated university student or staff member.
- **Supporting services:** Authentication (M1), listing API and persistence, photo storage.
- **General precondition:** The user is logged in before creating, editing, closing, deleting, or managing their own listings.
- **Authorization rule:** A user may modify only listings that they own, unless an explicitly defined administrative permission applies.

## 3. Functional Requirements

| ID | Functional requirement | Verification / acceptance evidence |
| --- | --- | --- |
| **FR-M2-01** | The system shall allow an authenticated user to create a **lost-item** listing with an item name, description, category, and lost location. Required fields shall be validated before saving. | A valid lost-item form creates a listing owned by the user; missing required information produces a validation error without creating one. |
| **FR-M2-02** | The system shall allow an authenticated user to create a **found-item** listing with an item name, description, category, found location, and agreed handover/drop-off information. | Valid information creates a found-item listing; missing required information produces a validation error. |
| **FR-M2-03** | The system shall allow a listing owner to edit permitted fields of their own listing and shall reject edits by other ordinary users. | Owner edits persist; non-owner edits are denied. |
| **FR-M2-04** | The system shall allow a listing owner to close an eligible listing using a valid status transition defined by the project's `ItemStatus` rules. | A permitted close action updates the status; invalid transitions are rejected. |
| **FR-M2-05** | The system shall allow a listing owner to delete an eligible listing in accordance with the project's retention and active-claim rules. | Eligible owner deletion succeeds; non-owner or disallowed deletion fails. |
| **FR-M2-06** | The system shall allow a listing owner to attach a supported photo to their listing and shall reject unsupported or oversized files with an informative error. | Valid photo is attached; invalid format/size is rejected without corrupting the listing. |
| **FR-M2-07** | The system shall allow an authenticated user to view their own listings and their current statuses. | The My Listings view shows the user's listings without including other users' private management records. |

## 4. Use Cases

### UC-M2-01 - Create a Lost Item Listing

- **Primary actor:** Authenticated campus user
- **Related requirements:** FR-M2-01, FR-M2-07
- **Precondition:** The user is signed in.
- **Trigger:** The user selects **Create Listing** and chooses **Lost**.

**Main success scenario:**
1. The system displays the lost-item form.
2. The user enters item name, description, category, and lost location.
3. The user submits the form.
4. The system validates required fields and confirms that the user is authenticated.
5. The system saves the listing and associates it with its creator.
6. The system displays a success confirmation and the listing is available in **My Listings**.

**Alternative / exception flows:**
- **A1 - Missing or invalid fields:** The system displays field-specific validation feedback and does not save the listing.
- **A2 - Session expired:** The system rejects the unauthenticated request and asks the user to sign in.
- **A3 - Save failure:** The system displays an error and does not falsely confirm creation.

**Postcondition:** A valid lost-item listing exists and belongs to the signed-in user.

### UC-M2-02 - Create a Found Item Listing and Add a Photo

- **Primary actor:** Authenticated campus user
- **Related requirements:** FR-M2-02, FR-M2-06, FR-M2-07
- **Precondition:** The user is signed in.
- **Trigger:** The user selects **Create Listing** and chooses **Found**.

**Main success scenario:**
1. The system displays the found-item form.
2. The user enters item name, description, category, found location, and handover/drop-off information.
3. The user submits the form.
4. The system validates and saves the found-item listing under the user's account.
5. The user selects a photo for the saved listing.
6. The system validates the file and attaches it to the listing.
7. The system displays the saved listing with its photo.

**Alternative / exception flows:**
- **A1 - Missing required details:** The system shows validation feedback and does not create the listing.
- **A2 - Unsupported photo format or size:** The system rejects the file and explains the restriction.
- **A3 - Photo upload fails:** The saved listing remains available; the system reports the failure and allows the user to retry.
- **A4 - Session expired:** The system requires the user to sign in again.

**Postcondition:** A found-item listing exists; the photo is associated with it if the upload succeeded.

## 5. Refined Product Backlog

The original Sprint 1 story IDs, priorities, and story-point estimates are preserved below. Newly proposed M2 stories use the reserved `US-30–39` range and require Product Owner/team approval.

### US-03 - Create a Lost Item Listing

**Original story:** As a user, I want to create a lost item listing so that I can report my missing belongings.  
**Priority:** High | **Estimate:** 5 SP | **Related FR:** FR-M2-01

**Acceptance criteria (Given-When-Then):**
- **Given** I am signed in and have entered valid required lost-item details, **when** I submit the form, **then** the system saves the listing under my account and confirms success.
- **Given** a required field is missing or invalid, **when** I submit the form, **then** the system shows an appropriate validation message and does not create the listing.

**Dependencies:** US-02 (login), listing persistence, agreed category/field definitions.

### US-04 - Create a Found Item Listing

**Original story:** As a user, I want to create a found item listing so that I can inform others about items I have found.  
**Priority:** High | **Estimate:** 5 SP | **Related FR:** FR-M2-02

**Acceptance criteria (Given-When-Then):**
- **Given** I am signed in and have entered valid found-item details, **when** I submit the form, **then** the system saves a found-item listing and confirms success.
- **Given** required information is missing, **when** I submit, **then** the system shows validation feedback and does not create the listing.

**Dependencies:** US-02 (login), listing persistence, agreed handover/drop-off fields.

### US-09 - Upload an Item Photo

**Original story:** As a user, I want to upload an item photo so that I can help identify the item.  
**Priority:** Medium | **Estimate:** 3 SP | **Related FR:** FR-M2-06

**Acceptance criteria (Given-When-Then):**
- **Given** I own a saved listing and select a supported image within the agreed size limit, **when** I upload it, **then** the system associates the photo with that listing.
- **Given** the selected image has an unsupported format or exceeds the agreed limit, **when** I upload it, **then** the system rejects the upload and explains why.

**Dependencies:** US-03 or US-04 (listing exists), photo storage, agreed upload constraints.

### US-10 - View My Own Listings

**Original story:** As a user, I want to view my own listings so that I can manage the items I have reported.  
**Priority:** Medium | **Estimate:** 3 SP | **Related FR:** FR-M2-07

**Acceptance criteria (Given-When-Then):**
- **Given** I am signed in, **when** I open **My Listings**, **then** I can see the listings I created and their statuses.
- **Given** I have not created any listings, **when** I open **My Listings**, **then** the system displays an appropriate empty state.
- **Given** another user has listings, **when** I open my own management view, **then** their listings do not appear as mine.

**Dependencies:** US-02 (login), listing ownership data, at least one listing for the non-empty scenario.

### US-30 - Edit My Listing (New - Proposed)

**Story:** As a listing owner, I want to edit my listing so that I can correct or update its details.  
**Proposed priority:** Medium | **Proposed estimate:** 3 SP | **Related FR:** FR-M2-03

- **Given** I own a listing and provide valid changes, **when** I save them, **then** the updated details are stored.
- **Given** I do not own a listing, **when** I attempt to edit it, **then** the system denies the action.

**Dependencies:** US-02, US-03 or US-04, authorization rules.

### US-31 - Close My Listing (New - Proposed)

**Story:** As a listing owner, I want to close an eligible listing so that it is no longer treated as active.  
**Proposed priority:** Medium | **Proposed estimate:** 2 SP | **Related FR:** FR-M2-04

- **Given** I own an eligible listing, **when** I close it, **then** its status changes according to the approved status-transition rules.
- **Given** the transition is not allowed, **when** I try to close the listing, **then** the system rejects the action.

**Dependencies:** US-02, listing status rules, M4/M5 claim workflow coordination.

### US-32 - Delete My Listing (New - Proposed)

**Story:** As a listing owner, I want to delete an eligible listing so that I can remove an unnecessary post.  
**Proposed priority:** Medium | **Proposed estimate:** 2 SP | **Related FR:** FR-M2-05

- **Given** I own a listing that is eligible for deletion, **when** I confirm deletion, **then** the system removes it according to the agreed deletion policy.
- **Given** I do not own the listing or deletion is prohibited, **when** I request deletion, **then** the system rejects the action.

**Dependencies:** US-02, ownership checks, retention and active-claim policy.

## 6. Requirements Traceability

| Functional requirement | Use case | User story | API operation / route to verify |
| --- | --- | --- | --- |
| FR-M2-01 | UC-M2-01 | US-03 | `POST /items` |
| FR-M2-02 | UC-M2-02 | US-04 | `POST /items` |
| FR-M2-03 | - | US-30 | Update operation on `/items/{id}` |
| FR-M2-04 | - | US-31 | Status update operation on `/items/{id}` |
| FR-M2-05 | - | US-32 | Delete operation on `/items/{id}` |
| FR-M2-06 | UC-M2-02 | US-09 | Photo upload on `/items/{id}/photo` |
| FR-M2-07 | UC-M2-01, UC-M2-02 | US-10 | `GET /users/me/items` |

**Note:** These routes are based on the Sprint 2 task allocation. Exact HTTP methods for update/close/delete, parameter names, response codes, and request schemas must be validated against the repository's current OpenAPI specification.

## 7. Assumptions and Open Questions

1. Which fields are required for lost versus found listings, and how is the handover/drop-off location represented?
2. Which exact `ItemStatus` values and transitions are supported for closing a listing?
3. Is deletion permanent or soft deletion, and what happens if a listing has an active claim?
4. Which photo formats and maximum file size are officially supported?
5. Do the OpenAPI schemas and routes match the traceability table above?
6. The Product Owner must approve the proposed US-30, US-31, and US-32 priorities and estimates.

## 8. Definition of Done (M2 Documentation)

- All seven functional requirements have testable acceptance evidence.
- Both use cases include main and alternative flows.
- Original backlog story IDs and estimates are preserved; new stories are clearly marked as proposed.
- Dependencies and traceability are recorded.
- OpenAPI alignment questions are resolved or tracked before final approval.
- The documentation is reviewed and merged through a pull request linked to issue #40.
