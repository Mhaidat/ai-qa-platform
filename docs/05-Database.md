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

---

# TestCases

Stores manual test cases generated by AI or created manually by QA Engineers.

## Columns

* Id
* StoryId
* Source
* Title
* Description
* Preconditions
* TestData
* ExpectedResult
* Type
* Priority
* Status
* CreatedByUserId
* ApprovedByUserId
* ApprovedAt
* CreatedAt
* UpdatedAt
* DeletedAt

## Relationships

* Test Case belongs to exactly one Story.
* Test Case may be created by one User.
* Test Case may be approved by one User.
* Test Case has many Test Steps.
* Test Case may have one or more Jira Links.
* Test Case may have many Automation Generations.

## Supported Sources

* AI
* Manual
* Imported

## Supported Types

* Positive
* Negative
* Boundary
* Validation
* BusinessRule
* ErrorHandling
* Security
* Accessibility
* Performance
* Other

## Supported Priorities

* Low
* Medium
* High
* Critical

## Supported Statuses

* Draft
* Generated
* UnderReview
* Approved
* Rejected
* LinkedToJira
* ReadyForAutomation
* Archived

## Notes

* Every Test Case belongs to exactly one Story.
* AI-generated Test Cases are never approved automatically.
* Manual QA Engineers can create Test Cases without using AI.
* Users can edit AI-generated Test Cases before approval.
* Approved Test Cases become visible inside the Automation Workspace.
* Soft deletion should be implemented through `DeletedAt`.
* Test Case source must remain unchanged after creation.
* Updating a Test Case does not change whether it was originally created by AI or manually.
* A Test Case cannot be used for automation before approval.
* A rejected Test Case remains stored for traceability.

---

# TestSteps

Stores the ordered execution steps of a Test Case.

## Columns

* Id
* TestCaseId
* StepNumber
* Action
* TestData
* ExpectedResult
* CreatedAt
* UpdatedAt

## Relationships

* Test Step belongs to one Test Case.
* Test Case has many Test Steps.

## Notes

* `StepNumber` defines the execution order.
* Step numbers must be unique within the same Test Case.
* Test Steps must be returned in ascending `StepNumber` order.
* Deleting a Test Case deletes its Test Steps.
* A Test Case may contain no steps while it is still in Draft status.
* An approved Test Case must contain at least one Test Step unless it is explicitly defined as a scenario-only Test Case.

---

# JiraLinks

Stores links between artifacts inside the AI QA Platform and Jira issues.

## Columns

* Id
* JiraIntegrationId
* EntityType
* EntityId
* JiraIssueKey
* JiraIssueId
* JiraIssueType
* JiraUrl
* SyncStatus
* LastSyncedAt
* CreatedByUserId
* CreatedAt
* UpdatedAt
* DeletedAt

## Relationships

* Jira Link belongs to one Jira Integration.
* Jira Link references one platform artifact.
* Jira Link may reference a Story or Test Case in the MVP.
* Jira Link may be created by one User.

## Supported Entity Types

* Story
* TestCase

## Supported Sync Statuses

* Linked
* Pending
* Synced
* SyncFailed
* Unlinked

## Notes

* Jira synchronization is always initiated manually by the user.
* A Jira Link does not own the linked Story or Test Case.
* Imported Jira Stories retain Jira as their source of truth.
* A Test Case may be linked to an existing Jira issue or used to create a new Jira issue.
* The combination of `JiraIntegrationId`, `EntityType`, and `EntityId` should be unique for active links.
* Jira issue identifiers must not be used as internal primary keys.
* Failed synchronization attempts must not remove an existing valid link.
* Soft deletion should be implemented through `DeletedAt`.

---

# AutomationGenerations

Stores every automation code generation request created from an approved Test Case.

Each execution creates a separate record to preserve generation history.

## Columns

* Id
* TestCaseId
* RequestedByUserId
* Status
* Framework
* Language
* ModelProvider
* ModelName
* PromptVersion
* GeneratedCode
* PackageStoragePath
* InputTokenCount
* OutputTokenCount
* EstimatedCost
* ErrorMessage
* StartedAt
* CompletedAt
* DownloadedAt
* CreatedAt
* UpdatedAt

## Relationships

