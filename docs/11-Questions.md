# Questions & Answers

## Q001

### Question

What happens if Jira Story changes after Test Cases are approved?

### Answer

Jira changes will not be synchronized automatically.

The user must manually trigger synchronization.

If changes are detected, the platform shows the differences and allows the user to review them before applying any update.

Approved test cases and generated automation must not be overwritten automatically.

---

## Q002

### Question

Can Manual QA create Test Cases manually?

### Answer

Yes.

Manual QA can create test cases manually in addition to AI-generated test cases.

Each test case must store its source:

- AI Generated
- Manual
- Imported

---

## Q003

### Question

How can AI detect business conflicts across different User Stories?

### Answer

The platform will use a Project Knowledge Base.

Stories will be indexed using embeddings.

When analyzing a Story, the system retrieves related Stories and Knowledge Items, then sends only the relevant context to the AI.

This helps detect conflicts such as:

- One Story says registration uses email only.
- Another Story requires sending SMS to the user phone number.

---

## Q004

### Question

Who owns the Organization?

### Answer

The first user who registers a new company becomes the Organization Owner.

The Organization Owner can invite team members, assign roles, manage organization settings, and manage projects.

---

## Q005

### Question

Can one user belong to multiple organizations?

### Answer

Not in the MVP.

In the MVP, each user belongs to one Organization only.

Multi-organization membership may be added in a future release.

---

## Q006

### Question

How are AI prompts optimized to reduce token cost?

### Answer

The platform will avoid sending the full project context to the AI.

Instead, it will use:

- Embeddings
- Semantic search
- Related Story detection
- Knowledge Items
- RAG

Only the most relevant context will be sent to the AI.
