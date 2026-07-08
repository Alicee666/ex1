# Search Service

## Overview

The search service provides relevance-ranked discovery across BranchGuardian resources. It accepts a query, optional filters, pagination, and a sort preference, then returns matching resources with highlights and stable cursors for subsequent pages.

## Query Behavior

- Trim surrounding whitespace and reject empty queries.
- Tokenize terms while preserving quoted phrases.
- Apply tenant and visibility filters before ranking results.
- Rank exact names ahead of descriptions, with recency as a tie breaker.
- Return a cursor rather than an offset for deep pagination.
- Include a correlation ID in every response for support diagnostics.

The API returns `200 OK` with an empty result list when no resources match. Malformed filters return `400 Bad Request`, and an unavailable index returns `503 Service Unavailable` so callers can retry safely. Search logs record query metadata and latency but never store raw secrets or authorization headers.

## Indexing and Operations

Resource changes are published to an indexing queue. Workers process events idempotently and periodically reconcile the index against the source database. Dashboards track query latency, zero-result rate, queue lag, and index freshness.

