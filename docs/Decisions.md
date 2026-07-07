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
