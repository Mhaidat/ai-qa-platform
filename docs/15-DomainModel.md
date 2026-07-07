# Domain Model

## Core Entities

- Organization
- User
- Invitation
- Project
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

## Optional / Future Entities

- Subscription
- Epic
- Sprint
- Release
- AI Settings
- Audit Log

---

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
