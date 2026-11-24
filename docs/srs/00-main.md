# System Requirements Specification (SRS) — Campus Lost and Found Portal

**Project:** Campus Lost and Found Portal  
**Team:** Team 24  
**Course:** SENG 321 — Software Engineering  
**Version:** 2.0 (Sprint 2)  
**Lead Author / Accountable:** İrem Köse (K1 - Product Owner)

---

## 1. Introduction

### 1.1 Purpose
This document specifies the software requirements for the **Campus Lost and Found Portal**, a centralized web platform designed for university students, staff, and administrators to report, search, claim, and manage lost and found items across campus.

### 1.2 Scope
The scope encompasses user authentication, profile management, item listing creation (lost/found), item discovery/search with filters, ownership claiming workflow, staff decision/verification, notification alerts, and administrative audit logging. Integration with external university databases and automatic AI verification remain strictly out of scope for the current release.

### 1.3 Definitions, Acronyms, and Abbreviations
* **SRS:** Software Requirements Specification
* **NFR:** Non-Functional Requirement
* **FR:** Functional Requirement
* **UC:** Use Case
* **US:** User Story
* **KVKK / GDPR:** Personal Data Protection Law / General Data Protection Regulation
* **REST:** Representational State Transfer (API Architecture)
* **JWT:** JSON Web Token

### 1.4 References
* System Vision and Boundary Document (`docs/vision-and-boundary.md`)
* Project Proposal (`project_proposal.md`)
* OpenAPI Specification (`api/openapi.yaml`)
* Team Roles and RACI Matrix (`docs/team-roles-raci.md`)

---

## 2. Overall Description

### 2.1 Product Perspective
The Campus Lost and Found Portal operates as an independent web application providing RESTful APIs consumed by a single-page frontend.

### 2.2 System Actors
1. **Unauthenticated Visitor:** Can view public listings, browse categories, and access login/registration pages.
2. **Student User (Student/Staff):** Registered user who can create lost/found listings, manage their own listings, and submit ownership claim requests.
3. **University Staff:** Authorized user who can review ownership claims, record physical item returns, and manage listings in their department.
4. **System Administrator:** Super-user who oversees category structures, manages user roles, handles moderation reports, and reviews system audit logs.

### 2.3 General Constraints
* Standardized ID Naming Rules: `FR-Mx-nn`, `NFR-Mx-nn`, `UC-Mx-nn`, `US-nn`.
* Compliance with MIT License terms for open-source dependencies.
* Compliance with data privacy (KVKK) preventing public display of sensitive personal contact information.

---

## 3. Module Overview Matrix

| Module ID | Module Name | Owner | Primary Focus |
|---|---|---|---|
| **M1** | Identity, Auth & Profile | İrem Köse (K1) | User registration, login, JWT auth, user roles, profile view, KVKK consent |
| **M2** | Item Management | Tuğba Kezban Çay (K2) | Lost/found report creation, edit, close, delete, photo attachment, user's listings |
| **M3** | Discovery & Notification | Aslıhan Kaçmaz (K3) | Item listing search, filtering, category navigation, email notifications |
| **M4** | Ownership Claims | Ceren Torun (K4) | Submitting ownership claims, proof details, claim status tracking |
| **M5** | Approval, Admin & Audit | Berra Bayındır (K5) | Staff claim decision (approve/reject), physical handoff record, moderation, audit logs |

---

## 4. Data Requirements

### 4.1 Data Definitions
* **User Account & Profile Data:** Identity attributes (`full_name`, `email`, `phone_number`, `role`), credential hash (`password_hash`), and explicit privacy consent metadata (`kvkk_accepted`, `kvkk_accepted_at`).
* **Session / Token Metadata:** Active or revoked authentication session tracking (`session_id`, `token_jti`, `expires_at`, `revoked_at`).
* **Item Listing Data:** Lost or found report records including type (`lost` / `found`), category, title, description, location, date/time, lifecycle status, and attached photo metadata.
* **Ownership Claim Data:** Claim requests submitted for a found/lost item, including claimant reference, proof/description details, review status, and staff decision/handover records.
* **Audit & Moderation Data:** Immutable logs recording privileged administrative actions, moderation deletions, role updates, and handover state transitions.

