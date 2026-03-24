---
name: api
description: >
  Use this skill whenever the user wants to work with the Loops API — this
  includes sending events, managing contacts, sending transactional emails,
  working with mailing lists, or building any integration with Loops.
  Trigger on phrases like "send a Loops event", "add a contact to Loops",
  "send a transactional email", "Loops API", "Loops SDK", or any time the
  user wants to programmatically interact with their Loops account.
  Also trigger when the user asks how to integrate Loops into their app,
  automate their email workflows, or sync contacts with Loops from external systems.
metadata:
  version: 1.0.0
---

# Loops API Skill

This skill helps you use the Loops REST API to manage contacts, send events, and dispatch transactional emails from your own code.

## When to Use

Use this skill when the user needs to integrate Loops into an app, backend, automation, or webhook flow. Reach for it when the task involves contacts, mailing lists, transactional emails, event-triggered email automation, or SDK/API implementation details.

## Working Style

When this skill is active:

- Prefer an official Loops SDK when the user's language or framework has one.
- Keep all Loops API calls server-side.
- Verify exact request and response shapes against the official docs or OpenAPI spec when details matter.
- Use idempotency keys for retry-prone event and transactional email flows.

Official references:

- Docs: `https://loops.so/docs`
- API reference: `https://loops.so/docs/api-reference/intro`
- OpenAPI spec: `https://app.loops.so/openapi.json`

## Authentication

Every request needs your Loops API key as a Bearer token.

Generate one at: **Settings → API** in your Loops account.

```
Authorization: Bearer YOUR_API_KEY
```

**Security rule**: Never hardcode API keys in source code or expose them client-side. Always load from environment variables or a secrets manager. The Loops API requires server-side requests — browser-side calls will hit CORS errors by design.

```bash
# Store it as an environment variable
export LOOPS_API_KEY="your-api-key-here"
```

## Base URL

```
https://app.loops.so/api
```

## Rate Limits

**10 requests per second per team.** Responses include `x-ratelimit-limit` and `x-ratelimit-remaining` headers. On limit, you'll get HTTP 429 — implement exponential backoff retries.

## SDKs

Loops has official SDKs that handle auth, retries, and TypeScript types for you:

- **JavaScript/TypeScript**: `npm install loops` — works in Node.js and edge runtimes
- **Nuxt**: `@nuxtjs/loops`
- **PHP**: `loops-php`
- **Ruby**: `loops-ruby`

When the user is working in one of these languages, prefer the SDK over raw HTTP requests.

There is currently no cli for Loops but it is in development.

---

## Endpoints

### Test API key

```
GET /v1/api-key
```

Returns `{ success: true, teamName: "..." }` on success. Use this to confirm credentials are working.

---

### Contacts

#### Create a contact

```
POST /v1/contacts/create
```

```json
{
  "email": "user@example.com", // required
  "firstName": "Alex",
  "lastName": "Chen",
  "subscribed": true,
  "userGroup": "premium",
  "userId": "usr_123",
  "mailingLists": { "cm_abc123": true },
  "customProperty": "value" // any custom contact property
}
```

Returns `{ success: true, id: "contact_id" }`.
Returns `409` if email or userId already exists — use `PUT /v1/contacts/update` instead.

#### Update a contact

```
PUT /v1/contacts/update
```

Same body shape as create. Must include `email` or `userId` to identify the contact. Works as an upsert — if the contact doesn't exist, it will be created.

#### Find a contact

```
GET /v1/contacts/find?email=user%40example.com
GET /v1/contacts/find?userId=usr_123
```

Only one parameter allowed. Email must be URI-encoded. Returns an array:

```json
[
  {
    "id": "...",
    "email": "user@example.com",
    "firstName": "Alex",
    "lastName": "Chen",
    "source": "api",
    "subscribed": true,
    "userGroup": "premium",
    "userId": "usr_123",
    "mailingLists": { "cm_abc123": true },
    "optInStatus": "accepted"
  }
]
```

#### Delete a contact

```
POST /v1/contacts/delete
```

```json
{
  "email": "user@example.com"
}
```

Provide exactly one of `email` or `userId` (not both).

---

### Contact Properties

#### List properties

