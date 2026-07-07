# Risk Register

This document tracks the major risks that could impact the successful development and adoption of the AI QA Platform.

---

## Risk-001

### Title

High AI Token Cost

### Description

Analyzing large projects may require a significant number of AI tokens, increasing operational costs.

### Impact

High

### Probability

High

### Mitigation

* Use Embeddings.
* Use Retrieval-Augmented Generation (RAG).
* Send only relevant project context.
* Cache AI responses when possible.

### Status

Open

---

## Risk-002

### Title

AI Hallucination

### Description

The AI may generate incorrect business rules, acceptance criteria, or test cases.

### Impact

High

### Probability

Medium

### Mitigation

* Human review is mandatory.
* AI output is never auto-approved.
* Users can edit every AI-generated artifact.

### Status

Open

---

## Risk-003

### Title

Business Rule Conflicts

### Description

Different Stories may contain contradictory business rules.

### Impact

High

### Probability

High

### Mitigation

* Build and maintain a Project Knowledge Base.
* Detect related Stories.
* Run cross-Story conflict analysis.

### Status

Open

---

## Risk-004

### Title

Requirement Changes

### Description

Requirements may change after test cases or automation have already been generated.

### Impact

High

### Probability

High

### Mitigation

* Manual Jira synchronization.
* Story versioning (future release).
* Notify users about detected changes.

### Status

Open

---

## Risk-005

### Title

Vendor Lock-in

### Description

Depending on a single AI provider may limit flexibility and increase long-term costs.

### Impact

Medium

### Probability

Medium

### Mitigation

Design the AI layer using provider abstraction to support multiple LLM providers in future releases.

### Status

Open

---

## Risk-006

### Title

Poor Requirement Quality

### Description

AI output quality depends on the quality of the input requirements.

### Impact

High

### Probability

High

### Mitigation

Improve requirement quality through the Knowledge Module before generating test cases.

### Status

Open

---

## Risk-007

### Title

Scalability of the Knowledge Base

### Description

Large projects may contain thousands of Stories, making semantic search slower and increasing storage requirements.

### Impact

Medium

### Probability

Medium

### Mitigation

* Use pgvector for embeddings.
* Index embeddings efficiently.
* Retrieve only the top relevant Stories.
* Archive inactive projects when appropriate.

### Status

Open
