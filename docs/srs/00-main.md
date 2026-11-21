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
* System Vision and Boundary Document (`docs/system-vision.md`)
* Project Proposal (`docs/project-proposal.md`)
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

## 4. Requirement Traceability Matrix

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
| **FR-M2-03** | Edit Item Listing | US-09 | `PUT /items/{id}` | Authorization & PUT Request Test |
| **FR-M2-04** | Close Item Listing | US-10 | `PATCH /items/{id}/status` | Status Transition Test |
| **FR-M2-05** | Delete Item Listing | US-10 | `DELETE /items/{id}` | Owner Authorization Test |
| **FR-M2-06** | Photo Upload | US-04 | `POST /items/{id}/photo` | Media Type & Size Boundary Test |
| **FR-M2-07** | User Item List | US-09 | `GET /users/me/items` | Filtered GET Request Test |
| **FR-M3-01** | Item Listing Pagination | US-05 | `GET /items` | Performance & Pagination Test |
| **FR-M3-02** | Keyword Search | US-06 | `GET /items?q={query}` | Search Accuracy Test |
| **FR-M3-03** | Category Filtering | US-07 | `GET /items?category={cat}` | Query Parameter Test |
| **FR-M3-04** | Status/Location Filter | US-08 | `GET /items?status={st}` | Query Parameter Test |
| **FR-M3-05** | Item Detail View | US-05 | `GET /items/{id}` | Data Masking / Privacy Check |
| **FR-M3-06** | Category List API | US-07 | `GET /categories` | Schema Validation Test |
| **FR-M3-07** | Email Alerts | US-40 | Async Email Event | Integration Mock Email Test |
| **FR-M4-01** | Claim Creation | US-11 | `POST /items/{id}/claims` | API State Validation Test |
| **FR-M4-02** | Claim Proof Details | US-11 | `POST /items/{id}/claims` | Schema Input Test |
| **FR-M4-03** | Self-Claim Prevention | US-11 | `POST /items/{id}/claims` | Business Rule Validation Test |
| **FR-M4-04** | Duplicate Claim Prevention| US-11 | `POST /items/{id}/claims` | 409 Conflict Response Test |
| **FR-M4-05** | Claim Status Tracking | US-13 | `GET /users/me/claims` | Integration Test |
| **FR-M4-06** | Claim Listing for Owner | US-12 | `GET /items/{id}/claims` | Authorization Test |
| **FR-M5-01** | Staff Claim Decision | US-12 | `POST /claims/{id}/decision` | Staff Role RBAC Test |
| **FR-M5-02** | Handover Record | US-20 | `POST /claims/{id}/handover` | Audit State Test |
| **FR-M5-03** | Status Transition (Returned)| US-21 | `PATCH /items/{id}/status` | Lifecycle State Test |
| **FR-M5-04** | Admin Moderation | US-22 | `DELETE /admin/items/{id}` | Admin Role Authorization Test |
| **FR-M5-05** | Category Management | US-23 | `POST /admin/categories` | Admin CRUD Test |
| **FR-M5-06** | User Role Management | US-24 | `PATCH /admin/users/{id}/role` | Security Privilege Test |
| **FR-M5-07** | System Audit Log | US-25 | `GET /admin/audit-logs` | Data Immutability Test |

---

## 5. Definition of Ready (DoR) & Definition of Done (DoD)

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
