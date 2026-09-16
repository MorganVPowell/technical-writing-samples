# Authentication

Sample API documentation created to demonstrate technical writing skills — not tied to a real company or live API.

## Overview
The MuppetWorld API uses API keys to authenticate requests. Every request to the API must include a valid API key; requests made without one, or with an invalid key, will be rejected.

You can generate and manage your API keys from your account dashboard.

---

## Authentication Method

All requests must include your API key in the `Authorization` header, using the `Bearer` scheme:

```
Authorization: Bearer YOUR_API_KEY
```

Do not send your API key as a query parameter (e.g., `?api_key=...`). Query parameters can be logged in browser history, server logs, and proxies, which exposes your key unnecessarily. Always send it in the header.

### Example Request

```
GET https://MuppetWorld.com/v1/orders/order_5d3e2f1a
Authorization: Bearer sk_live_abc123
```

---

## API Key Types

| Key Type | Prefix | Use Case |
|---|---|---|
| Live key | `sk_live_` | Production requests, real data |
| Test key | `sk_test_` | Sandbox requests, no real data affected |

Use test keys during development and integration testing. Test keys behave identically to live keys but operate against a sandbox environment, so no real orders, charges, or customer records are created.

---

## Generating an API Key

1. Log in to your MuppetWorld dashboard.
2. Navigate to **Settings > API Keys**.
3. Click **Generate New Key**, and select either **Live** or **Test**.
4. Copy your key immediately — for security reasons, the full key is only shown once. If you lose it, you'll need to revoke it and generate a new one.

---

## Keeping Your API Key Secure

- Never expose your API key in client-side code (JavaScript running in a browser, mobile app bundles, etc.). Anyone who can view the code can extract the key.
- Store keys in environment variables or a secrets manager, not directly in your codebase.
- Rotate keys periodically, and immediately if you suspect a key has been exposed.
- Use separate keys for different environments (development, staging, production) so you can revoke one without affecting others.

---

## Authentication Errors

| Status Code | Error Code | Meaning |
|---|---|---|
| `401 Unauthorized` | `missing_api_key` | No `Authorization` header was included in the request |
| `401 Unauthorized` | `invalid_api_key` | The API key provided does not exist or has been revoked |
| `403 Forbidden` | `insufficient_permissions` | The API key is valid but does not have permission to access this resource |

### Example Error Response

```json
{
  "error": {
    "code": "invalid_api_key",
    "message": "The API key provided is not valid. Check your dashboard for your active keys."
  }
}
```

---

## Revoking a Key

If a key is compromised or no longer needed, revoke it immediately from **Settings > API Keys > Revoke**. Revoking a key takes effect immediately — any requests using that key will begin returning `401 Unauthorized` within a few seconds.

---
*Sample documentation written to demonstrate API technical writing: authentication concepts, security best practices, and error handling documentation.*
