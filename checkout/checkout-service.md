# Checkout Service

## Overview

The checkout service turns a customer's cart into an order after validating inventory, delivery details, pricing, and payment readiness. It coordinates the order workflow but delegates payment authorization to the payment service.

## Checkout Flow

1. Load the cart and verify that it belongs to the authenticated customer.
2. Reprice each item and calculate tax, shipping, discounts, and the final total.
3. Reserve inventory with a bounded expiration time.
4. Validate the shipping address and selected delivery option.
5. Create an order in `pending_payment` state using the checkout idempotency key.
6. Request payment authorization and transition the order to `confirmed` or `payment_failed`.
7. Release inventory when the checkout expires or payment fails.

All mutation requests require an idempotency key. Replaying a completed request returns the original order result rather than creating a duplicate order. Price and inventory snapshots are retained with the order so later changes do not alter the customer receipt.

## Reliability and Observability

- Use a saga or compensating actions for failures between inventory and payment.
- Reject stale carts when the calculated total changes materially.
- Publish order lifecycle events only after the transaction is durable.
- Track conversion rate, reservation expiry, payment latency, and failed compensations.

