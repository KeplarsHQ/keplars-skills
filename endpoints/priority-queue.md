# Priority Queue - Endpoint Selection

Pick the endpoint that matches the real-time urgency of the email. Do not use `/instant` for everything.

| Endpoint | Use Case | Delivery |
|---|---|---|
| `POST /send-email/instant` | OTP, 2FA, password reset | 0–5 sec |
| `POST /send-email/high` | Account alerts, security notifications | 0–30 sec |
| `POST /send-email/async` | Welcome emails, receipts, confirmations | 0–5 min |
| `POST /send-email/bulk` | Newsletters, marketing, announcements | Idle/background |
| `POST /send-email/schedule` | Any future-dated delivery | Scheduled time |

**Default to `/async`.** Only escalate when real-time delivery genuinely matters to the user experience.

## Decision Guide

```
Is the user waiting on this email to proceed?
├── Yes → Is it a one-time code (OTP/2FA)?
│         ├── Yes → /instant
│         └── No  → /high
└── No  → Is it scheduled for a future time?
          ├── Yes → /schedule
          └── No  → Is it a bulk/marketing send?
                    ├── Yes → /bulk
                    └── No  → /async  ← default
```
