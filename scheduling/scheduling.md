# Scheduling

Use `POST /send-email/schedule` to deliver an email at a future time. Works with both raw email and template email body shapes - just add the scheduling fields.

```json
{
  "to": "user@example.com",
  "subject": "Your weekly digest",
  "body": "<p>Here's what happened this week...</p>",
  "scheduled_at": "2025-12-25_09:00:00",
  "timezone": "America/New_York"
}
```

## Scheduling Fields

| Field | Type | Required | Notes |
|---|---|---|---|
| `scheduled_at` | string | yes | `YYYY-MM-DD_HH:MM:SS` or ISO 8601 |
| `timezone` | string | yes | IANA timezone string |

**Common timezones**: `America/New_York`, `America/Los_Angeles`, `Europe/London`, `Europe/Berlin`, `Asia/Kolkata`, `Asia/Singapore`, `Australia/Sydney`, `UTC`

## Constraints

- Minimum: **1 minute** in the future
- Maximum: **365 days** in the future
- Times in the past return a 400 error immediately
- Requires **LAUNCH plan** or higher

## Manage Scheduled Emails

**List all scheduled emails for a workspace:**
```
GET /send-email/scheduled
Authorization: Bearer $KEPLARS_API_KEY
```

**Cancel a scheduled email:**
```
DELETE /send-email/schedule/:id
Authorization: Bearer $KEPLARS_API_KEY
```

Cancellation only works before the email has been handed to the queue for delivery.
