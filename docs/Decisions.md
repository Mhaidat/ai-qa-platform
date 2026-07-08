# Decisions

## Decision 001 - Jira Synchronization Strategy

Jira synchronization will be manual and initiated by the user.

Reason:
Automatic synchronization may overwrite reviewed requirements, approved test cases, or generated automation.

---

## Decision 002 - Organization Registration

The first user who registers creates an Organization and becomes the Organization Owner/Admin.

Admins can invite employees and assign roles.

---

## Decision 003 - Flexible Project Structure

The platform will not enforce Scrum or any specific methodology.

Stories are the core entity.

Epics, Releases, and Sprints are optional.

---

## Decision 004 - Database Technology

The MVP will use PostgreSQL with pgvector.

Reason:
PostgreSQL supports relational SaaS data, and pgvector supports embeddings for the Knowledge Base.

## Decision 005 - Knowledge-Centered Architecture

The platform will be designed around a Project Knowledge Base rather than isolated AI prompts.

AI will be used to build and enrich project knowledge, but the Knowledge Base will remain the central product value.

Reason:
This allows the platform to understand project context, detect conflicts across Stories, reduce prompt size, reduce AI cost, and improve output quality.

Result:
Stories, AI Analysis, Test Cases, and Automation will all depend on project knowledge instead of working in isolation.

## Decision 006

Knowledge Base is not a stored entity.

It is a logical view built from Knowledge Items extracted from Stories.

## Decision 007 - Project-Level Roles

User roles inside the platform will be project-specific.

A User may have different roles across different Projects.

This will be represented by a Project Member entity.

Reason:
In real QA teams, the same person may act as Manual QA in one Project, Automation QA in another Project, and QA Lead in another.

Result:
Authorization will be more flexible and closer to real-world team structures.

---

## Decision 008 - Organization-Level Invitations

Invitations will be handled at the Organization level in the MVP.

Users will first join the Organization, then Organization Owners or Administrators can assign them to Projects.

Reason:
In real companies, employees usually belong to the company first, then they are assigned to projects.

Result:
The onboarding flow remains simple and flexible.

---

## Decision 009 - Project Role as Enum in MVP

Project roles will be implemented as fixed Enum values in the MVP.

Supported roles:

- Business Analyst
- Manual QA Engineer
- Automation QA Engineer
- QA Lead
- Project Admin

Reason:
Custom role and permission management adds complexity and is not required for the first commercial version.

Result:
The MVP will ship faster while keeping the design open for custom roles in future releases.

## Decision 010 - Project Represents a Software Product

A Project represents a complete software product or platform, not a single module.

Modules or functional areas are managed inside the Project.

Reason:
In real QA teams, multiple business modules usually belong to the same software product and are tested under one project context.

Result:
The Project Knowledge Base can understand the entire product and detect conflicts across different modules.

## Decision 011 - Story Versioning

The MVP stores only the latest version of each Story.

Future releases may introduce Story Versioning to preserve the complete history of requirement changes.

Reason:
Versioning adds significant complexity and is not essential for the first commercial release.

Result:
The domain remains simple while allowing future evolution.

## Decision 012 - Story Source of Truth

Stories imported from Jira are read-only inside the AI QA Platform.

The platform may enrich imported Stories with AI Analysis, Knowledge Items, Comments, Test Cases, and Automation, but it will never modify the original Story content.

Stories created inside the platform remain fully editable.

Reason:
Keeping Jira as the source of truth prevents synchronization conflicts and maintains consistency between both systems.

Result:
The platform enriches imported Stories without taking ownership of their original content.

## Decision 013 - Test Case Ownership

Every Test Case belongs to exactly one Story.

The platform will use a one-to-many relationship between Story and Test Case.

Reason:
Most real-world test cases are created from a specific Story, and rare cases that may relate to multiple Stories can be handled by duplicating or referencing the related Story manually.

Result:
Traceability remains simple, clear, and easy to manage.

## Decision 014 - Knowledge Item Ownership

In the MVP, Knowledge Items belong to a Project and have one Origin Story.

A Story can produce many Knowledge Items.

Future versions may introduce Knowledge Sources to allow one Knowledge Item to be linked to multiple Stories.

Reason:
This keeps the MVP simple while preserving traceability between project knowledge and the Story that produced it.

Result:
The Knowledge Base remains project-level, while every Knowledge Item can still be traced back to its originating Story.

## Decision 015 - Knowledge Item Relationship

Knowledge Items belong to the Project, not directly to the Story.

In the MVP, each Knowledge Item keeps an Origin Story reference for traceability.

Reason:
Knowledge represents project-level understanding, while the Origin Story preserves where the knowledge came from.

Result:
The Knowledge Base can evolve as a project-level concept while still maintaining traceability to Stories.