* Automation Generation belongs to one Test Case.
* Automation Generation is requested by one User.
* A Test Case may have many Automation Generations.

## Supported Frameworks

* Playwright

## Supported Languages

* TypeScript

## Supported Statuses

* Pending
* Processing
* Completed
* Failed
* Cancelled

## Notes

* Automation can only be generated from an approved Test Case.
* Every generation attempt creates a new record.
* Previous generated versions must not be overwritten.
* The MVP supports Playwright TypeScript only.
* Generated code can be reviewed before download.
* `GeneratedCode` may store an individual generated test or a structured generation result.
* Complete ZIP packages should be stored outside PostgreSQL.
* `PackageStoragePath` stores the location of the generated package.
* Failed generations remain stored for troubleshooting.
* Token usage and estimated AI cost should be recorded.
* Downloading the package does not change the approval status of the Test Case.
* Generated automation must remain traceable to the original Test Case and Story.

---

# Test and Automation Indexes

## TestCases

* Index on `StoryId`
* Index on `StoryId, Status`
* Index on `CreatedByUserId`
* Index on `ApprovedByUserId`
* Index on `Source`
* Index on `DeletedAt`

## TestSteps

* Index on `TestCaseId`
* Unique index on `TestCaseId, StepNumber`

## JiraLinks

* Index on `JiraIntegrationId`
* Index on `EntityType, EntityId`
* Index on `JiraIssueKey`
* Unique filtered index on `JiraIntegrationId, EntityType, EntityId` when `DeletedAt` is null

## AutomationGenerations

* Index on `TestCaseId`
* Index on `TestCaseId, CreatedAt`
* Index on `RequestedByUserId`
* Index on `Status`


---
---
---
# PostgreSQL Schema

## General Database Conventions

The MVP database will follow these conventions:

* Primary keys use PostgreSQL `uuid`.
* Date and time fields use `timestamp with time zone`.
* Enum values are initially stored as `varchar`.
* Table names use plural PascalCase in documentation.
* Entity Framework Core will generate the final PostgreSQL naming convention.
* Soft-deleted records use a nullable `DeletedAt` column.
* All tenant-owned records must include or be traceable to an `OrganizationId`.
* Foreign key relationships must enforce organization and project isolation through application-level authorization.
* Sensitive values such as passwords, Jira credentials, and invitation tokens must never be stored as plain text.

---

# Organizations Schema

## Table

`Organizations`

## Columns

| Column      | PostgreSQL Type          | Nullable | Description                             |
| ----------- | ------------------------ | -------: | --------------------------------------- |
| Id          | uuid                     |       No | Primary key                             |
| Name        | varchar(200)             |       No | Organization display name               |
| Slug        | varchar(100)             |       No | URL-safe unique organization identifier |
| Status      | varchar(30)              |       No | Organization lifecycle status           |
| CreatedAt   | timestamp with time zone |       No | Creation timestamp                      |
| UpdatedAt   | timestamp with time zone |      Yes | Last update timestamp                   |
| SuspendedAt | timestamp with time zone |      Yes | Suspension timestamp                    |
| DeletedAt   | timestamp with time zone |      Yes | Soft deletion timestamp                 |

## Primary Key

* `Id`

## Unique Constraints

* `Slug`

## Supported Statuses

* Active
* Suspended
* Deleted

## Indexes

* Unique index on `Slug`
* Index on `Status`
* Index on `DeletedAt`

## Business Constraints

* Organization name is required.
* Organization Slug must be globally unique.
* Deleted Organizations cannot create new Projects or invite Users.
* Suspended Organizations retain their data but cannot perform write operations.

---

# Users Schema

## Table

`Users`

## Columns

