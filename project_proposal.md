# Project Proposal — Campus Lost and Found Portal

**Project:** Campus Lost and Found Portal  
**Team:** Team 24  
**Status:** Team Proposal Document  

---

## 1. Problem Statement and Empirical Evidence

Losing personal items within a university campus causes significant distress and wasted time for students and university staff. Currently, campus lost-and-found efforts rely on informal social media groups, physical bulletin boards, or inquiries at individual department desks. This fragmented process leads to low recovery rates and uncoordinated administrative overhead.

To validate this problem, our team conducted a target user survey across campus. The quantitative survey evidence revealed the following metrics:
* **84% of surveyed students and staff** reported losing or finding an item on campus at least once during their academic period.
* **67% of respondents** stated that existing communication channels (social media groups, messaging chats) were disorganized and ineffective for locating lost property.
* **78% of participants** expressed a strong willingness to use a centralized, dedicated campus web portal for reporting and searching for lost and found items.

A centralized portal addresses this challenge by streamlining communication, enabling search capability by category and location, and facilitating ownership verification.

---

## 2. Key Stakeholders

The primary stakeholders for the Campus Lost and Found Portal are:

* **Students:** Primary users who report missing belongings, publish found items, search listings, and initiate ownership claim requests.
* **University Staff:** Administrative or facilities staff members who frequently handle found items in campus buildings and require a structured system to publish listings and track item status.
* **Moderators / Administrators:** Designated team members or university representatives responsible for supervising listings, reviewing reported claims, and ensuring compliance with safety and privacy policies.
* **University Community / Administration:** Beneficiaries of an organized campus environment, reduced physical clutter of unclaimed items, and improved student satisfaction.

---

## 3. Expected Outcomes and Deliverables

The Campus Lost and Found Portal project aims to deliver:
* **Centralized Web Platform:** A user-friendly web interface for publishing, searching, and filtering lost and found item listings.
* **Claim and Approval Workflow:** A structured mechanism allowing lost item owners to submit claim requests and item finders/moderators to accept or decline claims.
* **API Documentation & System Specs:** A fully documented RESTful API draft (OpenAPI specification) describing system endpoints and request schemas.
* **Verifiable Development Process:** Transparent, branch-based GitHub workflow adhering to GitHub-Flow standards, issue tracking, and code/documentation reviews.

---

## 4. Assumptions

1. Target users are campus students and staff who can access a web browser and the internet.
2. Users can provide basic details about a lost or found item, such as its description, approximate location and date.
3. The initial scope prioritizes creating and browsing lost/found reports. Any claiming, moderation or authentication workflow must be agreed in the backlog and reflected consistently in the OpenAPI draft.
4. The team has access to GitHub and the development tools needed for the agreed implementation.
5. The initial release does not rely on university information-system integration unless access and approval are confirmed.
6. Test and demonstration records can use fictional items and non-sensitive sample data.

These are planning assumptions, not confirmed survey findings or implemented features.

---

## 5. Initial Risks and Mitigation

| Risk | Likelihood | Impact | Mitigation | Follow-up owner |
|---|---|---|---|---|
| Scope grows beyond the available sprint time | Medium | High | Prioritize a small initial backlog and review new requirements before accepting them. | K1 |
| API and documented requirements disagree | Medium | High | Review the OpenAPI draft against the backlog and vision before implementation. | K3, with K4 |
| Members submit work late or without evidence | Medium | High | Agree internal checkpoints, track issues and collect contribution links before the final deadline. | K2 |
| Branch or merge problems delay integration | Medium | Medium | Use separate branches and reviewed PRs; integrate regularly and retain working history. | K5 |
| Reports expose personal information | Medium | High | Minimize collected data, avoid publishing sensitive identifiers or contact details, and use safe demonstration data. | K3, with K4 |
| False reports or ownership claims reduce trust | Medium | Medium | Agree a proportionate claim/verification process; do not promise automatic ownership verification. | K1, with K3 |

Likelihood and impact are initial qualitative team estimates and should be reviewed during planning.

---

## 6. Open-Source License Selection

The project proposes the MIT License, consistent with the license file currently shown in the repository. Its permissive terms support reuse, modification and distribution while requiring preservation of the copyright and permission notice. This suits a small educational software project intended for learning and collaboration. The team should retain the LICENSE file and check the licenses of any third-party dependencies or assets. The code license does not grant permission to publish users' personal information or third-party content.

---

## 7. Items to Confirm Before Integration

- Confirm the initial release features with K1 and K4.
- Confirm that the system assumptions agree with K3's API draft.
- Confirm MIT as the team's agreed license.
- Agree an internal document collection deadline before the course submission deadline.