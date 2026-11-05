# Login Service

## Overview

The login service authenticates registered users and establishes a short-lived application session. It accepts an email address and password, verifies the password against the stored password hash, and returns an opaque session identifier through a secure, HTTP-only cookie.

## Authentication Flow

1. Normalize the submitted email address and validate the request body.
2. Look up the account without revealing whether the email is registered.
3. Compare the supplied password with the stored Argon2id hash.
4. Apply rate limits after repeated failures by account and client address.
5. Create a session with a configurable idle timeout and absolute lifetime.
6. Emit an audit event containing the account ID, result, and request correlation ID.

Successful requests return `204 No Content`. Invalid credentials return the same `401 Unauthorized` response for unknown users and incorrect passwords. The service never logs passwords, password hashes, session tokens, or recovery secrets.

## Operational Notes

- Require TLS for every external request.
- Set `Secure`, `HttpOnly`, and `SameSite=Lax` on the session cookie.
- Rotate session identifiers after a successful login.
- Expose health checks without exposing authentication data.
- Alert on elevated failure rates and repeated attempts against many accounts.

