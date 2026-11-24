# M4 – Architecture: Ownership Claim and Verification

**Project:** Campus Lost and Found Portal
**Module owner:** Ceren Torun (@ccerentrn)

> Status names (Pending, Approved, Rejected) must match `ClaimStatus` in `api/openapi.yaml`.

## 1. Sequence Diagram – Create a claim
```mermaid
sequenceDiagram
    actor C as Claimant
    participant UI as Web UI
    participant API as REST API
    participant DB as Database
    C->>UI: Open found item and submit claim form
    UI->>API: POST /items/{id}/claims (token, details)
    API->>API: Validate token and input
    alt Invalid or missing token
        API-->>UI: 401 Unauthorized
    else Invalid input
        API-->>UI: 400 Bad Request
    else Valid
        API->>DB: Load item (type, status, owner)
        DB-->>API: Item
        alt Claimant is the item owner
            API-->>UI: 403 Forbidden
        else Item status is not Found
            API-->>UI: 422 Unprocessable (status is not Found)
        else
            API->>DB: Find claim by (item, claimant)
            DB-->>API: Result
            alt Claim already exists
                API-->>UI: 409 Conflict
            else
                API->>DB: Insert claim (status Pending) in one transaction
                DB-->>API: Claim saved
                API-->>UI: 201 Created (claim)
                UI-->>C: Confirmation with status Pending
            end
        end
    end
```

## 2. State Diagram – Claim status
```mermaid
stateDiagram-v2
    [*] --> Pending: Claim submitted
    Pending --> Approved: Staff approves (M5)
    Pending --> Rejected: Staff rejects (M5)
    Approved --> [*]
    Rejected --> [*]
```

## 3. Class Diagram
```mermaid
classDiagram
    class User {
        +uuid id
        +string fullName
        +UserRole role
    }
    class Item {
        +uuid id
        +ItemType type
        +ItemStatus status
        +uuid ownerId
    }
    class Claim {
        +uuid id
        +uuid itemId
        +uuid claimantId
        +ClaimStatus status
        +datetime createdAt
        +datetime updatedAt
    }
    class VerificationDetail {
        +uuid id
        +uuid claimId
        +string fieldName
        +string fieldValue
    }
    class ClaimStatus {
        <<enumeration>>
        Pending
        Approved
        Rejected
    }
    User "1" --> "0..*" Claim : submits
    Item "1" --> "0..*" Claim : receives
    Claim "1" *-- "1..*" VerificationDetail : contains
    Claim --> ClaimStatus
```

## 4. C4 Level 3 – Component Diagram (Claim component)
Drawn as a Mermaid flowchart that follows C4 component notation (name, responsibility).
```mermaid
flowchart LR
    subgraph Client[Container: Web UI]
        UI[Claim screens<br/>Form, My claims, Status]
    end
    subgraph API[Container: REST API]
        AUTH[Auth and Role Guard<br/>Validates token and role]
        CLAIM[Claim Component<br/>Create, list, status rules]
        LIST[Listing Component<br/>Item type, status, owner]
        NOTIF[Notification Component<br/>Queues e-mail events]
    end
    DB[(Container: Database<br/>claims, verification details)]
    MAIL[[External: E-mail provider]]
    UI -->|HTTPS JSON| AUTH
    AUTH --> CLAIM
    CLAIM -->|Check item rules| LIST
    CLAIM -->|Read and write| DB
    CLAIM -->|Status-change events| NOTIF
    NOTIF -->|SMTP| MAIL
```

## 5. Database Schema (ER Diagram) – M4 tables
`USERS`, `ITEMS`, and `DECISIONS` belong to other modules and are shown as references only. Aslıhan merges all modules into `docs/architecture/database-schema.md`.

```mermaid
erDiagram
    USERS ||--o{ CLAIMS : submits
    ITEMS ||--o{ CLAIMS : receives
    CLAIMS ||--|{ CLAIM_VERIFICATION_DETAILS : has
    CLAIMS ||--o| DECISIONS : "decided by (M5)"

    CLAIMS {
        uuid claim_id PK
        uuid item_id FK
        uuid claimant_id FK
        string status
        datetime created_at
        datetime updated_at
    }
    CLAIM_VERIFICATION_DETAILS {
        uuid detail_id PK
        uuid claim_id FK
        string field_name
        string field_value
    }
    USERS {
        uuid user_id PK
    }
    ITEMS {
        uuid item_id PK
    }
    DECISIONS {
        uuid decision_id PK
        uuid claim_id FK
    }
```
**Constraints:** unique pair (`item_id`, `claimant_id`) on `CLAIMS` enforces NFR-M4-02. `field_name` is one of description, color, brand, contents *(proposed)*.
**License note:** All libraries chosen for this module must be MIT-compatible.
