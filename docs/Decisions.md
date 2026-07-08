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
