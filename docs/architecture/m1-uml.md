# Module 1 (M1) — Architecture & UML Specifications

**Module Owner:** İrem Köse (K1 — Product Owner)

**Scope:** Sequence Diagram, Class Diagram, and C4 Level 3 Component Diagram for Authentication & User Management.

## 1. Sequence Diagram: User Authentication (Login Flow)

```mermaid
sequenceDiagram
    autonumber
    actor User as Campus User
    participant FE as Frontend App (Web Client)
    participant AuthAPI as Auth Controller (REST API)
    participant DB as PostgreSQL Database
    
    User->>FE: Enter Email & Password, click "Log In"
    FE->>AuthAPI: POST /auth/login { email, password }
    
    AuthAPI->>DB: SELECT * FROM users WHERE email = ?
    DB-->>AuthAPI: User Record (id, email, password_hash, role)
    
    alt User Not Found or Password Mismatch
        AuthAPI-->>FE: HTTP 401 Unauthorized { message: "Invalid credentials" }
        FE-->>User: Display Error Banner
    else Password Match Verified (bcrypt)
        AuthAPI->>AuthAPI: Generate JWT Token (payload: userId, role, exp)
        AuthAPI-->>FE: HTTP 200 OK { token: "eyJhbG...", user: { id, name, role } }
        FE->>FE: Store JWT in LocalStorage / Cookie
        FE-->>User: Redirect to User Profile / Dashboard
    end
```

## 2. Class Diagram: User and Role Domain Model

```mermaid
classDiagram
    class User {
        +UUID id
        +String fullName
        +String email
        +String passwordHash
        +UserRole role
        +Boolean kvkkConsent
        +DateTime createdAt
        +register()
        +login()
        +getProfile()
    }

    class UserRole {
        <<enumeration>>
        STUDENT
        STAFF
        ADMINISTRATOR
    }

    class AuthTokenPayload {
        +UUID userId
        +String email
        +UserRole role
        +Long exp
    }

    User "1" -- "1" UserRole : assigned
    User ..> AuthTokenPayload : generates
```

## 3. C4 Level 3 Component Diagram: Auth & Identity Component

```mermaid
C4Component
    title Component Diagram for M1 - Auth & Identity Architecture

    Container(web_app, "Single Page Web App", "React / HTML5", "Provides UI for registration, login, profile")
    
    Container_Boundary(api_app, "API Backend Application") {
        Component(auth_ctrl, "Auth Controller", "REST Controller", "Handles /auth/register and /auth/login HTTP endpoints")
        Component(user_ctrl, "User Controller", "REST Controller", "Handles /users/me HTTP endpoints")
        Component(auth_service, "Authentication Service", "Service Layer", "Encapsulates password verification & JWT creation")
        Component(user_service, "User Service", "Service Layer", "Manages user persistence & profile lookups")
        Component(security_filter, "JWT Security Filter", "Middleware", "Validates incoming HTTP Bearer tokens")
    }

    ContainerDb(database, "Database", "PostgreSQL", "Stores user accounts, credentials, and roles")

    Rel(web_app, auth_ctrl, "Submits credentials / reg data", "JSON/HTTPS")
    Rel(web_app, user_ctrl, "Requests profile with Bearer Token", "JSON/HTTPS")
    Rel(user_ctrl, security_filter, "Validates token")
    Rel(auth_ctrl, auth_service, "Delegates auth logic")
    Rel(user_ctrl, user_service, "Delegates profile logic")
    Rel(auth_service, user_service, "Fetches user entity")
    Rel(user_service, database, "Reads/Writes user records", "SQL")
```

## Database ER Diagram (M1)
 
```mermaid
erDiagram
    USERS {
        string id PK
        string full_name
        string email UK
        string phone_number
        string password_hash
        string role
        boolean kvkk_accepted
        string kvkk_accepted_at
        string created_at
        string updated_at
    }
 
    USER_SESSIONS {
        string session_id PK
        string user_id FK
        string token_jti UK
        string expires_at
        string revoked_at
        string created_at
    }
 
    USERS ||--o{ USER_SESSIONS : "has active or past sessions"
 ```
