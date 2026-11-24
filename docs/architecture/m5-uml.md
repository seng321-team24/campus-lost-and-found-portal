# Module 5 (M5) — Architecture & UML Specifications

**Module Owner:** Berra Bayındır (DevOps Engineer)  
**Scope:** Claim decision, handover, moderation and audit. All diagrams are written in Mermaid so that they are versioned with the repository.

---

## 1. Sequence Diagram: Claim Decision (with role and conflict checks)

```mermaid
sequenceDiagram
    actor Staff as Staff / Moderator
    participant UI as Web UI
    participant API as REST API
    participant PERM as Permission Checker
    participant DB as Database
    participant AUD as Audit Component
    participant MAIL as E-mail Service

    Staff->>UI: Choose Approve or Reject (and enter note)
    UI->>API: POST /claims/{claimId}/decision (JWT, decision, note)
    API->>PERM: Check role for decideClaim
    alt role is not STAFF or MODERATOR
        PERM-->>API: denied
        API-->>UI: 403 Forbidden
    else role allowed
        PERM-->>API: allowed
        API->>DB: Load claim and listing
        alt claim not found
            DB-->>API: not found
            API-->>UI: 404 Not Found
        else claim already decided
            DB-->>API: status is not PENDING
            API-->>UI: 409 Conflict
        else claim is PENDING
            API->>DB: Update claim and listing status (one transaction)
            API->>AUD: Write audit record
            AUD->>DB: Insert audit row (actor, action, target, before, after)
            API->>MAIL: Notify claimant of the decision
            API-->>UI: 200 OK (Claim)
        end
    end
    UI-->>Staff: Show result
```

---

## 2. Class Diagram: Decision, Handover and Audit Domain Model

```mermaid
classDiagram
    class User {
        +String id
        +String email
        +UserRole role
    }
    class Item {
        +String id
        +ItemType type
        +ItemStatus status
    }
    class Claim {
        +String id
        +String itemId
        +String claimantId
        +ClaimStatus status
        +String decisionNote
        +DateTime decidedAt
    }
    class Decision {
        +String claimId
        +String decidedBy
        +DecisionType decision
        +String note
        +DateTime decidedAt
    }
    class HandoverRecord {
        +String id
        +String claimId
        +String recordedBy
        +String receiverName
        +DateTime handedOverAt
    }
    class AuditLog {
        +String id
        +String actorId
        +AuditAction action
        +String targetType
        +String targetId
        +String beforeValue
        +String afterValue
        +DateTime createdAt
    }
    class UserRole {
        <<enumeration>>
        STUDENT
        STAFF
        MODERATOR
    }
    class ItemStatus {
        <<enumeration>>
        OPEN
        CLAIM_PENDING
        CLAIMED
        CLOSED
    }
    class ClaimStatus {
        <<enumeration>>
        PENDING
        APPROVED
        REJECTED
    }
    class DecisionType {
        <<enumeration>>
        APPROVED
        REJECTED
    }

    User "1" --> "*" Claim : submits
    Claim "*" --> "1" Item : is about
    Claim "1" --> "0..1" Decision : has
    Claim "1" --> "0..1" HandoverRecord : leads to
    User "1" --> "*" Decision : decides
    User "1" --> "*" AuditLog : is actor of
    User --> UserRole
    Item --> ItemStatus
    Claim --> ClaimStatus
    Decision --> DecisionType
```

---

## 3. State Diagram: Listing and Claim Status

The listing states are the `ItemStatus` values and the claim states are the `ClaimStatus` values of `api/openapi.yaml`.

```mermaid
stateDiagram-v2
    direction LR
    state "Listing (ItemStatus)" as L {
        [*] --> OPEN
        OPEN --> CLAIM_PENDING: claim submitted
        CLAIM_PENDING --> CLAIMED: claim approved
        CLAIM_PENDING --> OPEN: claim rejected
        CLAIMED --> CLOSED: handover recorded
        OPEN --> CLOSED: listing closed or removed
        CLOSED --> [*]
    }
```

```mermaid
stateDiagram-v2
    direction LR
    [*] --> PENDING: claim created
    PENDING --> APPROVED: staff approves
    PENDING --> REJECTED: staff rejects (note required)
    APPROVED --> [*]
    REJECTED --> [*]
```

---

## 4. C4 Level 3 Component Diagram: Moderation and Audit Components

Container: **REST API**. The components below belong to module M5.

```mermaid
flowchart LR
    UI["Web UI<br/>(staff and admin screens)"]

    subgraph API["Container: REST API"]
        MOD["Moderation Component<br/>claim decision, listing removal"]
        HAND["Handover Component<br/>handover record, listing status"]
        ADM["Admin Component<br/>categories and users"]
        AUD["Audit Component<br/>write and read audit records"]
        PERM["Permission Checker<br/>one place for role rules"]
        NOTI["Notification Component"]
    end

    DB[("Database")]
    MAIL["E-mail Service<br/>(external)"]

    UI -->|"HTTPS / JSON"| MOD
    UI -->|"HTTPS / JSON"| HAND
    UI -->|"HTTPS / JSON"| ADM
    UI -->|"HTTPS / JSON (admin only)"| AUD

    MOD --> PERM
    HAND --> PERM
    ADM --> PERM
    AUD --> PERM

    MOD -->|"read / write claims, listings"| DB
    HAND -->|"write handover, listing status"| DB
    ADM -->|"read / write categories, users"| DB
    AUD -->|"insert / read audit rows"| DB

    MOD -->|"record decision"| AUD
    HAND -->|"record handover"| AUD
    ADM -->|"record change"| AUD
    MOD -->|"decision notice"| NOTI
    NOTI -->|"SMTP"| MAIL
```

### Component responsibilities

| Component | Responsibility | Related FR |
|---|---|---|
| Moderation Component | Decide claims, remove listings | FR-M5-01, FR-M5-04 |
| Handover Component | Save handover and set listing status | FR-M5-02, FR-M5-03 |
| Admin Component | Manage categories and users | FR-M5-05, FR-M5-06 |
| Audit Component | Write and read append-only audit records | FR-M5-07 |
| Permission Checker | Single place for role rules | NFR-M5-01, NFR-M5-03 |
| Notification Component | Send e-mail about decisions | FR-M5-01 |
