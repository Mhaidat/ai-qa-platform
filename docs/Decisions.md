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
