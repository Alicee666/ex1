# Payment Service

## Overview

The payment service coordinates secure payment authorization for orders. It creates an idempotent payment attempt, sends the request to the configured provider, and records the provider reference and final state without storing raw card data.

## Processing Flow

1. Validate the order amount, currency, and customer account.
2. Require an idempotency key for every payment attempt.
3. Create a pending payment record before calling the provider.
4. Authorize the payment through the provider's tokenized API.
5. Persist the provider reference and transition the payment to authorized, declined, or failed.
6. Publish a payment event for order fulfillment and customer notifications.

Retries reuse the same idempotency key and never create a second charge. Webhook signatures are verified before processing, and duplicate webhook deliveries are ignored. Logs contain payment IDs and provider references, but never card numbers, security codes, or access tokens.

## Reliability

- Use timeouts and bounded retries for provider requests.
- Reconcile pending payments against provider reports.
- Alert on elevated decline rates, webhook lag, and reconciliation mismatches.
- Restrict refunds to authorized operators and record an audit event.

