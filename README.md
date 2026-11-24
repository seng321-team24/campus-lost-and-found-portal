# campus-lost-and-found-portal

A web system where students and staff can post lost and found items, submit ownership claims, and track the outcome in one place. Developed by Team 24 for SENG 321 Software Engineering.

> **Status: Sprint 2 (Requirements, Architecture & UI/UX Design).** This repository contains the Software Requirements Specification (SRS), Non-Functional Requirements (NFR), system architecture diagrams, OpenAPI contract (`v0.2.0`), and interactive Figma prototype links. Implementation begins in Sprint 3.

## Problem

There is no shared place on campus for lost and found items. Announcements are scattered across WhatsApp groups and notice boards, so people who lose something do not know where to look.

## Solution

A web portal with a simple flow: **post an item, submit a claim, approve it.**

* Publish lost and found item listings, with search and filtering
* Submit an ownership claim for a listing
* Moderator/staff review of claims (approve or reject) and physical handover tracking
* Track the status of each listing and claim with full audit logging

## Repository structure

```text
.
├── api/                     OpenAPI contract (api/openapi.yaml)
├── docs/
│   ├── architecture/        C4 diagrams, ADRs, and deployment architecture
│   ├── nfr/                 Non-functional requirements, metrics, and test strategy
│   ├── srs/                 Main SRS (00-main.md) and module specifications (M1–M5)
│   ├── ux/                  User flows (flows/), screen captures (screens/), and Figma links
│   ├── git-flow.md          Branching and PR workflow policy
│   ├── system-context-diagram.md
│   └── team-roles-raci.md   Team roles and RACI matrix
├── .github/                 Issue and pull request templates
├── CONTRIBUTIONS.md         Individual contributions of each team member
├── LICENSE                  MIT License
└── README.md
```

## Key documents

* **Requirements & Architecture**
  * [Main SRS Document](docs/srs/00-main.md)
  * [Deployment Architecture](docs/architecture/deployment.md)
  * [System Context Diagram](docs/system-context-diagram.md)
  * [OpenAPI Specification](api/openapi.yaml)
* **UX & Prototyping**
  * [Figma Prototype & Screen Links](docs/ux/figma-links.md)
* **Governance & Process**
  * [Git-Flow Policy](docs/git-flow.md)
  * [Team Roles and RACI](docs/team-roles-raci.md)
  * [Contributions](CONTRIBUTIONS.md)

## Team

| Name | GitHub | Role |
|---|---|---|
| İrem Köse | [@irem-ks](https://github.com/irem-ks) | Product Owner |
| Tuğba Kezban Çay | [@tugbacay21](https://github.com/tugbacay21) | Scrum Master |
| Aslıhan Kaçmaz | [@Aslhnnkcmaaz](https://github.com/Aslhnnkcmaaz) | Architect |
| Ceren Torun | [@ccerentrn](https://github.com/ccerentrn) | QA Engineer |
| Berra Bayındır | [@BerraB1](https://github.com/BerraB1) | DevOps Engineer |

## Workflow

We use a lightweight Git-Flow model:

* `main`: assessed and released versions only, tagged per sprint (`sprint-1`, `sprint-2`, ...)
* `develop`: integrated work for the next release (default branch)
* `feature/<issue-no>-<short-name>`: short-lived branch for one issue

No direct pushes to `main` or `develop`. Every change starts with an issue, goes through a pull request, and needs at least one teammate's review. See [docs/git-flow.md](docs/git-flow.md) for the full rules.

## License

Released under the [MIT License](LICENSE).
