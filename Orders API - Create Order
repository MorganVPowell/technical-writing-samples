# Orders API — Create Order

Sample API documentation created to demonstrate technical writing skills — not tied to a real company or live API.
## Overview
The `Create Order` endpoint lets you programmatically submit a new order on behalf of a customer. Use this endpoint when a customer completes checkout in your application and you need to record the order in our system for fulfillment and billing.

---

## Endpoint

```
POST https://MuppetWorld.com/v1/orders
```

## Authentication
This endpoint requires a Bearer token in the `Authorization` header. See [Authentication](#) for how to generate an API key.

```
Authorization: Bearer YOUR_API_KEY
```

## Request Headers

| Header | Required | Description |
|---|---|---|
| `Authorization` | Yes | Your API key, prefixed with `Bearer` |
| `Content-Type` | Yes | Must be `application/json` |
| `Idempotency-Key` | Recommended | A unique string to safely retry requests without creating duplicate orders |

## Request Body

| Field | Type | Required | Description |
|---|---|---|---|
| `customer_id` | string | Yes | Unique identifier of the customer placing the order |
| `items` | array | Yes | List of items in the order. See [Item object](#item-object) |
| `shipping_address` | object | Yes | Destination address for the order |
| `currency` | string | No | ISO 4217 currency code. Defaults to `USD` |
| `notes` | string | No | Internal notes attached to the order (not visible to customer) |

### Item object

| Field | Type | Required | Description |
|---|---|---|---|
| `sku` | string | Yes | Product SKU |
| `quantity` | integer | Yes | Number of units, must be ≥ 1 |
| `unit_price` | number | Yes | Price per unit, in the smallest currency unit (e.g., cents) |

### Example Request

```json
POST https://MuppetWorld.com/v1/orders
Content-Type: application/json
Authorization: Bearer sk_live_abc123
Idempotency-Key: 8f14e45f-ceea-4d5a-9b1e-3f2c1a7e9b21

{
  "customer_id": "cust_9f8a7b6c",
  "items": [
    {
      "sku": "SKU-1001",
      "quantity": 2,
      "unit_price": 1499
    }
  ],
  "shipping_address": {
    "line1": "123 Main St",
    "city": "Zzyzx",
    "state": "C",
    "postal_code": "92309",
    "country": "US"
  },
  "currency": "USD"
}
```

### Example Response — 201 Created

```json
{
  "id": "order_5d3e2f1a",
  "status": "pending",
  "customer_id": "cust_9f8a7b6c",
  "total_amount": 2998,
  "currency": "USD",
  "created_at": "2026-09-14T18:22:03Z"
}
```

## Errors

| Status Code | Meaning | Common Cause |
|---|---|---|
| `400 Bad Request` | Malformed request | Missing required field, invalid data type |
| `401 Unauthorized` | Authentication failed | Missing or invalid API key |
| `404 Not Found` | Customer not found | `customer_id` does not exist |
| `409 Conflict` | Duplicate request | `Idempotency-Key` reused with different payload |
| `422 Unprocessable Entity` | Business logic error | e.g., item out of stock |
| `500 Internal Server Error` | Server-side failure | Retry with exponential backoff |

### Example Error Response

```json
{
  "error": {
    "code": "customer_not_found",
    "message": "No customer exists with id 'cust_9f8a7b6c'."
  }
}
```

## Notes
- Orders created through this endpoint are placed in `pending` status until payment is confirmed via the [Payments API](#).
- We recommend always sending an `Idempotency-Key` to prevent duplicate orders in case of network retries.
- Rate limit: 100 requests per minute per API key. Exceeding this returns a `429 Too Many Requests` response.

---
*Sample documentation written to demonstrate API technical writing: endpoint reference structure, request/response examples, and error handling documentation.*
