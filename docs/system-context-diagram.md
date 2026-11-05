# System Context Diagram and External Interactions

**Project:** Campus Lost and Found Portal | **Team:** Team 24
**Sprint 1 Deliverable | Software Architect (Aslıhan Kaçmaz)**

This document supports the System Vision and Boundary deliverable. It is aligned with the Project Proposal (`project_proposal.md`, sections 3 and 6), the Initial Product Backlog (`initial_product_backlog.md`) and the OpenAPI draft (`api/openapi.yaml`).

## 1. System Context Diagram

```mermaid
flowchart LR
    U["Student / Staff<br/>(registered user)"]
    M["Moderator / Administrator"]
    S["Campus Lost and Found Portal<br/>(web application + REST API)"]
    F["File storage<br/>(item photos)"]
    I["University information systems<br/>(not integrated in the first version)"]

    U -->|"register, log in, create lost/found listings,<br/>upload photos, submit claims"| S
    U -->|"browse, search, filter, view details"| S
    S -->|"listings, details, claim results"| U
    M -->|"review claims, remove inappropriate listings"| S
    S -->|"claims and listings to review"| M
