# Architecture

## 1. Architectural Vision

The AI QA Platform is designed around **Project Knowledge**, not around AI prompts.

Artificial Intelligence is a supporting capability used to understand project requirements, enrich project knowledge, and assist software teams throughout the QA lifecycle.

The platform does not analyze isolated Stories. Instead, it continuously builds a shared Knowledge Base for each project, allowing AI to make better and more context-aware decisions.

---

# 2. Architectural Principles

## Knowledge First

Every AI capability must use project knowledge before generating any output.

The platform continuously builds and enriches a project Knowledge Base that serves as the primary context source for AI.

---

## Human First

Artificial Intelligence assists users but never replaces human decisions.

Every AI-generated output requires human review and approval before progressing to the next workflow stage.

---

## Traceability First

Every generated artifact must remain traceable to its originating Story.

Stories, AI Analysis, Test Cases, Jira links, and Automation must always preserve their relationships.

---

## Non-Destructive AI

AI never overwrites user work automatically.

All AI suggestions are editable, reviewable, and versioned.

---

## Modular Architecture

Every business capability is implemented as an independent module that can evolve without affecting the rest of the platform.

---

# 3. Core Domain Model

The AI QA Platform is built around the following core business entities.

---

## Organization

Represents a customer company using the platform.

Examples:

* TestCrew
* ABC Software
* Digital Transformation Company

Responsibilities:

* Owns users
* Owns projects
* Owns subscriptions
* Owns organization settings

---

## User

Represents a person using the platform.

A User belongs to one Organization.

Supported roles:

* Organization Owner
* Administrator
* Business Analyst
* Manual QA Engineer
* Automation QA Engineer
* QA Lead

---

## Project

Represents a software project.

A Project is the root business entity that owns all project knowledge.

A Project contains:

* Stories
* Knowledge Base
* AI Analysis
* Test Cases
* Jira Integration
* Automation

---

## Story

Represents a software requirement.

Stories may be:

* Imported from Jira
* Created manually

Stories are the primary requirement entity.

Epics, Releases, and Sprints are optional organizational structures.

---

## Knowledge Base

Represents the shared understanding of the project.

The Knowledge Base is continuously updated whenever Stories are created or modified.

Initial MVP knowledge includes:

* Business Rules
* Business Gaps
* Ambiguities
* Suggested Acceptance Criteria
* Related Stories
* Potential Conflicts

The Knowledge Base is visible to users and is considered the core intelligence layer of the platform.

---

## AI Analysis

Represents a single AI analysis execution for a Story.

Multiple analyses may exist for the same Story.

AI Analysis may include:

* Business Rule Extraction
* Gap Detection
* Ambiguity Detection
* Suggested Acceptance Criteria
* Related Stories
* Conflict Detection

---

## Test Case

Represents a manual test case.

A Test Case belongs to one Story.

A Test Case may be:

* AI Generated
* Manually Created
* Edited
* Approved
* Linked to Jira
* Used for Automation

---

## Jira Integration

Represents the Jira configuration for a Project.

Responsibilities:

* Connect Jira
* Import Stories
* Link Test Cases
* Manual Synchronization
* Synchronization History

Synchronization is always initiated by the user.

---

## Automation Generation

Represents an automation generation request.

Automation is generated only from approved Test Cases.

Generated artifacts may include:

* Playwright TypeScript Tests
* Page Object Model
* Assertions
* Test Data
* Downloadable ZIP Package

---

# 4. High-Level Domain Relationship

Organization

→ Users

→ Projects

↓

Project

→ Knowledge Base

→ Stories

→ Jira Integration

↓

Story

→ AI Analysis

→ Test Cases

↓

Test Case

→ Jira Link

→ Automation Generation

---

# 5. Knowledge-Centered Architecture

The platform is centered around the Project Knowledge Base rather than isolated AI prompts.

Workflow:

Project

↓

Stories

↓

Knowledge Base Construction

↓

Knowledge Enrichment

↓

AI Analysis

↓

Manual Review

↓

Approved Knowledge

↓

Test Case Generation

↓

Automation Generation

The Knowledge Base becomes the primary source of context for every AI operation.

---

# 6. High-Level Module Flow

Organization

↓

Project

↓

Stories

↓

Knowledge Base

↓

AI Analysis

↓

Manual Testing

↓

Jira Synchronization

↓

Automation Generation

---

# 7. Technology Stack (MVP)

## Backend

* ASP.NET Core
* Entity Framework Core (Code First)
* Clean Architecture
* Domain-Driven Design (DDD)
* JWT Authentication

---

## Database

* PostgreSQL
* pgvector

---

## Frontend

* React 19
* TypeScript
* Vite
* Tailwind CSS
* shadcn/ui
* Zustand
* TanStack Query
* React Hook Form

---

## AI

* OpenAI-Compatible APIs
* Embeddings
* Retrieval-Augmented Generation (RAG)

---

## Automation

* Playwright
* TypeScript

---

## Integrations

* Jira Cloud
---

# 8. Future Architectural Evolution

Future versions may introduce:

* Knowledge Graph
* Multi-LLM Support
* Local AI Models
* Distributed AI Services
* Redis Cache
* Object Storage
* Event-Driven Architecture
* Microservices (if required)

# 8. Future Architectural Evolution

Future versions may introduce:

* Multi-LLM Support
* Knowledge Graph
* Local AI Models
* Redis
* Object Storage (S3 / Azure Blob)
* Background Job Processing
* Event-Driven Architecture
* Microservices (only if justified by scale)

The MVP will remain a modular monolith to simplify development, deployment, testing, and maintenance.

The MVP will remain a modular monolithic architecture to simplify development, deployment, and maintenance.
