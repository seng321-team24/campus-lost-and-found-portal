# Proposal Section: Feasibility and First Release Scope

**Project:** Campus Lost and Found Portal
**Prepared by:** K4 – QA Engineer

---

## 1. Feasibility Analysis

### 1.1 Technical feasibility
All required features (user management, CRUD-based listings, search, file upload, email notifications) can be built with common, mature, open-source technologies. A standard web stack available to the team (REST API + relational database + responsive web interface) is sufficient. There is no need for special hardware or high scalability.
**Result:** Feasible. Low risk.

### 1.2 Operational feasibility
The success of the portal depends on found items being physically collected at one office (security / student affairs). A staff role is defined for this process. The user base (students and staff) is used to web applications, so training needs are minimal.
**Result:** Feasible; the assumption of staff office cooperation is tracked as a risk.

### 1.3 Economic feasibility
Development cost is student labor. Free/student hosting plans and open-source tools are sufficient. Since the license is MIT, there are no licensing costs.
**Result:** Feasible.

### 1.4 Schedule feasibility
Sprint 1 covers only documentation and repository setup. The first release scope (see Section 2) has therefore been kept small and prioritized.
**Result:** Feasible if scope is kept limited.

### 1.5 Legal and ethical feasibility
Personal data falls under KVKK: only minimal data is collected, contact details are hidden on listing pages, and users can delete their own data. Ownership verification is mandatory to prevent someone from collecting another person's item.
**Result:** Feasible; privacy must be treated as a design requirement.

### 1.6 Feasibility summary

| Dimension | Assessment | Risk |
|---|---|---|
| Technical | Feasible | Low |
| Operational | Feasible | Medium (staff office participation) |
| Economic | Feasible | Low |
| Schedule | Feasible if scope is limited | Medium |
| Legal/Ethical | Feasible | Medium (KVKK) |

---

## 2. First Release (MVP) Scope

The goal of the first release is to run the **main flow from losing an item to returning it to its owner** end to end.

### 2.1 Included in the first release (Must have)
1. User registration and sign-in
2. Create, list, view, and close lost item listings
3. Create, list, and view found item listings
4. Search by keyword and category
5. Submit an ownership claim
6. Staff approval/rejection of the claim and handover record
7. Role-based authorization (user, staff, admin)

### 2.2 Nice to have (Should have)
- Adding photos to listings
- Email notifications
- Advanced filtering by date and location
- Admin removal of inappropriate listings

### 2.3 Deferred to later releases (Won't have – for now)
- Mobile app, SSO integration, automatic match suggestions, multi-language support, statistics dashboard

### 2.4 First release acceptance criteria (Release criteria)
- Acceptance criteria for all Must have items are met.
- The OpenAPI file passes linting without errors and the endpoints work according to this schema.
- There are no open critical (blocker) defects.
- All changes are merged into `develop` via PR and reviewed by at least one person.

---

## 3. Main Feasibility-Related Risks

| # | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R1 | Staff office does not take part in the process | Medium | High | Early meeting; if needed, the admin takes over the staff role |
| R2 | Schedule is insufficient, scope grows | Medium | Medium | MoSCoW prioritization; non-Must-have work is postponed |
| R3 | Fraudulent ownership claim | Medium | High | Distinguishing-detail question and staff verification |
| R4 | Personal data leak | Low | High | Minimal data, hidden contact details, password hashing |
| R5 | Uneven distribution of contributions among team members | Medium | Medium | Mandatory issue/PR use, tracking in CONTRIBUTIONS.md |
