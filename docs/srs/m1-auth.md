# Module 1 (M1) — Identity, Authentication, and Profile Specification

**Module Owner:** İrem Köse (K1 — Product Owner)  
**Module Scope:** User registration, form validation, authentication, session logout, role-based access control, user profile management, and KVKK/privacy consent.

---

## 1. Functional Requirements (FR)

### `FR-M1-01`: User Registration
The system shall allow new users (students, staff) to register by providing a valid institutional email address, full name, phone number (optional), and a secure password.

### `FR-M1-02`: Registration Input Validation
The system shall validate all registration inputs prior to account creation. Email addresses must end with an approved university domain (`.edu.tr`), and passwords must meet minimum security criteria (minimum 8 characters, containing at least one uppercase letter, one lowercase letter, and one numeric digit).

### `FR-M1-03`: User Authentication (Login)
The system shall authenticate registered users using their registered email address and password. Upon successful authentication, the system shall issue a secure JSON Web Token (JWT) containing the user's identity and assigned role.

### `FR-M1-04`: Session Termination (Logout)
The system shall allow authenticated users to log out safely, invalidating the local JWT session state on the client application.

### `FR-M1-05`: Role-Based Access Control (RBAC)
The system shall enforce role assignments for every user account. Supported system roles shall include:
* `Student` (default for standard registrants)
* `Staff` (university lost and found management personnel)
* `Administrator` (system administrators)

### `FR-M1-06`: Profile Viewing and Management
The system shall provide authenticated users with access to their own user profile endpoint (`GET /users/me`), displaying their account details, current role, and registered date without exposing sensitive cryptographic hashes.

### `FR-M1-07`: Data Privacy Consent (KVKK Acceptance)
The system shall require users to explicitly accept the KVKK / Privacy Policy checkbox during registration before account activation is completed.

---

## 2. Use Cases

### Use Case `UC-M1-01`: User Account Registration

* **Primary Actor:** Unauthenticated Visitor (Student/Staff)
* **Preconditions:** The user is on the portal registration page and does not have an active account.
* **Postconditions:** A new user account is created with the `Student` role, and a confirmation response is returned.

#### Main Success Scenario
1. User navigates to the registration page.
2. User fills in full name, university email (`name@university.edu.tr`), password, and checks the KVKK consent box.
3. User submits the registration form.
4. System validates email domain, password strength, and KVKK check.
5. System hashes the password using bcrypt/Argon2.
6. System stores user record in the database with role `Student`.
7. System returns HTTP 201 Created and redirects the user to the login page.

#### Alternative Flows
* **A1: Duplicate Email Address**
  * 4a. System detects email already exists in the database.
  * 4b. System displays error message: *"An account with this email address already exists."*
  * 4c. User is prompted to log in or reset password.
* **A2: Invalid University Email Domain**
  * 4a. System detects email does not end with recognized university domain.
  * 4b. System displays error message: *"Registration requires a valid university email address."*
* **A3: KVKK Consent Not Checked**
  * 4a. User leaves KVKK consent checkbox unchecked.
  * 4b. System prevents form submission and highlights mandatory consent field.

---

### Use Case `UC-M1-02`: User Authentication (Login)

* **Primary Actor:** Registered User (Student / Staff / Admin)
* **Preconditions:** User has a registered account.
* **Postconditions:** User receives an authentication token and gains access to protected features.

#### Main Success Scenario
1. User navigates to the login page.
2. User enters registered email and password.
3. User submits login form.
4. System retrieves user record by email.
5. System verifies password hash against stored hash.
6. System generates JWT containing User ID, email, and role.
7. System returns HTTP 200 OK with the JWT payload.
8. Client stores token and redirects user to dashboard.

#### Alternative Flows
* **A1: Invalid Credentials**
  * 5a. System detects non-matching password or unverified email.
  * 5b. System returns HTTP 401 Unauthorized with generic message: *"Invalid email or password."*
  * 5c. User remains on login page with cleared password field.

---

## 3. Product Backlog Items — Module 1 User Stories

### `US-01`: User Account Registration
* **As a** campus student or staff member  
* **I want to** register for an account using my university email  
* **So that** I can create lost item reports and submit ownership claims.  
* **Priority:** High  
* **Effort:** 5 Story Points  
* **Dependencies:** None  
* **Acceptance Criteria:**
  ```gherkin
  Given I am on the registration page
  When I submit a valid university email, strong password, and check KVKK consent
  Then my account should be created with HTTP 201 Created status
  And I should be redirected to the login page.
  ```

### `US-02`: User Authentication & Token Refresh
* **As a** registered campus user  
* **I want to** log in securely with my credentials  
* **So that** I can access my profile and protected system capabilities.  
* **Priority:** High  
* **Effort:** 3 Story Points  
* **Dependencies:** `US-01`  
* **Acceptance Criteria:**
  ```gherkin
  Given I am a registered user on the login page
  When I enter my valid email and password
  Then the system returns a valid JWT token with HTTP 200 OK
  And I gain access to my user profile page.
  ```

### `US-30`: User Profile Management
* **As an** authenticated user  
* **I want to** view my personal profile details  
* **So that** I can verify my registered information and user role.  
* **Priority:** Medium  
* **Effort:** 2 Story Points  
* **Dependencies:** `US-02`  
* **Acceptance Criteria:**
  ```gherkin
  Given I am authenticated with a valid JWT token
  When I send a request to GET /users/me
  Then I should receive my name, email, role, and registration date in JSON format
  And my password hash must never be exposed.
  ```
