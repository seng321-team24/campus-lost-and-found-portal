# Project Proposal — Campus Lost and Found Portal
 
**Project:** Campus Lost and Found Portal  
**Team:** Team 24  
**Course:** SENG 321 — Software Engineering  
**Status:** Sprint 1 Project Proposal
 
---
 
## 1. Problem Statement and Empirical Evidence
 
Losing personal belongings on a university campus can cause stress, inconvenience, and wasted time for students and university staff. Currently, lost-and-found processes often rely on informal communication channels, university security offices, or individual department staff. These methods may make it difficult to find information about missing items efficiently.
 
To investigate this problem, our team conducted a campus survey with 15 respondents. The survey revealed the following findings:
 
- **53.3%** of respondents reported having lost or found an item on campus.
- **46.7%** stated that finding information about lost items at the university is difficult or very difficult.
- **66.7%** reported that they would normally contact university security or staff, while **20%** would ask classmates or friends.
- **66.7%** expressed willingness to use a dedicated university lost-and-found website, consisting of **40%** who answered "Definitely yes" and **26.7%** who answered "Probably yes".
 
These findings suggest that a centralized lost-and-found portal could improve access to information and provide an alternative to existing informal reporting methods.
 
The survey provides preliminary feedback from a small group of 15 respondents and does not necessarily represent the entire university population.
 
---
 
## 2. Key Stakeholders
 
The primary stakeholders of the Campus Lost and Found Portal are:
 
- **Students:** Primary users who report lost belongings, publish found items, search listings, and submit ownership claim requests.
- **University Staff:** Staff members who may find lost items or assist students with lost-and-found inquiries.
- **Moderators / Administrators:** Authorized users responsible for reviewing listings, managing reports, and supervising claim requests where applicable.
- **University Administration:** Stakeholders who may benefit from improved organization, reduced administrative workload, and better student services.
 
---
 
## 3. Proposed Solution
 
The proposed solution is a centralized web-based Lost and Found Portal designed for university students and staff.
 
The platform will allow users to report lost or found items and search existing listings.
 
The main proposed functionalities include:
 
1. **User Registration and Login:** Users can create accounts and securely access the platform.
2. **Lost and Found Item Reporting:** Users can create listings containing an item name, description, category, approximate location, and date.
3. **Search and Filtering:** Users can search listings by item name, category, and lost/found status.
4. **Item Details:** Users can view available information about reported items.
5. **Claim Requests:** Users can submit requests to claim items they believe belong to them.
6. **Claim Management:** Authorized users can review and accept or reject claim requests.
7. **Basic Administration:** Authorized administrators can manage inappropriate or misleading listings.
 
The exact implementation scope of these functionalities will be determined through the product backlog and development priorities.
 
---
 
## 4. Expected Outcomes and Deliverables
 
The Campus Lost and Found Portal aims to deliver the following outcomes:
 
- **Centralized Web Platform:** A user-friendly interface for reporting, searching, and filtering lost and found items.
- **Improved Information Accessibility:** A structured system that reduces dependence on scattered communication channels.
- **Claim and Approval Workflow:** A proposed mechanism for submitting and reviewing ownership claims.
- **API Documentation:** An OpenAPI specification describing RESTful endpoints, request and response schemas, and error responses.
- **Software Engineering Documentation:** Project proposal, system vision, project backlog, team responsibilities, and RACI matrix.
- **Verifiable Development Process:** A Git-Flow-based GitHub workflow with feature branches, issues, pull requests, and code reviews.
 
The expected result is a working software prototype developed incrementally throughout the course sprints.
 
---
 
## 5. Feasibility Assessment
 
The project is considered feasible within the course framework, provided that development is organized into manageable tasks and completed incrementally.
 
### 5.1 Technical Feasibility
 
The system can be developed using commonly available web development technologies and a database for storing user accounts, item listings, and claim information.
 
The team can use GitHub for version control, task tracking, and collaboration.
 
The project does not initially require integration with university information systems.
 
### 5.2 Operational Feasibility
 
The proposed platform is intended to be accessible through a web browser.
 
Students and university staff can use the system to publish and search for lost-and-found reports.
 
