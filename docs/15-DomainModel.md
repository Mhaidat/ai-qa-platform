# Domain Model

## Core Entities

- Organization
- User
- Invitation
- Project
- Project Member
- Story
- Story Comment
- Story Attachment
- Knowledge Item
- AI Analysis
- Test Case
- Test Step
- Jira Integration
- Jira Link
- Automation Generation

## Value Objects / Enums

- Project Role

## Optional / Future Entities

- Subscription
- Epic
- Sprint
- Release
- AI Settings
- Audit Log
- Custom Project Role
- Permission
- Knowledge Source

------

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

---
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

------

# Organization

## Purpose

Represents a customer company using the platform.

## Responsibilities

- Own users.
- Own projects.
- Own organization settings.
- Control access to company data.

## Relationships

- Organization has many Users.
- Organization has many Projects.
- Organization has many Invitations.

## Business Rules

- The first user who registers creates an Organization.
- The first user becomes the Organization Owner.
- Organization data must be isolated from other organizations.
- Users from one Organization cannot access another Organization's data.

## Lifecycle

Created

↓

Active

↓

Suspended

↓

Deleted


---

# User

## Purpose

Represents a member of an Organization who performs one or more responsibilities within the AI QA Platform.

A User collaborates with other team members throughout the software quality assurance lifecycle.

---

## Responsibilities

- Authenticate into the platform.
- Access assigned Projects.
- Perform actions based on assigned roles.
- Review and approve work when permitted.
- Collaborate with other team members.

---

## Relationships

- A User belongs to one Organization.
- A User can participate in multiple Projects.
- A User may create Stories.
- A User may create or edit Test Cases.
- A User may perform AI Analysis.
- A User may generate Automation.
- A User may receive Invitations.

---

## Business Rules

- Every User belongs to exactly one Organization.
- Every User must have at least one Role.
- Users can only access data within their Organization.
- Project access is granted explicitly.
- A User may have different roles in different Projects.
- Every business action must be auditable.

---

## Lifecycle

Invited

↓

Registered

↓

Active

↓

Suspended

↓

Deactivated

---

# Project Member

## Purpose

Represents a User's membership inside a specific Project.

A Project Member defines what a User is allowed to do within that Project.

---

## Responsibilities

- Connect a User to a Project.
- Define the User's role inside the Project.
- Control project-level access.
- Allow the same User to have different roles in different Projects.

---

## Relationships

- A Project Member belongs to one Project.
- A Project Member references one User.
- A Project Member has one Project Role.
- A Project has many Project Members.
- A User can be a member of many Projects.
---

## Supported Project Roles

- Business Analyst
- Manual QA Engineer
- Automation QA Engineer
- QA Lead
- Project Admin

---

## Business Rules

- A User cannot access a Project unless they are a Project Member.
- A User may have different roles in different Projects.
- Each Project must have at least one Project Admin or QA Lead.
- Organization Owner and Administrator can manage Project Members.
- Project roles control what actions the User can perform inside the Project.

---

## Lifecycle

Invited to Project

↓

Active

↓

Role Changed

↓

Removed


---

# Invitation

## Purpose

Represents an invitation sent to a person to join an Organization.

An Invitation controls how new users join the platform and ensures secure onboarding.

## Responsibilities

- Invite users to an Organization.
- Track invitation status.
- Allow invitation resending.
- Allow invited users to complete registration.

## Relationships

- An Invitation belongs to one Organization.
- An Invitation is sent to one email address.
- An Invitation may create one User after acceptance.

## Business Rules

- Invitations are sent by Organization Owners or Administrators.
- Invitations are Organization-level only in the MVP.
- Users are assigned to Projects after joining the Organization.
- Every invitation contains a secure unique token.
- Invitations have an expiration date.
- Expired invitations cannot be accepted.
- A user cannot accept the same invitation twice.
- If the invited email already exists, the user joins the Organization after accepting the invitation.

## Lifecycle

Created

↓

Sent

↓

Accepted

or

↓

Expired

or

↓

Cancelled

---

# Project

## Purpose

Represents a complete software product or platform owned by an Organization.

A Project is the main workspace where users manage Stories, project knowledge, test cases, Jira integration, and automation generation.

## Responsibilities

- Group all work related to one software product.
- Own Stories and requirements.
- Own Project Members.
- Own Jira Integration.
- Own Knowledge Items.
- Own Test Cases.
- Own Automation Generations.

## Relationships

- A Project belongs to one Organization.
- A Project has many Project Members.
- A Project has many Stories.
- A Project has many Knowledge Items.
- A Project has one or more Jira Integrations.
- A Project has many Test Cases through Stories.
- A Project has many Automation Generations through approved Test Cases.

## Business Rules

- A Project must belong to one Organization.
- A Project represents a software product, not a single business module.
- Functional modules are organized inside the Project.
- A User cannot access a Project unless they are a Project Member.
- Every Project must have at least one Project Admin or QA Lead.
- Project data must be isolated from other Projects.

## Lifecycle

Created

↓

Active

↓

Archived

↓

Deleted


---

# Knowledge Item

## Purpose

Represents a piece of project knowledge extracted from Stories or created during AI analysis.

A Knowledge Item is part of the Project Knowledge Base.

## Responsibilities

- Store extracted business knowledge.
- Support requirement analysis.
- Support test case generation.
- Help detect gaps, ambiguities, and conflicts.
- Provide project context for AI operations.

## Relationships

- A Knowledge Item belongs to one Project.
- In the MVP, a Knowledge Item has one Origin Story.
- A Story may produce many Knowledge Items.
- Future versions may allow one Knowledge Item to be linked to multiple Stories through Knowledge Sources.

## Knowledge Item Types

- Business Rule
- Gap
- Ambiguity
- Suggested Acceptance Criteria
- Conflict
- Actor
- Entity
- Glossary Term

## Business Rules

- Knowledge Items are visible to users.
- AI can create Knowledge Items.
- Users can review Knowledge Items.
- AI-generated Knowledge Items are not approved automatically.
- Each Knowledge Item must be traceable to its Origin Story in the MVP.

## Lifecycle

Extracted

↓

Reviewed

↓

Approved

or

↓

Rejected
