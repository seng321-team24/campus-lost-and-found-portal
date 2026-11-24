# C4 Model – Level 1 (System Context) and Level 2 (Containers)

**Project:** Campus Lost and Found Portal | **Team:** Team 24
**Owner:** Aslıhan Kaçmaz (K3, Architect) | **Sprint:** 2 | **Status:** Draft for review
**Consistent with:** `docs/system-context-diagram.md` (updated in the same sprint), `docs/architecture/adr.md`, `api/openapi.yaml`. Level 3 diagrams of each module are in `docs/architecture/mX-uml.md`.

Diagrams are Mermaid flowcharts that follow the C4 notation (person, software system, container, external system).

## 1. Level 1 – System Context

```mermaid
flowchart LR
    U["User<br/>[Person]<br/>Student or university staff member who<br/>publishes, searches and claims items"]
    ST["Staff officer<br/>[Person]<br/>Reviews claims and records handovers"]
    AD["Administrator<br/>[Person]<br/>Moderates listings, manages categories and users"]
    SYS["Campus Lost and Found Portal<br/>[Software system]"]
    MAIL["E-mail provider<br/>[External system]<br/>Delivers notification e-mails"]
    UNI["University information systems<br/>[External system]<br/>Not integrated in the first version"]
    U -->|"publishes, browses, searches, claims"| SYS
    ST -->|"approves or rejects claims, records handover"| SYS
    AD -->|"moderates, manages categories and users"| SYS
    SYS -->|"notification e-mails (SMTP)"| MAIL
    MAIL -->|"delivers e-mail"| U
    SYS -.->|"no integration planned"| UNI
```

| Element | Type | Description |
|---|---|---|
| User | Person | Registered student or university staff member. Browsing is public, publishing and claiming require login. |
| Staff officer | Person | Person with the staff role who reviews claims and records handovers (module M5). |
| Administrator | Person | Person with the admin role who moderates inappropriate listings, manages categories and users (module M5). |
| Campus Lost and Found Portal | Software system | The system described in this repository. |
| E-mail provider | External system | Sends e-mails for claim results and listing status changes (module M3). |
| University information systems | External system | Explicitly not integrated (Proposal 5.1 and 6.2). |

## 2. Level 2 – Containers

```mermaid
flowchart TB
    U["User<br/>[Person]"]
    ST["Staff officer<br/>[Person]"]
    AD["Administrator<br/>[Person]"]
    MAIL["E-mail provider<br/>[External system]"]
    subgraph SYS["Campus Lost and Found Portal [Software system]"]
        WEB["Web application<br/>[Container: responsive web UI]<br/>Screens for all modules"]
        API["REST API<br/>[Container: application]<br/>Business logic, authentication,<br/>authorisation, notifications"]
        DB[("Database<br/>[Container: relational database]<br/>Users, listings, categories,<br/>claims, audit log, notification queue")]
        FS["File storage<br/>[Container: file store]<br/>Item photos"]
    end
    U -->|"uses (HTTPS)"| WEB
    ST -->|"uses (HTTPS)"| WEB
    AD -->|"uses (HTTPS)"| WEB
    WEB -->|"calls (HTTPS, JSON, bearer token)"| API
    API -->|"reads and writes (SQL)"| DB
    API -->|"stores and reads photos"| FS
    API -->|"sends notification e-mails (SMTP)"| MAIL
    MAIL -->|"delivers e-mail"| U
```

| Container | Responsibility | Technology | Decision |
|---|---|---|---|
| Web application | Responsive screens for all modules (list, search, detail, claim, staff and admin screens) | Not decided yet, runs in the browser | ADR-001 |
| REST API | Implements `api/openapi.yaml`, validates requests, checks roles, creates and queues notifications | Not decided yet, layered (controller, service, repository) | ADR-001, ADR-002 |
| Database | Stores users, listings, categories, claims, audit log and notification queue | Relational database | ADR-001 |
| File storage | Stores item photos (JPEG or PNG, at most 5 MB); the database holds only `photoUrl` | File store, size limit enforced by the API | ADR-003 |
| E-mail provider | Delivers e-mails sent by the API | External SMTP service | ADR-004 |

## 3. Differences from the Sprint 1 context diagram

The Sprint 1 diagram showed file storage as an external node and listed e-mail notifications as out of scope. Because of the Sprint 2 module plan (M3 notifications) and to match the Vision document, the updated `docs/system-context-diagram.md` now:

- shows the **e-mail provider** as an external system,
- treats **file storage as a container inside the system boundary** (Level 2), not as an external system,
- names the person roles **User, Staff officer and Administrator**,
- keeps university information systems as "not integrated".

## 4. Open points

- Programming language and framework are not decided. This is the next architecture decision (ADR-005) and must be made before Sprint 3 starts.
- Role names in the OpenAPI draft (STUDENT, STAFF, MODERATOR) must be aligned with User, Staff officer and Administrator.
