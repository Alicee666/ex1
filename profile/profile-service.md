# Profile Service

## Overview

The profile service manages the user-facing account profile while keeping authentication credentials in the identity service. It supports reading and updating display details, locale preferences, notification choices, and an optional avatar reference.

## Profile Operations

- Return only fields visible to the authenticated user or permitted administrator.
- Validate display names, locale values, time zones, and avatar metadata.
- Apply partial updates with optimistic version checks.
- Emit an audit event after each successful profile change.
- Remove or replace avatar references through an asynchronous media workflow.

Updates require an authenticated session and a current profile version. A stale version returns `409 Conflict` so clients can refresh before retrying. The service redacts private fields from logs and does not accept passwords, tokens, or payment details.

## Data and Operations

Profile records are encrypted at rest and retained for the lifetime of the account. Deactivation marks a profile inactive immediately, while a scheduled cleanup removes personal data according to the retention policy. Metrics cover update latency, validation failures, avatar processing errors, and cleanup backlog.

