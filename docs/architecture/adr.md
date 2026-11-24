# Architecture Decision Records (ADR)

**Project:** Campus Lost and Found Portal | **Team:** Team 24
**Owner:** Aslıhan Kaçmaz (K3, Architect) | **Sprint:** 2

All records have the status **Proposed**. A record becomes **Accepted** when the pull request that adds or changes it is approved by a team member. Decisions that are still open are listed at the end.

---

## ADR-001 Architecture style: layered REST API with a relational database

**Status:** Proposed

**Context.** The team has five members and four sprints. The OpenAPI contract is a course deliverable and the first sprint already defined it. The data (users, listings, claims, audit log) is strongly related and needs consistent updates, for example when a claim decision changes both the claim and the listing status.

**Options considered**
1. Microservices per module (M1 to M5).
2. Server-rendered monolith without a separate API.
3. One REST API application with layers (controller, service, repository), a relational database and a separate responsive web application.

**Decision.** Option 3. The five modules are packages of one deployable application. Each module keeps its own controllers, services and repositories. The web application only talks to the REST API.

**Consequences**
- Simple deployment and testing for a small team; module boundaries are kept by packages.
- The web UI and the API can be developed in parallel against `api/openapi.yaml`.
- Microservices would add operational work that the project does not need; the module boundaries leave the option open later.
- Language and framework are not part of this decision (see open decisions).

---

## ADR-002 Authentication: stateless JWT bearer tokens

**Status:** Proposed

**Context.** The OpenAPI draft already declares `bearerAuth` (JWT) and a login response with `expiresIn: 3600`. The Sprint 2 plan requires strongly hashed passwords (FR-M1, NFR-M1-01) and role-based access to staff and admin operations (NFR-M5-01). University single sign-on is outside the first version (Proposal 6.2, backlog section 10).

**Options considered**
1. Server-side sessions with cookies.
2. Stateless JWT bearer tokens issued by `POST /auth/login`.
3. University single sign-on (SAML or OpenID Connect).

**Decision.** Option 2. Passwords are stored only as bcrypt or Argon2 hashes. The token carries the user id and role (user, staff, admin) and expires after 1 hour. The API checks the role on every protected operation and returns 401 (no or invalid token) or 403 (role not allowed).

**Consequences**
- Matches the existing OpenAPI contract and keeps the API stateless.
- A token cannot be revoked before it expires. Logout (FR-M1-04) removes the token on the client; a server-side block list can be added later if the team requires it.
- Single sign-on can be added later behind the same login operation without changing other operations.

---

## ADR-003 Photo storage: files in a file store, only the URL in the database

**Status:** Proposed

**Context.** Photos help identify items (US-09). The OpenAPI draft stores only `photoUrl` in the listing and defines `413` and `415` responses for the upload. Photos may contain personal data, for example a face or location data in the file metadata (KVKK).

**Options considered**
1. Store image bytes in the database.
2. Store image files in a file store inside the system and keep only the URL in the database.
3. Use an external image hosting service.

**Decision.** Option 2. The API accepts JPEG and PNG up to 5 MB (the final limit is confirmed by the owner of module M2 and the Product Owner). The API renames files to random identifiers and removes metadata such as location data before saving. Only the listing owner can upload or replace the photo.

**Consequences**
- The database stays small and backups are simple; the file store needs its own backup.
- File storage is a container inside the system boundary (see C4 Level 2).
- An external hosting service would make privacy and availability depend on a third party, so it is rejected for the first version.

---

## ADR-004 E-mail notifications: asynchronous queue and SMTP provider

**Status:** Proposed

**Context.** Module M3 requires e-mails for claim results and listing status changes (FR-M3-07). An e-mail failure must not block the action that caused it (FR-M3-08, NFR-M3-04). E-mails must not contain personal data of other users (NFR-M3-02).

**Options considered**
1. Send the e-mail inside the request that triggers it.
2. Put a message into a queue (a database table) and send it in the background through an SMTP provider.
3. Notify only inside the portal, without e-mail.

**Decision.** Option 2. The triggering component calls `NotificationService.notify(event)`, which stores a message and returns at once. A background worker sends the message through the `EmailAdapter`, retries up to 3 times and logs failures. Messages go only to the registered address of the account and contain the listing title and the new status or claim result.

**Consequences**
- Claim decisions and listing updates stay fast and reliable when the provider is down.
- A queue table and a background worker must be implemented and tested (NFR-M3-04).
- The choice of the SMTP provider (university server or a transactional e-mail service) is open.
- In-portal notifications only (option 3) do not satisfy FR-M3-07.

---

## Open decisions

| ID | Decision | Needed from | Needed by |
|---|---|---|---|
| ADR-005 | Programming language and framework for the API and the web application | Whole team | Before Sprint 3 starts |
| Open-1 | SMTP provider for e-mail notifications | K5 with K3 | Sprint 3 |
| Open-2 | Role names: OpenAPI draft (STUDENT, STAFF, MODERATOR) or Sprint 2 plan (user, staff, admin) | K1 with K5 | Sprint 2 |
| Open-3 | Whether logout must revoke the token on the server | K1 with K3 | Sprint 3 |
