# Architecture M2 - Listing Management

**Module owner:** Tugba Kezban Cay (tugbacay21)  
**Related:** `docs/srs/m2-listings.md`, `docs/nfr/m2.md`, `api/openapi.yaml`, `docs/architecture/c4-context-container.md`  
**Related issue:** #50  
**Status:** Draft for team review

This document contains the M2 parts of the architecture: a sequence diagram for creating a listing and uploading a photo, a state diagram for the listing status (`ItemStatus`), and the C4 Level 3 (component) view of the listing part of the REST API. All diagrams use Mermaid.

## 1. Sequence Diagram - Create Listing and Upload Photo (UC-M2-02)

Covers FR-M2-02 and FR-M2-06. The listing is created first (`POST /items`). The user may choose a photo while filling in the form (client-side check and local preview, same as the user flow), but the upload (`POST /items/{itemId}/photo`) is sent only after `201 Created`, because it needs the new listing id (`itemId` = `id` in the `201` response). A failed upload does not lose the listing (UC-M2-02, A4). The Photo Service only stores the file; the Listing Service writes the returned `photoUrl` to the item.

```mermaid
sequenceDiagram
    autonumber
    actor U as User
    participant W as Web UI
    participant A as REST API (Controllers)
    participant S as ListingService
    participant P as PhotoService
    participant DB as Database
    participant FS as Photo Storage

    U->>W: Fill found-item form
    opt User adds a photo in the form
        U->>W: Choose photo
        W->>W: Client-side check (JPG/PNG, max 5 MB) and local preview
    end
    U->>W: Submit form
    W->>A: POST /items (Bearer token, ItemCreate, type=FOUND)
    A->>A: Validate token (M1 Auth)
    alt Token missing or expired
        A-->>W: 401 Unauthorized
        W-->>U: Ask user to sign in, form and photo kept
    else Token valid
        A->>S: createItem(ownerId, data)
        S->>S: Validate required fields
        alt Required field missing
            S-->>A: Validation error
            A-->>W: 400 Bad Request
            W-->>U: Show field errors, no listing created
        else Valid
            S->>DB: INSERT item (status = OPEN)
            DB-->>S: id
            S-->>A: Item
            A-->>W: 201 Created (Item, itemId = Item.id)
            opt A photo was chosen in the form (upload only after 201)
                W->>A: POST /items/{itemId}/photo (multipart/form-data)
                A->>A: Validate token (M1 Auth)
                alt Token expired during upload
                    A-->>W: 401 Unauthorized
                    W-->>U: Sign in again, listing already saved, retry upload with same itemId
                else Token valid
                    A->>S: checkOwner(itemId, userId)
                    alt Not the owner
                        S-->>A: Forbidden
                        A-->>W: 403 Forbidden
                    else Owner
                        A->>P: validate(file)
                        alt Type is not JPEG/PNG
                            P-->>A: Unsupported type
                            A-->>W: 415 Unsupported Media Type
                            W-->>U: Listing saved, only JPG and PNG files are supported
                        else Size over 5 MB
                            P-->>A: Too large
                            A-->>W: 413 Payload Too Large
                            W-->>U: Listing saved, file must be 5 MB or smaller
                        else Valid file
                            P->>FS: Store file
                            FS-->>P: photoUrl
                            P-->>A: photoUrl
                            A->>S: attachPhoto(itemId, photoUrl)
                            S->>DB: UPDATE item SET photoUrl
                            S-->>A: Item
                            A-->>W: 200 OK (Item with photoUrl)
                        end
                    end
                end
            end
            W-->>U: Listing published (with photo if uploaded)
        end
    end
```

## 2. State Diagram - Listing Status (`ItemStatus`)

Values are taken exactly from `ItemStatus` in `api/openapi.yaml`: `OPEN`, `CLAIM_PENDING`, `CLAIMED`, `CLOSED`. The listing type (`LOST` / `FOUND`) is a separate field (`ItemType`) and does not change.

```mermaid
stateDiagram-v2
    [*] --> OPEN: Listing created (POST /items)
    OPEN --> CLAIM_PENDING: Claim submitted (M4)
    CLAIM_PENDING --> CLAIMED: Claim accepted (M5)
    CLAIM_PENDING --> OPEN: Claim rejected (M5)
    OPEN --> CLOSED: Owner closes listing (PUT status=CLOSED)
    OPEN --> [*]: Deleted (provisional rule)
    CLOSED --> [*]: Deleted (provisional rule)
```