### 4.2 Identifiers
* Primary and path resource identifiers use standardized naming aligned with the API contract: `userId`, `itemId`, `claimId`, and `sessionId` (UUID or unique system identifier).
* Unique business/lookup constraints apply to `email` (unique per user account) and `token_jti` (unique per session token).
* Requirement and story traceability uses standardized IDs: `FR-Mx-nn`, `NFR-Mx-nn`, `UC-Mx-nn`, and `US-nn`.

### 4.3 Relationships
* **User (`1`) — Item Listings (`0..*`):** A registered user can create and manage zero or more lost/found item listings.
* **User (`1`) — User Sessions (`0..*`):** A user can have one or more active or historical authentication sessions.
* **Item Listing (`1`) — Ownership Claims (`0..*`):** An item listing can receive zero or more ownership claim requests over its lifecycle.
* **User (`1`) — Ownership Claims (`0..*`):** A user can submit ownership claims on eligible items (excluding self-claims).
* **Ownership Claim (`1`) — Handover / Decision Record (`0..1`):** A reviewed/approved claim may have an associated staff decision and physical handover record.
* **Category (`1`) — Item Listings (`0..*`):** Each item listing belongs to a defined item category.

### 4.4 Validation Rules
* **Authentication & Registration:** Email must be valid and unique; password must meet security policy and be stored only as a salted hash (never plain text); `kvkk_accepted` must be true for account creation.
* **Item Listings:** Required fields (title, description, category, item type, location/date) must be non-empty; photo uploads must validate allowed media types and maximum file size limits; status transitions must follow valid lifecycle states.
* **Ownership Claims:** Proof/description details must be provided; users cannot claim their own items (self-claim prevention) and cannot submit duplicate active claims for the same item by the same user.
* **Authorization & Privacy:** Sensitive personal contact details must be masked/hidden from public views in compliance with KVKK; privileged endpoints and state changes require appropriate role (`STUDENT`/`STAFF` owner, `STAFF`, or `ADMINISTRATOR`) verification.

### 4.5 Data Retention
* **Active User & Listing Records:** Retained while the account or item remains active within the portal lifecycle, or until closed/deleted according to workflow and retention rules.
* **Session / Token Records:** Retained until token expiration (`expires_at`) or explicit revocation (`revoked_at`), followed by scheduled cleanup/archival per security policy.
* **Audit Logs & Handover Records:** Retained in an append-only / non-tamperable manner for accountability, dispute resolution, and compliance review according to institutional/sprint retention policy.
* **Deleted / Anonymized Records:** Soft-deleted or anonymized in accordance with KVKK data minimization and institutional retention timelines.

### 4.6 Data Ownership
* **M1 (İrem Köse — K1):** Owns user account/profile schema, authentication/session data rules, and KVKK consent records.
* **M2 (Tuğba Kezban Çay — K2):** Owns item listing data structure, photo attachment constraints, and listing lifecycle/ownership rules.
* **M3 (Aslıhan Kaçmaz — K3):** Owns search/filter query indexing rules, category reference data usage, and notification event payloads.
* **M4 (Ceren Torun — K4):** Owns ownership claim data fields, claim proof rules, and claimant-level claim state tracking.
* **M5 (Berra Bayındır — K5):** Owns claim decision/handover records, category/role moderation data rules, and immutable audit log integrity.

---

## 5. Requirement Traceability Matrix

This matrix maps Functional Requirements (FR) to User Stories (US), OpenAPI Endpoints, and QA Validation Strategies.

