# Decision 001

## Title

Jira Synchronization Strategy

## Decision

The platform will not automatically synchronize changes from Jira.

Synchronization will always be initiated manually by the user.

## Reason

Automatic synchronization may overwrite reviewed requirements, approved test cases, or generated automation.

Manual synchronization allows users to review incoming changes before applying them.

## Result

Users remain in control of their work while maintaining traceability with Jira.


# Decision 002

User signs up → creates Organization → becomes Owner/Admin.
Admin invites employees.
Employees do not create companies unless they sign up independently.
All Projects belong to an Organization.
Users access Projects only if invited.

# Decision 003
The platform will not enforce a specific project management methodology (Scrum, Kanban, etc.).
Stories are the core entity.
Releases, Sprints, and Epics are optional organizational structures.



# Decision 004

## Title

Database Technology Selection

## Decision

The MVP will use PostgreSQL as the primary database and pgvector for storing and searching AI embeddings.

## Reason

PostgreSQL is suitable for SaaS applications, cost-effective, scalable, and supports relational data well.

pgvector allows the platform to store embeddings inside PostgreSQL without introducing a separate vector database in the MVP.

## Result

The platform will use one database technology in the MVP while still supporting future AI Knowledge Base and RAG capabilities.
