# Webhooks

Configure a webhook URL in the Keplars dashboard. Keplars sends a `POST` request to your URL for every email lifecycle event.

## Payload Shape

```json
{
  "event": "email.delivered",
  "email_id": "em_7f3a9b2c1d4e",
  "workspace_id": "ws_abc123",
  "to": "user@example.com",
  "subject": "Your order is confirmed",
  "timestamp": "2025-06-15T14:23:01Z",
  "metadata": {}
}
```

## Event Types

| Event | Fired when |
|---|---|
| `email.queued` | Email accepted into the priority queue |
| `email.sent` | Handed off to the mail provider |
| `email.delivered` | Delivery confirmed by provider |
| `email.opened` | Recipient opened the email |
| `email.clicked` | Recipient clicked a tracked link |
| `email.bounced` | Hard or soft bounce received |
| `email.failed` | Delivery failed after all retries |
| `email.scheduled` | Email accepted for future delivery |
| `email.cancelled` | Scheduled email was cancelled |

## Signature Verification

Every webhook request includes an `X-Keplars-Signature` header - an HMAC-SHA256 signature of the raw request body, signed with your webhook secret (found in the dashboard, separate from your API key).

**Node.js:**
```js
import crypto from "crypto";

function verifyWebhook(rawBody, signatureHeader, secret) {
  const expected = crypto
    .createHmac("sha256", secret)
    .update(rawBody)
    .digest("hex");
  return crypto.timingSafeEqual(
    Buffer.from(expected),
    Buffer.from(signatureHeader)
  );
}

app.post("/webhook/keplars", express.raw({ type: "application/json" }), (req, res) => {
  const valid = verifyWebhook(
    req.body,
    req.headers["x-keplars-signature"],
    process.env.KEPLARS_WEBHOOK_SECRET
  );
  if (!valid) return res.status(401).send("Invalid signature");

  const event = JSON.parse(req.body);
  res.sendStatus(200);
});
```

> Always use `express.raw()` (not `express.json()`) to preserve the raw body for signature verification.