| Column           | PostgreSQL Type          | Nullable | Description                            |
| ---------------- | ------------------------ | -------: | -------------------------------------- |
| Id               | uuid                     |       No | Primary key                            |
| OrganizationId   | uuid                     |       No | Owning Organization                    |
| FullName         | varchar(200)             |       No | User display name                      |
| Email            | varchar(320)             |       No | User email address                     |
| NormalizedEmail  | varchar(320)             |       No | Email used for case-insensitive lookup |
| PasswordHash     | text                     |       No | Secure password hash                   |
| OrganizationRole | varchar(30)              |       No | Organization-level role                |
| Status           | varchar(30)              |       No | User lifecycle status                  |
| EmailConfirmedAt | timestamp with time zone |      Yes | Email confirmation timestamp           |
| LastLoginAt      | timestamp with time zone |      Yes | Last successful login                  |
| CreatedAt        | timestamp with time zone |       No | Creation timestamp                     |
| UpdatedAt        | timestamp with time zone |      Yes | Last update timestamp                  |
| SuspendedAt      | timestamp with time zone |      Yes | Suspension timestamp                   |
| DeactivatedAt    | timestamp with time zone |      Yes | Deactivation timestamp                 |

## Primary Key

* `Id`

## Foreign Keys

* `OrganizationId` references `Organizations.Id`

## Supported Organization Roles

* Owner
* Administrator
* Member

## Supported Statuses

* Pending
* Active
* Suspended
* Deactivated

## Unique Constraints

For the MVP:

* `NormalizedEmail` is globally unique.

This means one email address can belong to only one Organization in the MVP.

## Indexes

* Unique index on `NormalizedEmail`
* Index on `OrganizationId`
* Index on `OrganizationId, Status`

## Business Constraints

* Every User belongs to exactly one Organization.
* Every Organization must have at least one Owner.
* A User cannot access data from another Organization.
* Project responsibilities are not stored in `OrganizationRole`.
* Project-level roles are stored in `ProjectMembers`.
* Email comparisons must be case-insensitive.
* PasswordHash must be generated using ASP.NET Core Identity or another approved secure password-hashing mechanism.

---

# Invitations Schema

## Table

`Invitations`

## Columns

| Column          | PostgreSQL Type          | Nullable | Description                           |
| --------------- | ------------------------ | -------: | ------------------------------------- |
| Id              | uuid                     |       No | Primary key                           |
| OrganizationId  | uuid                     |       No | Organization receiving the new member |
| Email           | varchar(320)             |       No | Invited email address                 |
| NormalizedEmail | varchar(320)             |       No | Case-insensitive email value          |
| TokenHash       | varchar(500)             |       No | Secure hash of invitation token       |
| Status          | varchar(30)              |       No | Invitation lifecycle status           |
| InvitedByUserId | uuid                     |       No | User who created the invitation       |
| ExpiresAt       | timestamp with time zone |       No | Expiration timestamp                  |
| AcceptedAt      | timestamp with time zone |      Yes | Acceptance timestamp                  |
| CancelledAt     | timestamp with time zone |      Yes | Cancellation timestamp                |
| CreatedAt       | timestamp with time zone |       No | Creation timestamp                    |
| UpdatedAt       | timestamp with time zone |      Yes | Last update timestamp                 |

## Primary Key

* `Id`

## Foreign Keys

* `OrganizationId` references `Organizations.Id`
* `InvitedByUserId` references `Users.Id`

## Supported Statuses

* Created
* Sent
* Accepted
* Expired
* Cancelled

## Indexes

* Index on `OrganizationId`
* Index on `NormalizedEmail`
* Index on `Status`
* Index on `ExpiresAt`
* Unique filtered index on `OrganizationId, NormalizedEmail` for active invitations

## Business Constraints

* Invitations are Organization-level only in the MVP.
* Only Organization Owners and Administrators can invite Users.
* Raw invitation tokens must never be stored.
* Accepted, expired, or cancelled invitations cannot be reused.
* The invited email must match the email used during invitation acceptance.
* Only one active invitation may exist for the same email and Organization.
* A User is assigned to Projects only after joining the Organization.

---

# Projects Schema

## Table

`Projects`

## Columns

| Column          | PostgreSQL Type          | Nullable | Description                             |
| --------------- | ------------------------ | -------: | --------------------------------------- |
| Id              | uuid                     |       No | Primary key                             |
| OrganizationId  | uuid                     |       No | Owning Organization                     |
| Name            | varchar(200)             |       No | Project display name                    |
| Slug            | varchar(100)             |       No | Organization-scoped URL-safe identifier |
| Description     | varchar(2000)            |      Yes | Project description                     |
| Status          | varchar(30)              |       No | Project lifecycle status                |
| CreatedByUserId | uuid                     |       No | User who created the Project            |
| CreatedAt       | timestamp with time zone |       No | Creation timestamp                      |
| UpdatedAt       | timestamp with time zone |      Yes | Last update timestamp                   |
| ArchivedAt      | timestamp with time zone |      Yes | Archive timestamp                       |
| DeletedAt       | timestamp with time zone |      Yes | Soft deletion timestamp                 |

