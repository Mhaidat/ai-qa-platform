# Risk Register

This document tracks technical, business, security, AI, and operational risks that may affect the AI QA Platform.

Each risk includes:

- Description
- Impact
- Probability
- Mitigation
- Status

# Risk-001

Title

High AI Token Cost

Description

Sending complete project requirements to the LLM may become expensive as projects grow.

Impact

High

Probability

High

Mitigation

Use RAG.

Use Embeddings.

Use Vector Database.

Send only relevant context.

Status

Open


# Risk-002

Title

AI Hallucination

Description

The AI may generate business rules or test cases that are not supported by the requirement.

Impact

High

Probability

Medium

Mitigation

Human review is mandatory.

AI suggestions are never auto-approved.

Status

Open


# Risk-003

Title

Business Rule Conflicts

Description

Different user stories may contain contradictory business rules.

Example:

Story A:
Registration uses Email only.

Story B:
Send SMS after registration.

Impact

High

Probability

High

Mitigation

Create a project Knowledge Base.

Use semantic search before AI generation.

Detect conflicting business rules.

Status

Open


# Risk-003

Title

Business Rule Conflicts

Description

Different user stories may contain contradictory business rules.

Example:

Story A:
Registration uses Email only.

Story B:
Send SMS after registration.

Impact

High

Probability

High

Mitigation

Create a project Knowledge Base.

Use semantic search before AI generation.

Detect conflicting business rules.

Status

Open

# Risk-004

Title

Requirement Changes

Description

Approved requirements may change after test cases and automation have already been generated.

Impact

High

Probability

High

Mitigation

Manual Jira synchronization.

Versioning.

Impact Analysis.

Status

Open


# Risk-005

Title

Large Prompt Size

Description

Large prompts increase latency, token cost, and may exceed model context limits.

Impact

High

Probability

High

Mitigation

Chunking.

RAG.

Summaries.

Business Rule extraction.

Status

Open


# Risk-006

Title

Vendor Lock-in

Description

The platform depends on a single AI provider.

Impact

Medium

Probability

Medium

Mitigation

Support multiple LLM providers.

OpenAI

Azure OpenAI

Anthropic

Local Models

Status

Open
