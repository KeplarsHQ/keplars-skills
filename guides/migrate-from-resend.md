# Migrate from Resend to Keplars

Migration is a two-step change: rename one field and swap the endpoint URL. Everything else (`from`, `subject`, `cc`, `bcc`, `reply_to`) is identical.

## Field Mapping

| Resend | Keplars | Notes |
|---|---|---|
| `html` or `text` | `body` | Merge into one field |
| `to` | `to` | Keplars expects `string[]`, not `string \| string[]` |
| `from` | `from` | Identical |
| `subject` | `subject` | Identical |
| `cc` | `cc` | Identical |
| `bcc` | `bcc` | Identical |
| `reply_to` | `reply_to` | Identical |
| `react` | `template_id` + `params` | See templates section below |

## Endpoint Mapping

Resend has one endpoint. Keplars has five — pick based on urgency:

| Use case | Resend | Keplars |
|---|---|---|
| OTP, 2FA, password reset | `POST /emails` | `POST /send-email/instant` |
| Security alerts | `POST /emails` | `POST /send-email/high` |
| Welcome, receipts, confirmations | `POST /emails` | `POST /send-email/async` |
| Newsletters, marketing | `POST /emails` | `POST /send-email/bulk` |
| Future-dated delivery | `POST /emails` | `POST /send-email/schedule` |

## Before & After

**Resend:**
```js
await resend.emails.send({
  from: "Acme <orders@acme.com>",
  to: ["user@example.com"],
  subject: "Your order is confirmed",
  html: "<h1>Thanks for your order!</h1>",
  cc: ["manager@acme.com"],
  reply_to: "support@acme.com",
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
    cc: ["manager@acme.com"],
    reply_to: "support@acme.com",
  }),
});
```

## Auth

Replace your Resend API key with your Keplars API key. The header format is the same:

```
Authorization: Bearer $KEPLARS_API_KEY
```

## Templates

Resend uses React components as templates. Keplars uses saved templates with Handlebars variables.

1. Create your template in the Keplars dashboard (or import your HTML)
2. Replace `{{variable}}` placeholders where needed
3. Send using `template_id` + `params` instead of a `react` field

**Resend:**
```js
await resend.emails.send({
  from: "orders@acme.com",
  to: ["user@example.com"],
  subject: "Welcome",
  react: <WelcomeEmail firstName="Alice" />,
});
```

**Keplars:**
```js
body: JSON.stringify({
  from: "orders@acme.com",
  to: ["user@example.com"],
  template_id: "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  params: { firstName: "Alice" },
})
```

## Webhooks

If you're using Resend webhooks, update your handler:

| Resend event | Keplars event |
|---|---|
| `email.sent` | `email.sent` |
| `email.delivered` | `email.delivered` |
| `email.opened` | `email.opened` |
| `email.clicked` | `email.clicked` |
| `email.bounced` | `email.bounced` |

Signature verification moves from Resend's `svix` library to Keplars' HMAC-SHA256 — see [webhooks](../webhooks/webhooks.md).