```
GET /v1/contacts/properties?list=all     // "all" (default) or "custom"
```

Returns `[{ key, label, type }]`.

#### Create a property

```
POST /v1/contacts/properties
```

```json
{
  "name": "plan_tier", // required
  "type": "string" // required: "string", "number", "boolean", or "date"
}
```

Custom properties must exist before you can set them on a contact. Create them once in the dashboard or via this endpoint.

---

### Mailing Lists

#### List mailing lists

```
GET /v1/lists
```

Returns `[{ id, name, description, isPublic }]`. Use the `id` values in `mailingLists` objects.

---

### Dedicated Sending IPs

#### List dedicated sending IP addresses

```
GET /v1/dedicated-sending-ips
```

Returns a list of IP address strings, for example:

```json
["1.2.3.4", "5.6.7.8"]
```

---

### Events

#### Send an event

```
POST /v1/events/send
```

Events trigger email automations you've built in Loops. The event name must match exactly what you've configured in the Loops dashboard.

```json
{
  "email": "user@example.com", // either email or userId is required to identify contact
  "userId": "usr_123",
  "eventName": "signup", // required — must match event name in Loops exactly
  "eventProperties": {
    "plan": "pro",
    "trialDays": 14
  },
  "mailingLists": { "list_123": true },
  "firstName": "Alex" // top-level fields update the contact record permanently
}
```

**eventProperties vs contact properties**: Fields inside `eventProperties` are scoped to this specific event trigger (great for one-off data like invoice amounts). Top-level fields like `firstName` update the contact's record permanently.

To avoid duplicate sends on retries, pass an idempotency key:

```
Idempotency-Key: unique-id-max-100-chars
```

Returns `409` if the same key was used before.

---

### Transactional Emails

#### List transactional emails

```
GET /v1/transactional?perPage=20&cursor=...
```

Paginated list of published transactional emails. `perPage` must be between 10 and 50 (default 20). Use this to find `transactionalId` values.

```json
{
  "pagination": {
    "totalResults": 42,
    "returnedResults": 20,
    "perPage": 20,
    "totalPages": 3,
    "nextCursor": "abc...",
    "nextPage": "https://..."
  },
  "data": [
    {
      "id": "cll42l54f20i1la0lfooe3z12",
      "name": "Welcome email",
      "lastUpdated": "...",
      "dataVariables": ["firstName", "trialEnd"]
    }
  ]
}
```

#### Send a transactional email

```
POST /v1/transactional
```

```json
{
  "email": "user@example.com", // required
  "transactionalId": "cll42l54f20i1la0lfooe3z12", // required — use the exact id from Loops dashboard or GET /v1/transactional
  "addToAudience": true, // optionally create/update contact in your audience
  "dataVariables": {
    "firstName": "Alex",
    "resetLink": "https://..." // key must match variable name in your template
  },
  "attachments": [
    {
      "filename": "invoice.pdf",
      "contentType": "application/pdf",
      "data": "<base64-encoded-content>"
    }
  ]
}
```

Attachments must be enabled on your account before use. Contact `help@loops.so` to enable them.

Supports `Idempotency-Key` header the same way as events — useful for confirmation emails that could be triggered more than once.

---

## Code Examples

### JavaScript SDK (recommended for Node.js)

```typescript
import { LoopsClient } from "loops";

const loops = new LoopsClient(process.env.LOOPS_API_KEY);

// Send an event (e.g. after Stripe checkout completes)
await loops.sendEvent({
  email: "user@example.com",
  eventName: "paidSubscription",
  eventProperties: { planName: "pro" },
});

// Create a contact
await loops.createContact("user@example.com", {
  firstName: "Alex",
  userGroup: "premium",
  mailingLists: { list_123: true },
});

// Send a transactional email (e.g. password reset)
await loops.sendTransactionalEmail({
  transactionalId: "cll42l54f20i1la0lfooe3z12",
  email: "user@example.com",
  dataVariables: { resetLink: "https://yourapp.com/reset?token=abc" },
});
```

### Next.js — sending a signup event (App Router)

