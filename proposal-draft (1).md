# Project Proposal — K2 Draft Sections

Project: Campus Lost and Found Portal
Status: Proposed text for review with K1, K3 and K4. Integrate these sections into the team's main proposal; this file is not a complete proposal.

## Assumptions

1. Target users are campus students and staff who can access a web browser and the internet.
2. Users can provide basic details about a lost or found item, such as its description, approximate location and date.
3. The initial scope prioritizes creating and browsing lost/found reports. Any claiming, moderation or authentication workflow must be agreed in the backlog and reflected consistently in the OpenAPI draft.
4. The team has access to GitHub and the development tools needed for the agreed implementation.
5. The initial release does not rely on university information-system integration unless access and approval are confirmed.
6. Test and demonstration records can use fictional items and non-sensitive sample data.

These are planning assumptions, not confirmed survey findings or implemented features.

## Initial Risks and Mitigation

| Risk | Likelihood | Impact | Mitigation | Follow-up owner |
|---|---|---|---|---|
| Scope grows beyond the available sprint time | Medium | High | Prioritize a small initial backlog and review new requirements before accepting them. | K1 |
| API and documented requirements disagree | Medium | High | Review the OpenAPI draft against the backlog and vision before implementation. | K3, with K4 |
| Members submit work late or without evidence | Medium | High | Agree internal checkpoints, track issues and collect contribution links before the final deadline. | K2 |
| Branch or merge problems delay integration | Medium | Medium | Use separate branches and reviewed PRs; integrate regularly and retain working history. | K5 |
| Reports expose personal information | Medium | High | Minimize collected data, avoid publishing sensitive identifiers or contact details, and use safe demonstration data. | K3, with K4 |
| False reports or ownership claims reduce trust | Medium | Medium | Agree a proportionate claim/verification process; do not promise automatic ownership verification. | K1, with K3 |

Likelihood and impact are initial qualitative team estimates and should be reviewed during planning.

## Open-Source License Selection

The project proposes the MIT License, consistent with the license file currently shown in the repository. Its permissive terms support reuse, modification and distribution while requiring preservation of the copyright and permission notice. This suits a small educational software project intended for learning and collaboration. The team should retain the LICENSE file and check the licenses of any third-party dependencies or assets. The code license does not grant permission to publish users' personal information or third-party content.

## Items to Confirm Before Integration

- Confirm the initial release features with K1 and K4.
- Confirm that the system assumptions agree with K3's API draft.
- Confirm MIT as the team's agreed license.
- Agree an internal document collection deadline before the course submission deadline.
