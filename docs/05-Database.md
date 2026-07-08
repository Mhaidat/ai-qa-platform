---

# Domain Diagram

## MVP Domain Structure

Organization
│
├── Users
│
├── Invitations
│
└── Projects
    │
    ├── Project Members
    │
    ├── Jira Integrations
    │
    ├── Stories
    │   │
    │   ├── Story Comments
    │   ├── Story Attachments
    │   ├── AI Analyses
    │   └── Test Cases
    │       │
    │       ├── Test Steps
    │       ├── Jira Links
    │       └── Automation Generations
    │
    └── Knowledge Items
        │
        └── Origin Story


## Relationship Notes

- Organization is the top-level tenant.
- Each Organization owns Users, Invitations, and Projects.
- A Project represents a complete software product.
- Users access Projects through Project Members.
- Project Member defines the user's role inside a Project.
- Stories belong to a Project.
- Stories may be imported from Jira or created manually.
- Jira-imported Stories are read-only inside the platform.
- Knowledge Items belong to the Project.
- In the MVP, each Knowledge Item has one Origin Story.
- Test Cases belong to one Story.
- Test Steps belong to one Test Case.
- Jira Links connect platform artifacts with Jira.
- Automation Generations are created from approved Test Cases.