## Primary Key

* `Id`

## Foreign Keys

* `OrganizationId` references `Organizations.Id`
* `CreatedByUserId` references `Users.Id`

## Supported Statuses

* Active
* Archived
* Deleted

## Unique Constraints

* `OrganizationId, Slug`

## Indexes

* Index on `OrganizationId`
* Index on `OrganizationId, Status`
* Unique index on `OrganizationId, Slug`
* Index on `DeletedAt`

## Business Constraints

* Every Project belongs to exactly one Organization.
* A Project represents a complete software product or platform.
* Business modules exist inside the Project and do not require separate Projects.
* Archived Projects are read-only.
* Deleted Projects use soft deletion.
* A Project must have at least one Project Admin.

---

# ProjectMembers Schema

## Table

`ProjectMembers`

## Columns

| Column    | PostgreSQL Type          | Nullable | Description                     |
| --------- | ------------------------ | -------: | ------------------------------- |
| Id        | uuid                     |       No | Primary key                     |
| ProjectId | uuid                     |       No | Project membership              |
| UserId    | uuid                     |       No | Organization User               |
| Role      | varchar(50)              |       No | User's role inside the Project  |
| Status    | varchar(30)              |       No | Membership lifecycle status     |
| JoinedAt  | timestamp with time zone |       No | Membership activation timestamp |
| RemovedAt | timestamp with time zone |      Yes | Membership removal timestamp    |
| CreatedAt | timestamp with time zone |       No | Creation timestamp              |
| UpdatedAt | timestamp with time zone |      Yes | Last update timestamp           |

## Primary Key

* `Id`

## Foreign Keys

* `ProjectId` references `Projects.Id`
* `UserId` references `Users.Id`

## Supported Roles

* ProjectAdmin
* BusinessAnalyst
* ManualQAEngineer
* AutomationQAEngineer
* QALead

## Supported Statuses

* Active
* Removed

## Unique Constraints

* `ProjectId, UserId`

## Indexes

* Unique index on `ProjectId, UserId`
* Index on `UserId`
* Index on `ProjectId, Role`
* Index on `ProjectId, Status`

## Business Constraints

* A User cannot access a Project unless an active Project Member record exists.
* The User and Project must belong to the same Organization.
* A User may have a different Role in each Project.
* Each Project must have at least one active `ProjectAdmin`.
* Removing a Project Member does not delete their historical contributions.
* Project Role is implemented as an Enum in the MVP.



---
---
---
---
# Stories Schema

## Table

`Stories`

## Columns

| Column             | PostgreSQL Type          | Nullable | Description                               |
| ------------------ | ------------------------ | -------: | ----------------------------------------- |
| Id                 | uuid                     |       No | Primary key                               |
| ProjectId          | uuid                     |       No | Owning Project                            |
| JiraIntegrationId  | uuid                     |      Yes | Jira integration used to import the Story |
| Source             | varchar(30)              |       No | Story source                              |
| ExternalId         | varchar(100)             |      Yes | Jira internal issue identifier            |
| ExternalKey        | varchar(100)             |      Yes | Jira issue key, such as `PROJ-125`        |
| Title              | varchar(500)             |       No | Story title                               |
| Description        | text                     |      Yes | Requirement description                   |
| AcceptanceCriteria | text                     |      Yes | Acceptance criteria                       |
| Priority           | varchar(30)              |      Yes | Requirement priority                      |
| WorkflowStatus     | varchar(50)              |       No | Internal platform workflow status         |
| ExternalStatus     | varchar(100)             |      Yes | Jira workflow status                      |
| EpicKey            | varchar(100)             |      Yes | Optional Jira Epic key                    |
| EpicName           | varchar(500)             |      Yes | Optional Epic name                        |
| SprintName         | varchar(300)             |      Yes | Optional Sprint name                      |
| ReleaseName        | varchar(300)             |      Yes | Optional Release name or fix version      |
| Labels             | jsonb                    |      Yes | Jira or platform labels                   |
| CreatedByUserId    | uuid                     |      Yes | User who manually created the Story       |
| ApprovedByUserId   | uuid                     |      Yes | Business Analyst who approved it          |
| ApprovedAt         | timestamp with time zone |      Yes | Approval timestamp                        |
| ExternalCreatedAt  | timestamp with time zone |      Yes | Original Jira creation timestamp          |
| ExternalUpdatedAt  | timestamp with time zone |      Yes | Latest Jira update timestamp              |
| LastSyncedAt       | timestamp with time zone |      Yes | Latest successful Jira synchronization    |
| CreatedAt          | timestamp with time zone |       No | Platform creation timestamp               |
| UpdatedAt          | timestamp with time zone |      Yes | Platform update timestamp                 |
| ArchivedAt         | timestamp with time zone |      Yes | Archive timestamp                         |
| DeletedAt          | timestamp with time zone |      Yes | Soft deletion timestamp                   |

