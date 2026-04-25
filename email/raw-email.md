# Raw Email - Request Body

Use this shape when sending email with an inline subject and body (not a template).

```json
{
  "to": "user@example.com",
  "subject": "Your order is confirmed",
  "body": "<h1>Thanks for your order!</h1>",
  "from": "orders@yourdomain.com",
  "from_name": "Acme Store",
  "reply_to": "support@yourdomain.com",
  "cc": ["manager@yourdomain.com"],
  "bcc": ["archive@yourdomain.com"]
}
```

## Fields

| Field | Type | Required | Notes |
|---|---|---|---|
| `to` | string | yes | Recipient email address |
| `subject` | string | yes | Email subject line |
| `body` | string | yes | HTML or plain text body |
| `from` | string | conditional | Required when using a custom domain or SMTP credential |
| `from_name` | string | no | Display name for sender |
| `reply_to` | string | no | Reply-to address |
| `cc` | string[] | no | CC recipients |
| `bcc` | string[] | no | BCC recipients |

## When `from` is required

`from` becomes required if your workspace has either:
- A **verified custom domain** configured (e.g. `mail.yourdomain.com`)
- An **SMTP credential** set up

In both cases, `from` must match a verified sender address on that domain.
