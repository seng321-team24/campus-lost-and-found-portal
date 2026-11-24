# Architecture – Module M3: Discovery and Notification

**Project:** Campus Lost and Found Portal | **Team:** Team 24
**Module owner:** Aslıhan Kaçmaz (K3) | **Sprint:** 2 | **Status:** Draft for review
**Related:** `docs/architecture/c4-context-container.md` (C4 L1 and L2), `docs/architecture/adr.md`, `api/openapi.yaml`

All diagrams are Mermaid so that they render on GitHub and keep a readable history. Names of classes and fields follow the OpenAPI draft.

## 1. Sequence diagram: search and filter listings

```mermaid
sequenceDiagram
    actor U as User
    participant W as Web UI
    participant A as REST API
    participant S as SearchService
    participant R as ListingRepository
    participant D as Database
    U->>W: enter keyword and filters
    W->>A: GET /items?q=wallet&categoryId=2&page=1
    A->>S: search(criteria)
    S->>S: validate criteria
    alt invalid criteria, for example date range reversed
        S-->>A: validation error
        A-->>W: 400 Error (VALIDATION_ERROR)
        W-->>U: show field message
    else valid criteria
        S->>R: find(criteria)
        R->>D: query listings, newest first
        D-->>R: rows and total count
        R-->>S: ItemPage
        S-->>A: ItemPage
        A-->>W: 200 ItemPage
        W-->>U: show results or empty state
    end
```

## 2. Sequence diagram: e-mail notification after a claim decision

```mermaid
sequenceDiagram
    participant C as Claim and decision component (M4, M5)
    participant N as NotificationService
    participant Q as Notification queue
    participant E as EmailAdapter
    participant P as E-mail provider
    C->>N: notify(CLAIM_DECIDED, claimId)
    N->>Q: enqueue message
    N-->>C: accepted, does not wait for delivery
    Q->>E: deliver message
    E->>P: send e-mail (SMTP)
    alt provider accepts
        P-->>E: accepted
        E-->>Q: mark as sent
    else provider fails
        P-->>E: error
        E-->>Q: retry later, at most 3 attempts, then log failure
    end
```

## 3. Class diagram: discovery and notification

```mermaid
classDiagram
    class Item {
        +int id
        +ItemType type
        +string title
        +string description
        +int categoryId
        +string location
        +date eventDate
        +string photoUrl
        +ItemStatus status
        +int ownerId
        +datetime createdAt
    }
    class Category {
        +int id
        +string name
    }
    class ItemPage {
        +Item[] items
        +int page
        +int pageSize
        +int total
    }
    class ItemSearchCriteria {
        +string q
        +ItemType type
        +int categoryId
        +ItemStatus status
        +string location
        +date dateFrom
        +date dateTo
        +int page
        +int pageSize
    }
    class ItemType {
        <<enumeration>>
        LOST
        FOUND
    }
    class ItemStatus {
        <<enumeration>>
        OPEN
        CLAIM_PENDING
        CLAIMED
        RETURNED
        CLOSED
    }
    class SearchService {
        +search(criteria) ItemPage
        +getItem(id) Item
    }
    class CategoryService {
        +listCategories() Category[]
    }
    class ListingRepository {
        +find(criteria) ItemPage
        +findById(id) Item
    }
    class CategoryRepository {
        +findAll() Category[]
    }
    class NotificationEvent {
        +NotificationType type
        +int recipientId
        +int itemId
        +int claimId
    }
    class NotificationService {
        +notify(event)
    }
    class EmailAdapter {
        +send(message)
    }
    ItemPage o-- Item
    Item --> Category
    Item --> ItemType
    Item --> ItemStatus
    SearchService ..> ItemSearchCriteria
    SearchService --> ListingRepository
    CategoryService --> CategoryRepository
    NotificationService ..> NotificationEvent
    NotificationService --> EmailAdapter
```

Note: `RETURNED` is recorded by staff in module M5 (handover). It must be added to `ItemStatus` in the OpenAPI draft (v0.2.0, see `docs/architecture/openapi-v0.2-changes.md`). The state diagram of listings (module M2) must use the same five values.

## 4. C4 Level 3: components of the REST API for search and notification

```mermaid
flowchart LR
    WEB["Web application<br/>[Container]"]
    DB[("Database<br/>[Container]")]
    SMTP["E-mail provider<br/>[External system]"]
    subgraph API["REST API [Container]"]
        LC["ListingController<br/>[Component]<br/>GET /items, GET /items/{itemId}"]
        CC["CategoryController<br/>[Component]<br/>GET /categories"]
        SS["SearchService<br/>[Component]<br/>validates criteria, runs search"]
        CS["CategoryService<br/>[Component]"]
        LR["ListingRepository<br/>[Component]"]
        CR["CategoryRepository<br/>[Component]"]
        NS["NotificationService<br/>[Component]<br/>queues messages, retries failures"]
        EA["EmailAdapter<br/>[Component]<br/>SMTP client"]
        CL["Claim component<br/>[Component, module M4]"]
        MO["Moderation and handover component<br/>[Component, module M5]"]
    end
    WEB -->|"HTTPS, JSON"| LC
    WEB -->|"HTTPS, JSON"| CC
    LC --> SS
    CC --> CS
    SS --> LR
    CS --> CR
    LR -->|"SQL"| DB
    CR -->|"SQL"| DB
    CL -->|"notify"| NS
    MO -->|"notify"| NS
    NS -->|"queue table"| DB
    NS --> EA
    EA -->|"SMTP"| SMTP
```

| Component | Responsibility | Requirement |
|---|---|---|
| ListingController | Receives list, search and detail requests, maps query parameters to criteria, returns `ItemPage`, `Item` or `Error` | FR-M3-01 to FR-M3-05 |
| CategoryController | Returns the category list | FR-M3-06 |
| SearchService | Validates criteria (for example date range), applies pagination limits, never returns personal data | FR-M3-01 to FR-M3-05, NFR-M3-02 |
| ListingRepository, CategoryRepository | Database access | NFR-M3-01 |
| NotificationService | Receives events from M4 and M5, creates messages for the registered address, queues them, retries failures | FR-M3-07, FR-M3-08 |
| EmailAdapter | Sends one message through the e-mail provider | FR-M3-07 |
