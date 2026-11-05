# CAMPUS LOST AND FOUND PORTAL
## Initial Product Backlog
**Sprint 1 Deliverable | 

---

### Project Information

| Item | Details |
| :--- | :--- |
| **Project name** | Campus Lost and Found Portal |
| **Team** | Team 24 |

### Team Role Assignment

| Role | Team member | Main responsibility |
| :--- | :--- | :--- |
| **Product Owner** | İrem Köse | Product backlog, requirements prioritization, and stakeholder value |
| **Scrum Master** | Tuğba Kezban Çay | Agile process coordination and team facilitation |
| **Software Architect** | Aslıhan Kaçmaz | System architecture and OpenAPI draft |
| **QA Engineer** | Ceren Torun | Quality assurance, validation, and testing support |
| **DevOps Engineer** | Berra Bayındır | GitHub workflow and development infrastructure |

---

## 1. Product Backlog Overview
The purpose of the initial product backlog is to identify, organize, and prioritize the main features of the Campus Lost and Found Portal. The backlog describes system requirements from the users' perspective and provides a foundation for future development sprints.

The system aims to help university students and staff report lost or found belongings, search for item listings, and view relevant information about missing or found items.

## 2. Product Goal and Initial Sprint Focus
* **Product goal:** Provide a simple and accessible campus platform where students and staff can publish, browse, and search lost-and-found item listings.
* **Suggested initial development focus:** User access, creating lost and found listings, and browsing available listings. The final Sprint 1 development commitment should be confirmed by the whole team during sprint planning.

---

## 3. Epics and Features

| Epic | Features |
| :--- | :--- |
| **User Management** | User registration and login |
| **Lost Item Management** | Create and manage lost item listings |
| **Found Item Management** | Create and manage found item listings |
| **Search and Discovery** | Search and filter item listings |
| **Item Details** | View item descriptions and photos |
| **Listing Management** | View and manage personal listings |

---

## 4. Initial User Stories and Estimates
User stories are written from the perspective of a person who will use the system. Priority and effort values are preliminary planning estimates. Story points represent relative effort and uncertainty, not working hours.

| ID | Epic | User Story | Priority | Effort (points) |
| :--- | :--- | :--- | :--- | :--- |
| **US-01** | User Management | As a user, I want to register so that I can access the platform. | High | 5 |
| **US-02** | User Management | As a registered user, I want to log in so that I can use my account. | High | 3 |
| **US-03** | Lost Item Management | As a user, I want to create a lost item listing so that I can report my missing belongings. | High | 5 |
| **US-04** | Found Item Management | As a user, I want to create a found item listing so that I can inform others about items I have found. | High | 5 |
| **US-05** | Search and Discovery | As a user, I want to view all item listings so that I can find relevant items. | High | 3 |
| **US-06** | Search and Discovery | As a user, I want to search for items by keyword so that I can find specific belongings. | High | 5 |
| **US-07** | Search and Discovery | As a user, I want to filter items by category so that I can narrow down the results. | Medium | 3 |
| **US-08** | Item Details | As a user, I want to view item details so that I can learn more about an item. | High | 2 |
| **US-09** | Item Details | As a user, I want to upload an item photo so that I can help identify the item. | Medium | 3 |
| **US-10** | Listing Management | As a user, I want to view my own listings so that I can manage the items I have reported. | Medium | 3 |

---

## 5. Acceptance Criteria

| ID | Acceptance Criteria |
| :--- | :--- |
| **US-01** | The user can enter the required registration information. The system validates the information before creating an account. |
| **US-02** | Registered users can log in with valid credentials. Invalid credentials produce an appropriate error message. |
| **US-03** | Users can enter the item name, description, category, and lost location. Required fields are validated. |
| **US-04** | Users can enter the item name, description, category, and found location. Required fields are validated. |
| **US-05** | The system displays available lost and found listings. |
| **US-06** | Users can search listings using keywords and see relevant results. |
| **US-07** | Users can filter listings according to item categories. |
| **US-08** | Selecting a listing displays its available details. |
| **US-09** | Users can upload an image in an accepted format and within the configured file-size limit. |
| **US-10** | Logged-in users can view the listings they have created. |

---

## 6. Dependencies

| User Story | Dependency |
| :--- | :--- |
| **US-02** | **US-01** - An account must exist before a user can log in. |
| **US-03** | **US-02** - The user must be logged in to create a listing. |
| **US-04** | **US-02** - The user must be logged in to create a listing. |
| **US-06** | **US-05** - Search operates on available listings. |
| **US-07** | **US-05** - Filters are applied to available listings. |
| **US-08** | **US-05** - The user selects an existing listing. |
| **US-09** | **US-03** or **US-04** - A photo is attached to a listing. |
| **US-10** | **US-02** and at least one listing created by the user. |

---

## 7. Priority and Effort Explanation
* **High:** Essential features required for the main functionality of the system.
* **Medium:** Useful features that improve the user experience but are not essential for the first basic version.
* **Low:** Additional features that may be considered in future development.

**Effort** uses relative story points (for example, 1, 2, 3, 5, and 8). A higher value indicates greater expected effort or uncertainty. The development team should review and agree on these estimates before committing to sprint work.

---

## 8. Backlog Ownership and Maintenance
The Product Owner, is responsible for maintaining the product backlog, clarifying requirements, and ordering backlog items according to their value and priority. Implementation assignees for individual stories will be confirmed by the development team during sprint planning. The backlog may be updated based on stakeholder feedback, team discussions, and project progress.

---

## 9. Definition of Done
A backlog item can be considered complete when:
* The agreed acceptance criteria are satisfied.
* The implemented functionality has been reviewed.
* Relevant tests pass.
* Identified blocking defects are resolved.
* The change is integrated into the team repository according to the agreed Git workflow.
* Documentation is updated when the change affects documented behavior.

---

## 10. Assumptions and Items for Team Validation
* The primary users are university students and staff.
* The first version focuses on publishing and finding lost-and-found listings; physical item handover is outside the platform's direct control.
* The team should confirm whether registration will use ordinary account credentials or university accounts.
* The team should agree on the allowed item categories, required listing fields, image formats, and maximum image size.
* The listed priorities, story points, and dependencies are initial proposals and should be validated during backlog refinement.
 
> *Campus Lost and Found Portal | Team 24*
