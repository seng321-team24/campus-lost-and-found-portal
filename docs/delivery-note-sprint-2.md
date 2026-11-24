# Sprint 2 Delivery Note – Team 24

| | |
|---|---|
| **Course / team** | SENG 321 – Team 24 |
| **Project** | Campus Lost and Found Portal |
| **Sprint** | Sprint 2 (deadline 24 November 2026, 23:59) |
| **Repository** | https://github.com/seng321-team24/campus-lost-and-found-portal |
| **Release** | `sprint-2` tag on `main` – https://github.com/seng321-team24/campus-lost-and-found-portal/releases/tag/sprint-2 (created by Berra after this note is merged) |
| **Delivered commit** | The commit the `sprint-2` tag points to (shown on the release page). |
| **ZIP source** | `main` at the `sprint-2` tag (team Git-Flow policy). Fallback only if the tag is not created before the deadline: latest `develop`, with the commit SHA written in the Teams submission comment. |
| **Submitted by** | Tuğba Kezban Çay (K2, Scrum Master) on Microsoft Teams – issue [#51](https://github.com/seng321-team24/campus-lost-and-found-portal/issues/51) |

## Team and roles
| ID | Name | GitHub | Role | Sprint 2 module |
|---|---|---|---|---|
| K1 | İrem Köse | irem-ks | Product Owner | M1 Identity, auth and profile; main SRS |
| K2 | Tuğba Kezban Çay | tugbacay21 | Scrum Master | M2 Listing management; delivery package |
| K3 | Aslıhan Kaçmaz | Aslhnnkcmaaz | Architect | M3 Search and discovery; C4 L1–L2; OpenAPI |
| K4 | Ceren Torun | ccerentrn | QA Engineer | M4 Claims; main NFR |
| K5 | Berra Bayındır | BerraB1 | DevOps Engineer | M5 Approval and admin; Figma main file; tag/release |

Issue, PR, commit and review links per member: see [CONTRIBUTIONS.md](https://github.com/seng321-team24/campus-lost-and-found-portal/blob/develop/CONTRIBUTIONS.md) (Sprint 2 section).

## Contents (status at ZIP creation)

Status is checked against the archived branch when the ZIP is created. *Pending* = file is still a skeleton or its PR is not merged. Status checked on `develop` at 24 Nov 2026, 22:50; re-checked on the archived commit when the ZIP is created.

| Path | Content | Status |
|---|---|---|
| [`api/openapi.yaml`](https://github.com/seng321-team24/campus-lost-and-found-portal/blob/develop/api/openapi.yaml) | API contract | Included |
| [`docs/srs/00-main.md`](https://github.com/seng321-team24/campus-lost-and-found-portal/blob/develop/docs/srs/00-main.md) | Main SRS + traceability | Included |
| [`docs/srs/`](https://github.com/seng321-team24/campus-lost-and-found-portal/tree/develop/docs/srs) (`m1-auth.md` … `m5-admin.md`) | Module SRS + refined backlog | Included (M1–M5) |
| [`docs/nfr/`](https://github.com/seng321-team24/campus-lost-and-found-portal/tree/develop/docs/nfr) (`00-main.md`, `m1`–`m5`) | NFRs | Included (main, M1–M5) |
| [`docs/ux/flows/`](https://github.com/seng321-team24/campus-lost-and-found-portal/tree/develop/docs/ux/flows) (`m1`–`m5`) | User flows (Mermaid) | Included (M1–M5) |
| [`docs/ux/figma-links.md`](https://github.com/seng321-team24/campus-lost-and-found-portal/blob/develop/docs/ux/figma-links.md) | Figma and prototype links | Included |
| [`c4-context-container.md`](https://github.com/seng321-team24/campus-lost-and-found-portal/blob/develop/docs/architecture/c4-context-container.md), [`deployment.md`](https://github.com/seng321-team24/campus-lost-and-found-portal/blob/develop/docs/architecture/deployment.md) | C4 L1–L2, deployment | Included |
| [`docs/architecture/`](https://github.com/seng321-team24/campus-lost-and-found-portal/tree/develop/docs/architecture) (`m1`–`m5-uml.md`) | Module UML | Included (M1–M5) |
| [`CONTRIBUTIONS.md`](https://github.com/seng321-team24/campus-lost-and-found-portal/blob/develop/CONTRIBUTIONS.md), [`docs/team-roles-raci.md`](https://github.com/seng321-team24/campus-lost-and-found-portal/blob/develop/docs/team-roles-raci.md) | Contributions and roles | Included (issue #51) |

**ZIP file name:** `Team24_Sprint2_campus-lost-and-found-portal.zip`  
**Archived from:** `main` at tag `sprint-2`

## Design links
- Figma team file: https://www.figma.com/design/ahb5vkWYGgXs1UmzzxNOvX
- M2 prototype starting points (page `02 Listings`): "M2 – My Listings" and "M2 – Empty State (first listing)"
- M2 user flow (FigJam, diagram "v3 – CURRENT"): https://www.figma.com/board/26Rv54kRU2JEZrfHzS40lt/M2-Listing-Management---User-Flow--v2-

## Known open decisions
- `handoverPoint` for found items is now in `api/openapi.yaml` v0.2.0 (PR #81).
- 5 MB photo limit (JPEG/PNG) confirmed by the M2 owner in PR #79; to be re-checked by the team in Sprint 3.
- OpenAPI v0.2.0 (PR #81, merged) adds the `RETURNED` status; the M2 SRS and state diagram will be updated to include it in Sprint 3.
- New M2 stories US-30, US-31, US-32 pending Product Owner (İrem) approval.
- `CLAIMED → CLOSED` transition and deleting listings with an active claim (M4/M5).
- PR #41 uses the older branch name `feature/m2-srs-backlog` (created before the naming rule was applied); all later M2 branches follow `feature/<issue-no>-<name>`.