| FR ID | Description | User Story | OpenAPI Endpoint | Verification Method |
|---|---|---|---|---|
| **FR-M1-01** | User Registration | US-01 | `POST /auth/register` | Automated Unit Test & Integration Test |
| **FR-M1-02** | Form Field Validation | US-01 | `POST /auth/register` | Frontend & API Input Validation Test |
| **FR-M1-03** | User Authentication | US-02 | `POST /auth/login` | Security & Token Verification Test |
| **FR-M1-04** | User Logout | US-02 | Client-side / Token Invalidation | Manual Acceptance Test |
| **FR-M1-05** | Role Assignment | US-01, US-02 | `GET /users/me` | Role-Based Access Control (RBAC) Test |
| **FR-M1-06** | Profile Viewing | US-02 | `GET /users/me` | Integration Test |
| **FR-M1-07** | KVKK / Privacy Consent | US-01 | `POST /auth/register` | Compliance Inspection & UI Test |
| **FR-M2-01** | Lost Item Reporting | US-03 | `POST /items` | End-to-End API Test |
| **FR-M2-02** | Found Item Reporting | US-04 | `POST /items` | End-to-End API Test |
| **FR-M2-03** | Edit Item Listing | US-30 | `PUT /items/{itemId}` | Authorization & PUT Request Test |
| **FR-M2-04** | Close Item Listing | US-31 | `PUT /items/{itemId}` (body: `{"status":"CLOSED"}`) | Status Transition Test |
| **FR-M2-05** | Delete Item Listing | US-32 | `DELETE /items/{itemId}` | Owner Authorization Test |
| **FR-M2-06** | Photo Upload | US-09 | `POST /items/{itemId}/photo` | Media Type & Size Boundary Test |
| **FR-M2-07** | User Item List | US-10 | `GET /users/me/items` | Filtered GET Request Test |
| **FR-M3-01** | Item Listing Pagination | US-05 | `GET /items` | Performance & Pagination Test |
| **FR-M3-02** | Keyword Search | US-06 | `GET /items?q={query}` | Search Accuracy Test |
| **FR-M3-03** | Category Filtering | US-07 | `GET /items?category={cat}` | Query Parameter Test |
| **FR-M3-04** | Status/Location Filter | US-08 | `GET /items?status={st}` | Query Parameter Test |
| **FR-M3-05** | Item Detail View | US-05 | `GET /items/{itemId}` | Data Masking / Privacy Check |
| **FR-M3-06** | Category List API | US-07 | `GET /categories` | Schema Validation Test |
| **FR-M3-07** | Email Alerts | US-40 | Async Email Event | Integration Mock Email Test |
| **FR-M4-01** | Claim Creation | US-11 | `POST /items/{itemId}/claims` | API State Validation Test |
| **FR-M4-02** | Claim Proof Details | US-11 | `POST /items/{itemId}/claims` | Schema Input Test |
| **FR-M4-03** | Self-Claim Prevention | US-11 | `POST /items/{itemId}/claims` | Business Rule Validation Test |
| **FR-M4-04** | Duplicate Claim Prevention| US-11 | `POST /items/{itemId}/claims` | 409 Conflict Response Test |
| **FR-M4-05** | Claim Status Tracking | US-13 | `GET /users/me/claims` | Integration Test |
| **FR-M4-06** | Claim Listing for Owner | US-12 | `GET /items/{itemId}/claims` | Authorization Test |
| **FR-M5-01** | Staff Claim Decision | US-12 | `POST /claims/{claimId}/decision` | Staff Role RBAC Test |
| **FR-M5-02** | Handover Record | US-20 | `POST /claims/{claimId}/handover` *(proposed — OpenAPI change needed)* | Audit State Test |
| **FR-M5-03** | Status Transition (Returned)| US-21 | `PATCH /items/{itemId}/status` *(proposed — OpenAPI change needed)* | Lifecycle State Test |
| **FR-M5-04** | Admin Moderation | US-22 | `DELETE /admin/items/{itemId}` *(proposed — OpenAPI change needed)* | Admin Role Authorization Test |
| **FR-M5-05** | Category Management | US-23 | `POST /admin/categories` *(proposed — OpenAPI change needed)* | Admin CRUD Test |
| **FR-M5-06** | User Role Management | US-24 | `PATCH /admin/users/{id}/role` *(proposed — OpenAPI change needed)* | Security Privilege Test |
| **FR-M5-07** | System Audit Log | US-25 | `GET /admin/audit-logs` *(proposed — OpenAPI change needed)* | Data Immutability Test |

---

## 6. Definition of Ready (DoR) & Definition of Done (DoD)

### Definition of Ready (DoR)
* User Story follows the format: "As a [role], I want to [action] so that [benefit]".
* Acceptance Criteria are explicitly defined using Given-When-Then syntax.
* Story points (effort estimate) are agreed upon by the team.
* Dependencies on other User Stories or APIs are clearly listed.

### Definition of Done (DoD)
* Code / Documentation has been reviewed and approved by the assigned Reviewer.
* Pull Request is merged into the `develop` branch without merge conflicts.
* OpenAPI specification matches implemented/documented endpoints.
* Acceptance criteria are validated through tests or documented reviews.
