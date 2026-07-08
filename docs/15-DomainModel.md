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

Represents an invitation sent to a person to join an Organization and optionally a Project.

An Invitation controls how new users join the platform and ensures secure onboarding.

---

## Responsibilities

- Invite users to an Organization.
- Invite users to a specific Project.
- Assign an initial Project Role.
- Track invitation status.
- Allow invitation resending.

---

## Relationships

- An Invitation belongs to one Organization.
- An Invitation may belong to one Project.
- An Invitation may assign one Project Role.
- An Invitation may create one User after acceptance.

---

## Business Rules

- Invitations are sent by Organization Administrators or Owners.
- Every invitation contains a secure unique token.
- Invitations have an expiration date.
- Expired invitations cannot be accepted.
- A user cannot accept the same invitation twice.
- If the invited email already exists, the user joins the Organization or Project after accepting the invitation.

---

## Lifecycle

Created

↓

Sent

↓

Accepted

↓

Expired

or

↓

Cancelled
