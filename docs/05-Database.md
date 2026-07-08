# Database Design

## Database Technology

The MVP will use:

- PostgreSQL
- pgvector
- Entity Framework Core Code First

## Design Approach

The database will be derived from the Domain Model.

The goal is to keep the database simple, relational, and ready for future AI Knowledge Base features.

## Main Tables

- Organizations
- Users
- Invitations
- Projects
- ProjectMembers
- Stories
- StoryComments
- StoryAttachments
- KnowledgeItems
- AIAnalyses
- TestCases
- TestSteps
- JiraIntegrations
- JiraLinks
- AutomationGenerations

---
------
---
# Organizations

Stores customer companies using the platform.

## Columns

- Id
- Name
- Slug
- Status
- CreatedAt
- UpdatedAt

## Notes

- Each Organization is a tenant.
- Organization data must be isolated from other Organizations.
- Slug should be unique.

---
# Users

Stores platform users.

## Columns

- Id
- OrganizationId
- FullName
- Email
- PasswordHash
- Status
- CreatedAt
- UpdatedAt

## Relationships

- User belongs to one Organization.
- Organization has many Users.

## Notes

- Email should be unique within the platform in the MVP.
- Users can access Projects only through ProjectMembers.
