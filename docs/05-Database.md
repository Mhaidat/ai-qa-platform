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

---

# Invitations

Stores invitations sent to users to join an Organization.

## Columns

- Id
- OrganizationId
- Email
- TokenHash
- Status
- ExpiresAt
- AcceptedAt
- InvitedByUserId
- CreatedAt
- UpdatedAt

## Relationships

- Invitation belongs to one Organization.
- Invitation is created by one User.
- Invitation may result in one User after acceptance.

## Notes

- Invitations are Organization-level only in the MVP.
- The raw invitation token must not be stored; only its secure hash is stored.
- Expired, cancelled, or accepted invitations cannot be reused.
- The combination of OrganizationId and Email should not have more than one active invitation.

## Notes

- Email should be unique within the platform in the MVP.
- Users can access Projects only through ProjectMembers.

---

# Projects

Stores software products or platforms managed by an Organization.

## Columns

- Id
- OrganizationId
- Name
- Slug
- Description
- Status
- CreatedByUserId
- CreatedAt
- UpdatedAt
- ArchivedAt

## Relationships

- Project belongs to one Organization.
- Project is created by one User.
- Project has many Project Members.
- Project has many Stories.
- Project has many Knowledge Items.
- Project has one or more Jira Integrations.

## Notes

- A Project represents a complete software product, not a single business module.
- Project Slug must be unique within the Organization.
- Project data must be isolated from other Organizations and Projects.
- Archived Projects remain readable but cannot accept new work.

---

# ProjectMembers

Stores the membership of Users within Projects.

This table defines project-level access and responsibilities.

## Columns

- Id
- ProjectId
- UserId
- Role
- Status
- JoinedAt
- RemovedAt
- CreatedAt
- UpdatedAt

## Relationships

- Project Member belongs to one Project.
- Project Member references one User.
- Project has many Project Members.
- User can belong to many Projects.

## Supported Roles (MVP)

- Project Admin
- Business Analyst
- Manual QA Engineer
- Automation QA Engineer
- QA Lead

## Notes

- Role will be implemented as an Enum in the MVP.
- The same User may have different Roles in different Projects.
- Users cannot access a Project unless they are Project Members.
- Every Project should have at least one Project Admin.

---

# Stories

Stores software requirements and user stories.

## Columns

* Id
* ProjectId
* Source
* ExternalKey
* Title
* Description
* AcceptanceCriteria
* Priority
* Status
* ExternalStatus
* EpicName
* SprintName
* ReleaseName
* Labels
* ExternalCreatedAt
* ExternalUpdatedAt
* LastSyncedAt
* CreatedByUserId
* ApprovedByUserId
* ApprovedAt
* CreatedAt
* UpdatedAt
* DeletedAt

## Relationships

* Story belongs to one Project.
* Story may be created by one User.
* Story may be approved by one User.
* Story has many Story Comments.
* Story has many Story Attachments.
* Story has many AI Analyses.
* Story may be the Origin Story of many Knowledge Items.
* Story has many Test Cases.

## Supported Sources

* Manual
* Jira

## Supported Statuses

* Draft
* ReadyForAnalysis
* UnderBusinessReview
* Approved
* Rejected
* Archived

## Notes

* Stories imported from Jira are read-only inside the platform.
* Manually created Stories are editable.
* `ExternalKey` stores the Jira issue key, such as `PROJ-125`.
* `ExternalKey` is nullable for manually created Stories.
* `ExternalStatus` stores the current Jira workflow status without controlling the platform workflow.
* Epic, Sprint, and Release fields are optional.
* The MVP stores only the latest synchronized Story content.
* Soft deletion should be used through `DeletedAt`.
* A Jira Story must be unique within its Jira Integration.

---

# StoryComments

Stores comments associated with a Story.

Comments may be imported from Jira or created inside the platform.

## Columns

* Id
* StoryId
* Source
* ExternalId
* Content
* AuthorName
* CreatedByUserId
* ExternalCreatedAt
* ExternalUpdatedAt
* CreatedAt
* UpdatedAt
* DeletedAt

## Relationships

* Story Comment belongs to one Story.
* A platform-created Comment may reference one User.

## Supported Sources

* Platform
* Jira

## Notes

