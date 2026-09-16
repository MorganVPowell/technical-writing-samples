# Webhooks

Sample API documentation created to demonstrate technical writing skills — not tied to a real company or live API.

## Overview
Webhooks let your application receive real-time notifications when events happen in MuppetWorld, instead of repeatedly polling the API to check for changes. When a subscribed event occurs, MuppetWorld sends an HTTP POST request to a URL you configure, containing details about the event.

Use webhooks to keep your system in sync with order status changes, payment confirmations, and shipping updates without constantly querying the API.

---

## How Webhooks Work

1. You create a webhook endpoint on your server that can receive POST requests.
2. You register that endpoint's URL in your MuppetWorld dashboard, along with the event types you want to receive.
3. When a subscribed event occurs, MuppetWorld sends a POST request to your endpoint containing a JSON payload describing the event.
4. Your endpoint responds with a `200 OK` status to confirm receipt. If MuppetWorld doesn't receive a `200` response, it will retry delivery.

---

## Registering a Webhook Endpoint

1. Go to **Settings > Webhooks** in your dashboard.
2. Click **Add Endpoint** and enter the URL that will receive event notifications.
3. Select the event types you want to subscribe to (see [Event Types](#event-types) below).
4. Save the endpoint. MuppetWorld will send a test event immediately so you can confirm the connection works.

---

## Event Types

| Event | Description |
|---|---|
| `order.created` | Sent when a new order is successfully created |
| `order.updated` | Sent when an existing order's status or details change |
| `order.cancelled` | Sent when an order is cancelled |
| `payment.succeeded` | Sent when a payment is successfully processed |
| `payment.failed` | Sent when a payment attempt fails |

---

## Payload Structure

Every webhook payload follows the same envelope structure, with event-specific data inside the `data` field.

```json
{
  "id": "evt_7a2c9f1b",
  "type": "order.created",
  "created_at": "2026-09-14T18:22:03Z",
  "data": {
    "id": "order_5d3e2f1a",
    "status": "pending",
    "customer_id": "cust_9f8a7b6c",
    "total_amount": 2998,
    "currency": "USD"
  }
}
```

| Field | Type | Description |
|---|---|---|
| `id` | string | Unique identifier for this webhook event (useful for deduplication) |
| `type` | string | The event type, matching one of the values in [Event Types](#event-types) |
| `created_at` | string | ISO 8601 timestamp of when the event occurred |
| `data` | object | The event-specific payload — structure varies by event type |

---

## Verifying Webhook Signatures

To confirm that a webhook request genuinely came from MuppetWorld and wasn't spoofed, every request includes a signature header:

```
MuppetWorld-Signature: t=1757890923,v1=5257a869e7ecebeda32affa62cdca3fa51cad7e77a0e56ff536d0ce8e108d8bd
```

To verify the signature:
1. Extract the timestamp (`t`) and signature (`v1`) from the header.
2. Concatenate the timestamp and the raw request body, separated by a period.
3. Compute an HMAC-SHA256 hash of that string using your webhook signing secret (found in **Settings > Webhooks**).
4. Compare your computed hash to the `v1` value. If they match, the request is authentic.

Always verify signatures before processing a webhook. Skipping this step allows anyone who discovers your endpoint URL to send fake events.

---

## Retry Behavior

If your endpoint doesn't return a `200` status, MuppetWorld will retry delivery using an exponential backoff schedule:

| Attempt | Delay After Previous Attempt |
|---|---|
| 1 | Immediate |
| 2 | 5 minutes |
| 3 | 30 minutes |
| 4 | 2 hours |
| 5 (final) | 12 hours |

After the final attempt fails, the event is marked as `failed` and is viewable in your dashboard's webhook logs, but will not be retried again automatically.

---

## Best Practices

- **Respond quickly.** Return a `200` as soon as you've received the payload, and process the event asynchronously. Slow responses can cause MuppetWorld to treat the delivery as failed and trigger an unnecessary retry.
- **Handle duplicate events.** Use the event's `id` field to detect and ignore duplicates, since retries or network issues can occasionally cause the same event to be delivered more than once.
- **Verify signatures on every request.** Never process a webhook payload without confirming its signature first.

---
*Sample documentation written to demonstrate API technical writing: event-driven/async concepts, security (signature verification), and retry-logic documentation.*