```typescript
// app/api/register/route.ts
import { LoopsClient } from "loops";
import { NextResponse } from "next/server";

const loops = new LoopsClient(process.env.LOOPS_API_KEY!);

export async function POST(req: Request) {
  const { email, plan } = await req.json();

  // ... create user in your database ...

  await loops.sendEvent({
    email,
    eventName: "signup",
    eventProperties: { plan },
  });

  return NextResponse.json({ success: true });
}
```

All Loops API calls must be server-side — the SDK blocks client-side use, and browser calls will fail with CORS errors.

### Stripe webhook — firing a Loops event on checkout

```typescript
// pages/api/webhooks/stripe.ts (or app/api/webhooks/stripe/route.ts)
import Stripe from "stripe";
import { LoopsClient } from "loops";

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);
const loops = new LoopsClient(process.env.LOOPS_API_KEY!);

export async function POST(req: Request) {
  const sig = req.headers.get("stripe-signature")!;
  const body = await req.text();
  const event = stripe.webhooks.constructEvent(
    body,
    sig,
    process.env.STRIPE_WEBHOOK_SECRET!
  );

  if (event.type === "checkout.session.completed") {
    const session = event.data.object;
    const customerEmail = session.customer_details?.email;
    const planName = session.metadata?.planName; // set this when creating the checkout session

    if (customerEmail) {
      await loops.sendEvent({
        email: customerEmail,
        eventName: "paidSubscription",
        eventProperties: { planName },
      });
    }
  }

  return new Response("ok");
}
```

### Python (no official SDK — use requests)

```python
import os
import requests

LOOPS_API_KEY = os.environ["LOOPS_API_KEY"]
BASE_URL = "https://app.loops.so/api"
headers = {
    "Authorization": f"Bearer {LOOPS_API_KEY}",
    "Content-Type": "application/json",
}

# Send a transactional email (e.g. password reset)
resp = requests.post(f"{BASE_URL}/v1/transactional", headers=headers, json={
    "email": "user@example.com",
    "transactionalId": "cll42l54f20i1la0lfooe3z12",   # from your Loops dashboard or GET /v1/transactional
    "dataVariables": {
        "resetLink": "https://yourapp.com/reset?token=abc123"
    },
})
resp.raise_for_status()
```

### curl

```bash
# Send an event
curl -X POST https://app.loops.so/api/v1/events/send \
  -H "Authorization: Bearer $LOOPS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","eventName":"signup","eventProperties":{"plan":"pro"}}'

# Create a contact
curl -X POST https://app.loops.so/api/v1/contacts/create \
  -H "Authorization: Bearer $LOOPS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","firstName":"Alex","subscribed":true,"mailingLists":{"cm_abc123":true}}'
```

---

## Common Errors

| Status     | Meaning             | Fix                                                                               |
| ---------- | ------------------- | --------------------------------------------------------------------------------- |
| 401        | Invalid API key     | Check the key is correct and hasn't been revoked                                  |
| 400        | Bad request         | Check required fields and value types                                             |
| 404        | Not found           | Contact or transactional email ID doesn't exist                                   |
| 409        | Conflict            | Email/userId already exists (use PUT to update), or idempotency key reused        |
| 429        | Rate limited        | Back off and retry — typical rate is max 10 req/s (some teams have custom limits) |
| CORS error | Client-side request | Move the API call to your server                                                  |

Request body string values are limited to **500 characters**.

---

## Tips

- **Upsert pattern**: Use `PUT /v1/contacts/update` when you're not sure if a contact exists — it creates or updates.
- **`addToAudience` on transactional**: Setting this to `true` when sending a transactional email will make sure the recipient is added to the audience for markting emails.
- **Finding your `transactionalId`**: Go to your Loops dashboard → Transactional, or call `GET /v1/transactional`, and use the exact `id` returned.
- **Mailing list membership**: Pass `{ "list_id": true }` to subscribe, `{ "list_id": false }` to unsubscribe. Use the exact list IDs returned by `GET /v1/lists`.
- **Event name must match exactly**: The `eventName` you send must match the trigger you configured in your Loops automation — including case.
- **Idempotency keys**: Use these any time an operation could be retried (e.g. webhook handlers, email confirmation links). Pass a stable ID like `confirmation-${userId}` as the `Idempotency-Key` header.