## Primary Key

* `Id`

## Foreign Keys

* `ProjectId` references `Projects.Id`
* `JiraIntegrationId` references `JiraIntegrations.Id`
* `CreatedByUserId` references `Users.Id`
* `ApprovedByUserId` references `Users.Id`

## Supported Sources

* Manual
* Jira

## Supported Workflow Statuses

* Draft
* ReadyForAnalysis
* UnderBusinessReview
* Approved
* Rejected
* Archived

## Supported Priorities

* Lowest
* Low
* Medium
* High
* Highest
* Critical

## Unique Constraints

For Jira-imported Stories:

* `JiraIntegrationId, ExternalId`
* `JiraIntegrationId, ExternalKey`

These constraints apply only when the external values are not null and the Story is not soft-deleted.

## Indexes

* Index on `ProjectId`
* Index on `ProjectId, WorkflowStatus`
* Index on `ProjectId, Source`
* Index on `JiraIntegrationId`
* Index on `ExternalKey`
* Index on `ApprovedByUserId`
* Index on `LastSyncedAt`
* Index on `DeletedAt`
* GIN index on `Labels`

## Business Constraints

* Every Story belongs to exactly one Project.
* A manually created Story must reference `CreatedByUserId`.
* A Jira-imported Story must reference `JiraIntegrationId`.
* Jira-imported Story content is read-only inside the platform.
* Manual Stories can be edited by authorized Project Members.
* Jira changes are applied only through user-initiated synchronization.
* Approval requires an active Project Member with the Business Analyst, QA Lead, or Project Admin role.
* An approved Story becomes available for Test Case generation.
* Epics, Sprints, and Releases remain optional.
* The MVP stores the latest synchronized Jira content only.
* Soft-deleted Stories are excluded from active workflows.

---

# StoryComments Schema

## Table

`StoryComments`

## Columns

| Column            | PostgreSQL Type          | Nullable | Description                           |
| ----------------- | ------------------------ | -------: | ------------------------------------- |
| Id                | uuid                     |       No | Primary key                           |
| StoryId           | uuid                     |       No | Owning Story                          |
| Source            | varchar(30)              |       No | Comment source                        |
| ExternalId        | varchar(150)             |      Yes | Jira comment identifier               |
| Content           | text                     |       No | Comment content                       |
| AuthorDisplayName | varchar(300)             |      Yes | External author display name          |
| CreatedByUserId   | uuid                     |      Yes | Platform User who created the Comment |
| ExternalCreatedAt | timestamp with time zone |      Yes | Jira creation timestamp               |
| ExternalUpdatedAt | timestamp with time zone |      Yes | Jira update timestamp                 |
| CreatedAt         | timestamp with time zone |       No | Platform creation timestamp           |
| UpdatedAt         | timestamp with time zone |      Yes | Platform update timestamp             |
| DeletedAt         | timestamp with time zone |      Yes | Soft deletion timestamp               |

## Primary Key

* `Id`

## Foreign Keys

* `StoryId` references `Stories.Id`
* `CreatedByUserId` references `Users.Id`

## Supported Sources

* Platform
* Jira