* Jira comments are read-only inside the platform.
* Platform comments can be edited or deleted by authorized users.
* `ExternalId` is nullable for platform-created comments.
* Imported Jira comments should not be duplicated during synchronization.
* Comments can be included as contextual input during Knowledge Analysis.

---

# StoryAttachments

Stores metadata for files associated with a Story.

## Columns

* Id
* StoryId
* Source
* ExternalId
* FileName
* ContentType
* FileSize
* StoragePath
* ExternalUrl
* UploadedByUserId
* ExternalCreatedAt
* CreatedAt
* DeletedAt

## Relationships

* Story Attachment belongs to one Story.
* A platform-uploaded Attachment may reference one User.

## Supported Sources

* Platform
* Jira

## Notes

* The database stores file metadata only.
* File content should be stored in object storage or local file storage during the MVP.
* Jira attachments may initially store only their external URL and metadata.
* Access to attachments must respect Organization and Project isolation.
* Unsupported or unsafe file types must be rejected.
* Attachment size limits must be configurable.

---

# AIAnalyses

Stores every AI analysis execution performed for a Story.

Previous analysis results are preserved and are not overwritten.

## Columns

* Id
* StoryId
* RequestedByUserId
* Status
* ModelProvider
* ModelName
* PromptVersion
* InputTokenCount
* OutputTokenCount
* EstimatedCost
* ErrorMessage
* StartedAt
* CompletedAt
* CreatedAt

## Relationships

* AI Analysis belongs to one Story.
* AI Analysis is requested by one User.
* AI Analysis may create many Knowledge Items.

## Supported Statuses

* Pending
* Processing
* Completed
* Failed
* Cancelled

## Notes

* Every execution creates a new AI Analysis record.
* Failed executions must remain available for troubleshooting.
* The complete prompt does not need to be stored if it contains sensitive project information.
* Prompt version, model information, token usage, and cost should be recorded.
* AI Analysis does not approve or modify Stories automatically.
* Generated results must be validated before being stored as Knowledge Items.

---

# KnowledgeItems

Stores individual pieces of project knowledge.

The collection of Knowledge Items represents the Project Knowledge Base.

## Columns

* Id
* ProjectId
* OriginStoryId
* AIAnalysisId
* Type
* Title
* Content
* Status
* ConfidenceScore
* CreatedBy
* ReviewedByUserId
* ReviewedAt
* CreatedAt
* UpdatedAt
* DeletedAt
* Embedding

## Relationships

* Knowledge Item belongs to one Project.
* Knowledge Item has one Origin Story in the MVP.
* Knowledge Item may originate from one AI Analysis.
* Knowledge Item may be reviewed by one User.

## Supported Types

* BusinessRule
* Gap
* Ambiguity
* SuggestedAcceptanceCriteria
* Conflict
* Actor
* Entity
* GlossaryTerm

## Supported Statuses

* Extracted
* UnderReview
* Approved
* Rejected
* Superseded

## Supported Creators

* AI
* User

## Notes

* Knowledge Items belong to the Project, not directly to the Story.
* `OriginStoryId` preserves traceability to the Story that produced the knowledge.
* AI-generated Knowledge Items are never approved automatically.
* `ConfidenceScore` is informational and must not replace human review.
* `Embedding` uses pgvector and supports semantic search.
* Rejected Knowledge Items should remain stored for audit and learning purposes.
* Duplicate or similar Knowledge Items may be detected through semantic search.

---

# Story Aggregate Indexes

## Stories

* Index on `ProjectId`
* Index on `ProjectId, Status`
* Unique filtered index on `ProjectId, ExternalKey` when `ExternalKey` is not null
* Index on `LastSyncedAt`
* Index on `DeletedAt`

## StoryComments

* Index on `StoryId`
* Unique filtered index on `StoryId, ExternalId` when `ExternalId` is not null

## StoryAttachments

* Index on `StoryId`
* Unique filtered index on `StoryId, ExternalId` when `ExternalId` is not null

## AIAnalyses

* Index on `StoryId`
* Index on `StoryId, CreatedAt`
* Index on `Status`

## KnowledgeItems

* Index on `ProjectId`
* Index on `OriginStoryId`
* Index on `AIAnalysisId`
* Index on `ProjectId, Type`
* Index on `ProjectId, Status`
* Vector index on `Embedding`

