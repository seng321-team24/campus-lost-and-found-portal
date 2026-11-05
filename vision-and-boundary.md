# System Vision & Boundary

**Project:** Campus Lost and Found Portal (Lost and Found Management System)
**Course:** SENG 321 – Sprint 1
**Accountable:** K4 – QA Engineer
**Status:** Draft v0.1 · Sprint 1

---

## 1. Purpose

To let lost and found items on campus be reported, searched, and matched with their rightful owners in a single central web portal. Today this process relies on scattered announcements (WhatsApp groups, notice boards, security desk logbooks). The portal makes it fast, traceable, and verifiable.

**Vision statement:** *Anyone who loses an item on campus can search for it within minutes, and anyone who finds an item can report it in a few steps.*

### Success criteria
- A lost or found listing can be created in under 2 minutes.
- Lost and found listings can be searched by keyword and category.
- The handover process is trackable in the system as a status (Lost → Found → Returned).

---

## 2. Users (Actors)

| User | Description | Main need |
|---|---|---|
| **Item owner** (student/staff) | Loser of an item, signed in with a campus account | Create a lost listing, search found items, submit a claim |
| **Finder** (student/staff) | Person who found an item and reports it | Create a found listing, state where the item was handed in |
| **Office staff** (security / student affairs) | Staff who physically receive found items | Register items, verify ownership claims, confirm handover |
| **Administrator** | Authorized person who manages the portal | Remove inappropriate listings, manage users and categories |
| **Visitor** (not signed in) | Unregistered user | View the public listing only (without personal data) |

---

## 3. Functional Overview

1. **Authentication and profile:** Register, sign in, sign out, view profile.
2. **Lost listing management:** Create, edit, close, delete (title, category, description, date, location, photo).
3. **Found item listing management:** Create, edit, handover location information.
4. **Search and filtering:** By keyword, category, date range, location, and status.
5. **Ownership claim:** The owner submits a claim for a found item with verification details.
6. **Claim verification and handover:** Staff approve or reject the claim and record the handover.
7. **Notifications:** Email notifications for matches, claim results, and status changes.
8. **Admin panel:** Moderation, category management, and user management.
9. **REST API:** All functions are exposed through the API defined in the OpenAPI draft.

---

## 4. Scope

### 4.1 In scope (first release target)
- Creating, listing, updating, and closing lost and found listings
- Search and filtering
- Ownership claim and staff-approved handover flow
- Role-based authorization (user, staff, admin)
- Email notifications
- Photo upload (limited number per listing)
- REST API defined with OpenAPI
- Turkish user interface (English in a later release)

### 4.2 Out of scope
- Native mobile apps (iOS/Android); responsive web only
- Automatic matching through image recognition
- Real-time chat / messaging
- Reward or payment system
- Off-campus (citywide) lost items
- Physical storage and inventory management (only records are kept)
- University SSO integration (first release uses email and password; SSO in a future release)
- Integration with law enforcement

---

## 5. Constraints

| Type | Constraint |
|---|---|
| **Time** | Sprint 1 covers only planning and documentation deliverables; coding takes place in later sprints. |
| **Team** | 5-person student team; roles are assigned as K1–K5. |
| **Cost** | Free/student plans and open-source tools will be used. |
| **License** | The project is MIT licensed; dependencies must be compatible with MIT. |
| **Process** | Git-Flow (`main`, `develop`, `feature/<issue-no>-<name>`); every change is merged via PR with cross-review. |
| **Privacy** | Under KVKK (Turkish data protection law), personal data (name, email, phone) is kept to a minimum; contact details are not shown publicly on listing pages. |
| **Security** | Passwords are stored hashed; API endpoints are protected by role; photo uploads are limited by type and size. |
| **Quality** | Every user story must have measurable acceptance criteria; the OpenAPI file must pass linting without errors. |

---

## 6. Assumptions
- Most users have a campus email address.
- The security / student affairs office agrees to receive found items.
- Users can provide distinguishing details to verify an item (color, brand, contents, etc.).

---

## 7. System Boundary and External Systems

Inside the system boundary: web interface, REST API, database, and file (photo) storage.
Outside the system boundary: email provider (SMTP), physical campus handover points, and (in the future) university SSO.

> The context diagram (Mermaid) is being prepared by K3 and will be added to this section: `docs/context-diagram.md`

| External system / actor | Interaction | Direction |
|---|---|---|
| Email provider (SMTP) | Sending notifications | Portal → Provider |
| User browser | Web interface and API calls | Two-way |
| Physical handover point | The item is held there; the portal only keeps the record | Outside the system |
| University SSO (future) | Authentication | Provider → Portal |

---

## 8. Quality Assurance Notes (K4)
- At least one **Given–When–Then** acceptance criterion will be written for each function (see Product Backlog).
- The OpenAPI file will be linted (e.g., Spectral) and sample responses will conform to the schemas.
- Definition of Done for the first release: code reviewed via PR, acceptance criteria met, documentation up to date.
