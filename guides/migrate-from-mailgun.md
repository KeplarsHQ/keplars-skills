# Migrate from Mailgun to Keplars

The main differences: Mailgun uses HTTP Basic auth and form-encoded bodies. Keplars uses Bearer tokens and JSON.

## Field Mapping

| Mailgun | Keplars | Notes |
|---|---|---|
| `from` | `from` + `from_name` | Split display name out if needed |
| `to` | `to` | Keplars expects `string[]` |
| `subject` | `subject` | Identical |
| `html` | `body` | Rename |
| `text` | `body` | Rename (use `html` or `text`, not both) |
| `cc` | `cc` | Identical |
| `bcc` | `bcc` | Identical |
| `h:Reply-To` | `reply_to` | Simplified |
| `o:tag` | `metadata` | Use `metadata` object for custom data |
| `o:tracking-opens` | Built-in | Open tracking is automatic |
| `o:tracking-clicks` | Built-in | Click tracking is automatic |
| `template` | `template_id` + `params` | See templates section below |
| `h:X-Mailgun-Variables` | `params` | Pass template variables directly |

## Auth

Mailgun uses HTTP Basic auth with your API key as the password:
```
Authorization: Basic base64("api:YOUR_MAILGUN_KEY")
```

Keplars uses a standard Bearer token:
```
Authorization: Bearer $KEPLARS_API_KEY
```

## Request Format

Mailgun accepts `multipart/form-data` or `application/x-www-form-urlencoded`. Keplars is always `application/json`.

## Before & After

**Mailgun:**
```js
const form = new FormData();
form.append("from", "Acme <orders@acme.com>");
form.append("to", "user@example.com");
form.append("subject", "Your order is confirmed");
form.append("html", "<h1>Thanks for your order!</h1>");
form.append("h:Reply-To", "support@acme.com");

await fetch(`https://api.mailgun.net/v3/${MAILGUN_DOMAIN}/messages`, {
  method: "POST",
  headers: {
    Authorization: `Basic ${btoa(`api:${MAILGUN_API_KEY}`)}`,
  },
  body: form,
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

## Endpoint Mapping

Mailgun uses one domain-scoped endpoint. Keplars uses priority tiers:

| Use case | Mailgun | Keplars |
|---|---|---|
| OTP, 2FA, password reset | `POST /v3/{domain}/messages` | `POST /send-email/instant` |
| Security alerts | `POST /v3/{domain}/messages` | `POST /send-email/high` |
| Receipts, confirmations | `POST /v3/{domain}/messages` | `POST /send-email/async` |
| Newsletters, marketing | `POST /v3/{domain}/messages` | `POST /send-email/bulk` |
| Scheduled delivery | `POST /v3/{domain}/messages` + `o:deliverytime` | `POST /send-email/schedule` |

## Templates

**Mailgun:**
```js
form.append("template", "welcome-email");
form.append("h:X-Mailgun-Variables", JSON.stringify({ firstName: "Alice" }));
```

**Keplars:**
```js
body: JSON.stringify({
  to: ["user@example.com"],
  template_id: "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  params: { firstName: "Alice" },
})
```

## Webhooks

| Mailgun event | Keplars event |
|---|---|
| `delivered` | `email.delivered` |
| `opened` | `email.opened` |
| `clicked` | `email.clicked` |
| `failed` (permanent) | `email.bounced` |
| `failed` (temporary) | `email.failed` |
| `complained` | `email.bounced` |

Mailgun signs webhooks with HMAC-SHA256 — Keplars does too. Swap the secret and update the header name from `X-Mailgun-Signature-V2` to `X-Keplars-Signature`. See [webhooks](../webhooks/webhooks.md).