## Unique Constraints

* `StoryId, ExternalId` when `ExternalId` is not null and `DeletedAt` is null

## Indexes

* Index on `StoryId`
* Index on `StoryId, Source`
* Index on `CreatedByUserId`
* Index on `DeletedAt`

## Business Constraints

* Jira comments are read-only inside the platform.
* Platform comments can be edited by their creator or an authorized Project Member.
* Platform-created comments require `CreatedByUserId`.
* Jira comments require `ExternalId`.
* Imported Jira comments must not be duplicated during synchronization.
* Comments may be included in the Project Context used during AI analysis.
* Deleting a Story must not physically delete Comments immediately; soft deletion or controlled cascading must preserve auditability.

---

# StoryAttachments Schema

## Table

`StoryAttachments`

## Columns

| Column            | PostgreSQL Type          | Nullable | Description                         |
| ----------------- | ------------------------ | -------: | ----------------------------------- |
| Id                | uuid                     |       No | Primary key                         |
| StoryId           | uuid                     |       No | Owning Story                        |
| Source            | varchar(30)              |       No | Attachment source                   |
| ExternalId        | varchar(150)             |      Yes | Jira attachment identifier          |
| FileName          | varchar(500)             |       No | Original file name                  |
| ContentType       | varchar(200)             |      Yes | MIME content type                   |
| FileSizeBytes     | bigint                   |      Yes | File size in bytes                  |
| StorageProvider   | varchar(50)              |      Yes | Storage provider name               |
| StoragePath       | text                     |      Yes | Internal storage location           |
| ExternalUrl       | text                     |      Yes | Jira attachment URL                 |
| UploadedByUserId  | uuid                     |      Yes | Platform User who uploaded the file |
| ExternalCreatedAt | timestamp with time zone |      Yes | Jira attachment timestamp           |
| CreatedAt         | timestamp with time zone |       No | Platform creation timestamp         |
| DeletedAt         | timestamp with time zone |      Yes | Soft deletion timestamp             |

## Primary Key

* `Id`

## Foreign Keys

* `StoryId` references `Stories.Id`
* `UploadedByUserId` references `Users.Id`

## Supported Sources

* Platform
* Jira

## Unique Constraints

* `StoryId, ExternalId` when `ExternalId` is not null and `DeletedAt` is null

## Indexes

* Index on `StoryId`
* Index on `StoryId, Source`
* Index on `UploadedByUserId`
* Index on `DeletedAt`

## Business Constraints

* The database stores attachment metadata only.
* File contents are stored outside PostgreSQL.
* Platform attachments require a valid internal `StoragePath`.
* Jira attachments may use `ExternalUrl` without copying the file in the MVP.
* Files must be validated by type and size before storage.
* Access to attachments must be authorized through the Story's Project and Organization.
* Jira attachment URLs and credentials must not be exposed to unauthorized users.

---

# AIAnalyses Schema

## Table

`AIAnalyses`

## Columns

| Column              | PostgreSQL Type          | Nullable | Description                        |
| ------------------- | ------------------------ | -------: | ---------------------------------- |
| Id                  | uuid                     |       No | Primary key                        |
| StoryId             | uuid                     |       No | Analyzed Story                     |
| RequestedByUserId   | uuid                     |       No | User who requested the Analysis    |
| Status              | varchar(30)              |       No | Execution status                   |
| ModelProvider       | varchar(100)             |       No | AI provider                        |
| ModelName           | varchar(150)             |       No | Model identifier                   |
| PromptVersion       | varchar(50)              |       No | Prompt template version            |
| ContextStrategy     | varchar(50)              |       No | Context retrieval strategy         |
| RelatedStoriesCount | integer                  |       No | Number of related Stories supplied |
| InputTokenCount     | integer                  |      Yes | Input tokens consumed              |
| OutputTokenCount    | integer                  |      Yes | Output tokens consumed             |
| EstimatedCost       | numeric(18,6)            |      Yes | Estimated provider cost            |
| ErrorCode           | varchar(100)             |      Yes | Structured failure code            |
| ErrorMessage        | text                     |      Yes | Failure details                    |
| StartedAt           | timestamp with time zone |      Yes | Execution start                    |
| CompletedAt         | timestamp with time zone |      Yes | Execution completion               |
| CreatedAt           | timestamp with time zone |       No | Record creation timestamp          |

