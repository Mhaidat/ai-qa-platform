# Domain Model

## Core Entities

- Organization
- User
- Invitation
- Project
- Story
- Story Comment
- Story Attachment
- Knowledge Item
- AI Analysis
- Test Case
- Test Step
- Jira Integration
- Jira Link
- Automation Generation

## Optional / Future Entities

- Subscription
- Epic
- Sprint
- Release
- AI Settings
- Audit Log

---

# Organization

## Purpose

Represents a customer company using the platform.

## Responsibilities

- Own users.
- Own projects.
- Own organization settings.
- Control access to company data.

## Relationships

- Organization has many Users.
- Organization has many Projects.
- Organization has many Invitations.

## Business Rules

- The first user who registers creates an Organization.
- The first user becomes the Organization Owner.
- Organization data must be isolated from other organizations.
- Users from one Organization cannot access another Organization's data.

## Lifecycle

Created

↓

Active

↓

Suspended

↓

Deleted
