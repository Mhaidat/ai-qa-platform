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
