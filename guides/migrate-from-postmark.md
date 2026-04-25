# Migrate from Postmark to Keplars

The main differences: Postmark uses a custom auth header and PascalCase field names. Keplars uses standard Bearer auth and lowercase fields.

## Field Mapping

| Postmark | Keplars | Notes |
|---|---|---|
| `From` | `from` + `from_name` | Split display name out if needed |
| `To` | `to` | Keplars expects `string[]` |
| `Subject` | `subject` | Lowercase |
| `HtmlBody` | `body` | Renamed and lowercased |
| `TextBody` | `body` | Use `HtmlBody` or `TextBody`, not both |
| `Cc` | `cc` | Lowercase |
| `Bcc` | `bcc` | Lowercase |
| `ReplyTo` | `reply_to` | Snake case |
| `MessageStream` | endpoint choice | Use priority endpoint instead |
| `Tag` | `metadata` | Use `metadata` object for custom data |
| `TemplateId` | `template_id` | UUID format in Keplars |
| `TemplateModel` | `params` | Renamed |
| `TrackOpens` | Built-in | Open tracking is automatic |
| `TrackLinks` | Built-in | Click tracking is automatic |

## Auth

Postmark uses a custom server token header:
```
X-Postmark-Server-Token: YOUR_SERVER_TOKEN
```

Keplars uses standard Bearer auth:
```
Authorization: Bearer $KEPLARS_API_KEY
```

## Before & After

**Postmark:**
```js
await fetch("https://api.postmarkapp.com/email", {
  method: "POST",
  headers: {
    "Accept": "application/json",
    "Content-Type": "application/json",
    "X-Postmark-Server-Token": process.env.POSTMARK_SERVER_TOKEN,
  },
  body: JSON.stringify({
    From: "Acme <orders@acme.com>",
    To: "user@example.com",
    Subject: "Your order is confirmed",
    HtmlBody: "<h1>Thanks for your order!</h1>",
    ReplyTo: "support@acme.com",
    MessageStream: "outbound",
  }),
});
```

**Keplars:**
```js
await fetch("https://api.keplars.com/api/v1/send-email/async", {
  method: "POST",
  headers: {
    "Authorization": `Bearer ${process.env.KEPLARS_API_KEY}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    from: "orders@acme.com",
    from_name: "Acme",
    to: ["user@example.com"],
    subject: "Your order is confirmed",
    body: "<h1>Thanks for your order!</h1>",
    reply_to: "support@acme.com",
  }),
});
```

## MessageStream → Priority Endpoint

Postmark uses `MessageStream` to separate transactional from broadcast email. Keplars uses priority endpoints instead:

| Postmark MessageStream | Keplars endpoint |
|---|---|
| `outbound` (transactional) | `POST /send-email/async` |
| `broadcast` (marketing) | `POST /send-email/bulk` |

And within transactional, escalate further by urgency:

| Use case | Keplars endpoint |
|---|---|
| OTP, 2FA, password reset | `POST /send-email/instant` |
| Security alerts | `POST /send-email/high` |
| Receipts, confirmations, welcome | `POST /send-email/async` |

## Templates

**Postmark:**
```js
body: JSON.stringify({
  From: "orders@acme.com",
  To: "user@example.com",
  TemplateId: 12345,
  TemplateModel: { firstName: "Alice", confirmationUrl: "https://..." },
  MessageStream: "outbound",
})
```

**Keplars:**
```js
body: JSON.stringify({
  from: "orders@acme.com",
  to: ["user@example.com"],
  template_id: "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  params: { firstName: "Alice", confirmationUrl: "https://..." },
})
```

Note: Postmark template IDs are integers. Keplars template IDs are UUIDs — copy them from the Keplars dashboard.

## Webhooks

| Postmark event | Keplars event |
|---|---|
| `Delivery` | `email.delivered` |
| `Open` | `email.opened` |
| `Click` | `email.clicked` |
| `Bounce` (Hard) | `email.bounced` |
| `Bounce` (Soft) | `email.failed` |
| `SpamComplaint` | `email.bounced` |

Postmark does not sign webhooks by default. Keplars signs all webhook payloads with HMAC-SHA256 via `X-Keplars-Signature`. See [webhooks](../webhooks/webhooks.md).