The interface should be simple and understandable for users with basic computer skills.
 
### 5.3 Schedule Feasibility
 
The project will be developed across four sprints.
 
To reduce scheduling risks, the team will prioritize essential functionalities before implementing more complex features.
 
Tasks will be distributed among team members according to their responsibilities.
 
### 5.4 Resource Feasibility
 
The development process can use commonly available development tools, GitHub, and suitable open-source technologies.
 
The project is intended as an educational prototype and is not expected to require significant infrastructure investment during its initial development.
 
---
 
## 6. Initial Project Scope
 
### 6.1 In-Scope Features
 
The planned system scope includes:
 
- User registration and login
- User profile viewing
- Creating lost-item reports
- Creating found-item reports
- Adding item descriptions, categories, locations, and dates
- Viewing item listings and details
- Searching for items by name
- Filtering items by category and status
- Submitting ownership claim requests
- Reviewing claim requests
- Basic listing and claim management
 
The team will prioritize these features through the product backlog and implement them incrementally.
 
### 6.2 Out-of-Scope Features
 
The initial system will not include:
 
- Direct integration with university student information systems
- Payment processing or financial transactions
- Automatic ownership verification using artificial intelligence
- Integration with external police or governmental databases
- A dedicated Android or iOS mobile application
- Advanced real-time location tracking
 
These features may be considered for future development if appropriate.
 
---
 
## 7. Assumptions
 
1. Target users are campus students and staff who can access a web browser and the internet.
2. Users can provide basic details about a lost or found item, such as its description, approximate location, and date.
3. The initial development prioritizes creating and browsing lost-and-found reports. Claim management, moderation, and authentication requirements will be finalized through the product backlog and reflected consistently in the OpenAPI specification.
4. The team has access to GitHub and the development tools needed for implementation.
5. The initial release does not depend on university information-system integration.
6. Testing and demonstrations can use fictional items and non-sensitive sample data.
 
These assumptions represent initial planning decisions and may be updated during development.
 
---
 
## 8. Initial Risks and Mitigation
 
| Risk | Likelihood | Impact | Mitigation | Follow-up Owner |
|---|---|---|---|---|
| Scope grows beyond the available sprint time | Medium | High | Prioritize essential requirements and review additional feature requests before accepting them. | İrem |
| API documentation and system requirements become inconsistent | Medium | High | Review the OpenAPI specification against the product backlog and system vision. | Aslıhan, Ceren |
| Team members submit work late or without evidence | Medium | High | Establish internal checkpoints, track GitHub issues, and collect contribution evidence. | Tuğba |
| Branch conflicts or merge problems delay integration | Medium | Medium | Use feature branches, reviewed pull requests, and a consistent Git-Flow process. | Berra |
| Item listings expose personal information | Medium | High | Minimize personal data collection and avoid displaying sensitive information publicly. | Aslıhan, Ceren |
| False reports or ownership claims reduce system reliability | Medium | Medium | Implement appropriate claim review procedures and avoid assuming automatic ownership verification. | İrem, Aslıhan |
 
The likelihood and impact ratings are preliminary qualitative estimates and may be revised during subsequent sprints.
 
---
 
## 9. Open-Source License Selection
 
The team proposes using the **MIT License** for the Campus Lost and Found Portal.
 
The MIT License is a permissive open-source license that allows software to be used, modified, and distributed while requiring preservation of the copyright and permission notice.
 
This license is suitable for an educational software engineering project because it supports collaboration, learning, and future improvements.
 
The team will retain the LICENSE file in the GitHub repository and review the licenses of any third-party dependencies or assets used during development.
 
The software license does not authorize the publication of private user information or third-party copyrighted content without permission.
 
---
 
## 10. Conclusion
 
The Campus Lost and Found Portal aims to provide a centralized and accessible solution for managing lost-and-found information within a university environment.
 
The preliminary survey results indicate potential demand for a dedicated web-based reporting and search platform.
 
By implementing essential features through an incremental four-sprint development process, the team aims to deliver a functional prototype supported by clear requirements, API documentation, and collaborative GitHub practices.
 
The project will focus on usability, organized item reporting, efficient searching, and responsible handling of user information.
 