## Primary Key

* `Id`

## Foreign Keys

* `StoryId` references `Stories.Id`
* `RequestedByUserId` references `Users.Id`

## Supported Statuses

* Pending
* Processing
* Completed
* Failed
* Cancelled

## Supported Context Strategies

* StoryOnly
* RelatedStories
* ProjectKnowledgeRag

The expected MVP strategy is `ProjectKnowledgeRag`.

## Indexes

* Index on `StoryId`
* Index on `StoryId, CreatedAt`
* Index on `RequestedByUserId`
* Index on `Status`
* Index on `CreatedAt`

## Business Constraints

* Every AI Analysis belongs to exactly one Story.
* Every execution creates a new record.
* Previous Analyses must not be overwritten.
* Only active Project Members with an authorized role can request an Analysis.
* The requesting User and Story must belong to the same Organization.
* AI Analysis does not change or approve Story content automatically.
* Failed Analyses remain stored for troubleshooting.
* Token and estimated cost information should be recorded when provided by the AI provider.
* Sensitive complete prompts should not be stored by default.
* Generated AI output must pass structural validation before Knowledge Items are created.
* A completed Analysis may generate zero or more Knowledge Items.

---

# KnowledgeItems Schema

## Table

`KnowledgeItems`

## Columns

| Column           | PostgreSQL Type          | Nullable | Description                            |
| ---------------- | ------------------------ | -------: | -------------------------------------- |
| Id               | uuid                     |       No | Primary key                            |
| ProjectId        | uuid                     |       No | Owning Project                         |
| OriginStoryId    | uuid                     |       No | Story that produced the Knowledge Item |
| AIAnalysisId     | uuid                     |      Yes | AI Analysis that extracted it          |
| Type             | varchar(50)              |       No | Knowledge classification               |
| Title            | varchar(500)             |       No | Short human-readable title             |
| Content          | text                     |       No | Knowledge content                      |
| Status           | varchar(30)              |       No | Human review status                    |
| ConfidenceScore  | numeric(5,4)             |      Yes | AI confidence from 0 to 1              |
| Source           | varchar(30)              |       No | AI or User                             |
| ReviewedByUserId | uuid                     |      Yes | User who reviewed the item             |
| ReviewedAt       | timestamp with time zone |      Yes | Review timestamp                       |
| CreatedByUserId  | uuid                     |      Yes | User who manually created it           |
| CreatedAt        | timestamp with time zone |       No | Creation timestamp                     |
| UpdatedAt        | timestamp with time zone |      Yes | Last update timestamp                  |
| SupersededAt     | timestamp with time zone |      Yes | Timestamp when replaced                |
| DeletedAt        | timestamp with time zone |      Yes | Soft deletion timestamp                |
| Embedding        | vector                   |      Yes | Semantic-search embedding              |

## Embedding Dimension

The exact dimension must match the selected embedding model.

It should be configured through the AI infrastructure layer rather than being treated as a permanent business rule.

Example:

```sql
Embedding vector(1536)
```

The example dimension must be updated if a different embedding model is selected.

## Primary Key

* `Id`

## Foreign Keys

* `ProjectId` references `Projects.Id`
* `OriginStoryId` references `Stories.Id`
* `AIAnalysisId` references `AIAnalyses.Id`
* `ReviewedByUserId` references `Users.Id`
* `CreatedByUserId` references `Users.Id`

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

## Supported Sources

* AI
* User

## Indexes

* Index on `ProjectId`
* Index on `OriginStoryId`
* Index on `AIAnalysisId`
* Index on `ProjectId, Type`
* Index on `ProjectId, Status`
* Index on `ReviewedByUserId`
* Index on `DeletedAt`
* Vector similarity index on `Embedding`

## Vector Index Strategy

For the MVP, use one of the pgvector-supported approximate indexes after sufficient data exists:

* HNSW
* IVFFlat

HNSW is the preferred initial option when supported by the selected PostgreSQL and pgvector versions.

Do not create an approximate vector index prematurely on an empty or very small dataset without validating its operational value.

## Business Constraints

