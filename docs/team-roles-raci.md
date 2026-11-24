# Team Roles and RACI — Sprint 1

Project: Campus Lost and Found Portal
Status: Draft for team review. Role assignments supplied by the team; responsibilities follow the Sprint 1 task allocation.

## Team
| ID | Name | GitHub | Role |
|---|---|---|---|
| K1 | İrem Köse | irem-ks | Product Owner |
| K2 | Tuğba Kezban Çay | tugbacay21 | Scrum Master |
| K3 | Aslıhan Kaçmaz | Aslhnnkcmaaz | Architect |
| K4 | Ceren Torun | ccerentrn | QA Engineer |
| K5 | Berra Bayındır | BerraB1 | DevOps Engineer |

## Responsibilities

- **K1 — Product Owner:** Own the initial product backlog (epics, features, stories, acceptance criteria, estimates, dependencies and owners). Prepare the proposal problem statement, supporting survey evidence, stakeholder analysis and expected outcomes. Coordinate the proposal as a whole. Use real evidence; do not invent survey results.
- **K2 — Scrum Master:** Prepare team roles, the RACI matrix and CONTRIBUTIONS.md. Write the proposal assumptions, risks and MIT license rationale. Collect contribution evidence, coordinate the delivery package, prepare the ZIP and submit through Teams with the repository link.
- **K3 — Architect:** Own the OpenAPI YAML draft, request/response schemas and success/error responses. Prepare the system context diagram and external interactions for the vision document.
- **K4 — QA Engineer:** Own System Vision and Boundary: purpose, users, core functions, scope, constraints and assumptions. Validate the OpenAPI draft, review example responses and backlog acceptance criteria. Prepare proposal feasibility and initial release scope.
- **K5 — DevOps Engineer:** Manage repository access, branches, protection rules, issue/PR templates and folders. Merge the reviewed develop branch into main for delivery and create the sprint-1 tag. Perform the final ZIP check.

These are Sprint 1 responsibilities. Implementation tasks for later sprints will be allocated separately; this table does not exempt any member from later development work.

## RACI Matrix

R = Responsible (performs the work); A = Accountable (owns completion); C = Consulted; I = Informed. Each activity has exactly one A; A/R combines ownership and execution.

| Activity | K1 İrem | K2 Tuğba | K3 Aslıhan | K4 Ceren | K5 Berra |
|---|---|---|---|---|---|
| Project Proposal | A/R | R | C | R | C |
| System Vision and Boundary | C | I | R | A/R | I |
| OpenAPI Draft | C | I | A/R | R | I |
| Team Roles and RACI | C | A/R | I | I | I |
| Repository and Git-Flow | I | C | C | C | A/R |
| Initial Product Backlog | A/R | C | C | R | I |
| CONTRIBUTIONS.md | C | A/R | C | C | C |
| ZIP and Teams submission | I | A/R | C | R | R |

> **Note (Sprint 1 actual delivery):** By later team agreement, the Sprint 1 ZIP was prepared and submitted on Teams by Berra (K5), as recorded in `CONTRIBUTIONS.md`. The row above shows the original allocation.

For CONTRIBUTIONS.md, all members provide their actual work and evidence links to K2. This makes explicit the information collection needed by the original allocation.

## Contribution and Review Workflow

1. Each member opens an issue and assigns it to themselves.
2. Work on a branch from `develop` named `feature/<issue-number>-<short-name>`.
3. Commit using your own account and open a pull request targeting develop.
4. Review assignments: K1 reviews K2; K2 reviews K3; K3 reviews K4; K4 reviews K5; K5 reviews K1.
5. Record actual issue, PR and commit links in CONTRIBUTIONS.md. Review unresolved questions with the author; do not approve work without understanding the changes.
6. K2 collects the reviewed documents; K5 prepares the final merge/tag and checks the archive; K2 submits the agreed package and repository link.

---

## Sprint 2 Update

Status: Sprint 2 (24 November 2026). Roles are unchanged. Each member owns one module (M1–M5) with its SRS, NFR, user flow, Figma screens and UML.

| ID | Sprint 2 module |
|---|---|
| K1 İrem | M1 Identity, auth and profile; main SRS and traceability |
| K2 Tuğba | M2 Listing management |
| K3 Aslıhan | M3 Search and discovery; C4 L1–L2; OpenAPI updates |
| K4 Ceren | M4 Claims; main NFR document |
| K5 Berra | M5 Approval and admin; Figma main file and style guide |

### Sprint 2 RACI (delivery activities)

| Activity | K1 İrem | K2 Tuğba | K3 Aslıhan | K4 Ceren | K5 Berra |
|---|---|---|---|---|---|
| M1 module documents | A/R | I | C | C | I |
| M2 module documents | C | A/R | C | C | I |
| M3 module documents | C | I | A/R | C | I |
| M4 module documents | C | I | C | A/R | C |
| M5 module documents | C | I | C | C | A/R |
| Product backlog refinement | A/R | C | C | C | C |
| OpenAPI updates | C | C | A/R | C | I |
| Figma main file and style guide | I | C | C | C | A/R |
| CONTRIBUTIONS.md (Sprint 2) | C | A/R | C | C | C |
| Merge to main, `sprint-2` tag and release | I | C | I | I | A/R |
| ZIP and Teams submission | I | A/R | I | I | C |

The ZIP and Teams submission owner is **Tuğba (K2)** in Sprint 2; this matches CONTRIBUTIONS.md. Berra (K5) creates the tag and performs the final archive check (C).