> **Provisional:** OpenAPI already defines **who** may delete a listing (the owner or a moderator, `204`). It does **not** define **in which statuses** deletion is allowed - that is a team decision with M4/M5. Until it is decided, deletion is drawn only from `OPEN` and `CLOSED`; no delete transition is drawn from `CLAIM_PENDING` or `CLAIMED`.

| Transition | Who | Module | Rule |
| --- | --- | --- | --- |
| `[*] → OPEN` | Listing owner | M2 | Every new listing starts as `OPEN`. |
| `OPEN → CLOSED` | Listing owner | M2 | FR-M2-04. Only from `OPEN`; rejected from `CLAIM_PENDING` and `CLAIMED`. |
| `OPEN → CLAIM_PENDING` | Claimant | M4 | A claim is submitted. |
| `CLAIM_PENDING → CLAIMED / OPEN` | Staff | M5 | Claim decision (`/claims/{claimId}/decision`). |
| `CLAIMED → CLOSED` | - | M5 | **Not in the diagram (open decision).** FR-M2-04 does not allow the owner to close a `CLAIMED` listing. Whether staff close it after handover is decided with M5 (Berra). |
| Delete | Owner or moderator | M2 / M5 | FR-M2-05. **Open decision:** deleting a listing with an active claim. |

## 3. C4 Level 3 - Component View (Listing part of the REST API)

Zooms into the **REST API** container from the C4 Level 2 diagram. Only the components used by M2 are shown; components of other modules appear in grey as neighbours.

```mermaid
flowchart LR
    user(["Campus user"])
    web["Web UI<br/>(Container)"]

    subgraph api ["REST API (Container)"]
        auth["Auth Filter<br/>(M1) validates bearer token"]
        ctrl["Listing Controller<br/>/items, /items/{itemId},<br/>/users/me/items"]
        photoCtrl["Photo Controller<br/>/items/{itemId}/photo"]
        svc["Listing Service<br/>validation, owner checks,<br/>status rules"]
        photoSvc["Photo Service<br/>type and size checks"]
        repo["Item Repository<br/>data access"]
        search["Search Component (M3)"]
        claims["Claim Component (M4)"]
    end

    db[("Database<br/>(Container)")]
    fs[("Photo Storage<br/>(Container)")]

    user --> web
    web -->|"HTTPS: JSON, multipart/form-data for photos"| auth
    auth --> ctrl
    auth --> photoCtrl
    ctrl --> svc
    photoCtrl -->|"1. Validate and store file"| photoSvc
    photoCtrl -->|"2. Save photoUrl"| svc
    svc --> repo
    photoSvc -->|"Stores files"| fs
    repo -->|"SQL"| db
    search -.->|"Reads items"| repo
    claims -.->|"Changes status"| svc

    style search fill:#EEEEEE,stroke:#999999
    style claims fill:#EEEEEE,stroke:#999999
    style api fill:#F5F8FF,stroke:#3B5BDB
```

| Component | Responsibility | Related FR / NFR |
| --- | --- | --- |
| Listing Controller | REST endpoints for create, read, update, delete and "my listings"; maps errors to 400/401/403/404 | FR-M2-01…05, FR-M2-07 |
| Photo Controller | Receives multipart upload; returns 413/415 | FR-M2-06, NFR-M2-03 |
| Listing Service | Required-field validation, owner check, allowed status transitions, saving `photoUrl` to the item | FR-M2-03, FR-M2-04, NFR-M2-04 |
| Photo Service | Checks file content type (JPEG/PNG) and size (≤ 5 MB), stores the file and returns `photoUrl`; does not access the database | NFR-M2-03 |
| Item Repository | Reads and writes items in the database | NFR-M2-01 |
| Auth Filter (M1) | Validates the bearer token and provides the current user id | NFR-M2-04 |

## 4. Consistency Notes

- Names (`ItemStatus`, `ItemType`, `itemId`, endpoints) match `api/openapi.yaml`.
- Container names (Web UI, REST API, Database, Photo Storage) must match Aslıhan's C4 L1-L2 document; rename here if that document uses different names.
- The found-item handover point (FR-M2-02) is not yet in the OpenAPI schema; see the open decisions in `docs/srs/m2-listings.md`.