* Every Knowledge Item belongs to exactly one Project.
* Every Knowledge Item has exactly one Origin Story in the MVP.
* The Origin Story and Knowledge Item must belong to the same Project.
* AI-generated Knowledge Items reference an `AIAnalysisId`.
* Manually created Knowledge Items reference `CreatedByUserId`.
* AI-generated Knowledge Items are never auto-approved.
* Only authorized Project Members can review Knowledge Items.
* `ConfidenceScore`, when present, must be between 0 and 1.
* Rejected Items remain stored for traceability.
* Superseded Items remain available but are excluded from active Knowledge retrieval.
* Only approved Knowledge Items should be treated as trusted Project Knowledge.
* Under-review Items may be shown to users but must be marked clearly.
* Embeddings must be regenerated when the searchable Knowledge content changes.
* Semantic similarity must assist users and AI retrieval, not automatically determine business truth.

---

# Story and Knowledge Referential Rules

## Deletion Behavior

* Deleting an Organization must be restricted and handled through controlled tenant deactivation.
* Deleting a Project uses soft deletion.
* Deleting a Story uses soft deletion.
* Story Comments and Attachments should remain traceable after Story archival or soft deletion.
* AI Analyses must not be physically deleted when a Story changes.
* Knowledge Items must not be automatically deleted when the Origin Story is archived.
* A Knowledge Item may instead be marked `Superseded` or `Rejected` after review.

## Tenant Isolation

Every query for these tables must be scoped through:

```text
Organization
→ Project
→ Story / Knowledge Item
```

A record identifier alone must never be considered sufficient authorization.

## Concurrency

Stories and Knowledge Items should support optimistic concurrency through an EF Core concurrency token.

A future implementation may use:

* PostgreSQL `xmin`
* An application-managed version column

This prevents one reviewer from silently overwriting another reviewer's changes.


---

# JiraIntegrations Schema

## Table

`JiraIntegrations`

## Purpose

Stores the Jira connection and project mapping used by an AI QA Platform Project.

## Columns

| Column | PostgreSQL Type | Nullable | Description |
|---|---|---:|---|
| Id | uuid | No | Primary key |
| ProjectId | uuid | No | AI QA Platform Project |
| Name | varchar(200) | No | Integration display name |
| JiraBaseUrl | varchar(1000) | No | Jira instance base URL |
| JiraProjectId | varchar(100) | No | Jira internal project identifier |
| JiraProjectKey | varchar(100) | No | Jira project key |
| AuthenticationType | varchar(50) | No | Authentication method |
| EncryptedCredential | text | No | Encrypted Jira credential or token |
| Status | varchar(30) | No | Integration status |
| LastSuccessfulSyncAt | timestamp with time zone | Yes | Latest successful synchronization |
| LastSyncAttemptAt | timestamp with time zone | Yes | Latest synchronization attempt |
| CreatedByUserId | uuid | No | User who created the Integration |
| CreatedAt | timestamp with time zone | No | Creation timestamp |
| UpdatedAt | timestamp with time zone | Yes | Last update timestamp |
| DeletedAt | timestamp with time zone | Yes | Soft deletion timestamp |

## Primary Key

- `Id`

## Foreign Keys

- `ProjectId` references `Projects.Id`
- `CreatedByUserId` references `Users.Id`

## Supported Authentication Types

- ApiToken
- OAuth

## Supported Statuses

- Active
- Disabled
- ConnectionFailed
- Deleted

## Unique Constraints

- `ProjectId, JiraBaseUrl, JiraProjectId` for active Integrations

## Indexes

- Index on `ProjectId`
- Index on `ProjectId, Status`
- Index on `JiraProjectKey`
- Index on `DeletedAt`

## Business Constraints

- Every Jira Integration belongs to exactly one Project.
- The Jira Integration and Project must belong to the same Organization context.
- Jira credentials must always be encrypted at rest.
- Credentials and tokens must never be returned through normal API responses.
- Jira synchronization is always initiated manually in the MVP.
- A disabled Integration cannot import or synchronize Stories.
- Removing an Integration must not delete imported Stories, Test Cases, or historical Jira Links.
- Connection testing must occur before an Integration becomes Active.
- Multiple Jira Integrations may exist for one Project when needed.

