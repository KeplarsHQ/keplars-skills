---
name: keplars
description: Send transactional and marketing emails via Keplars priority queue API. Covers endpoint selection by priority, request body shapes, template usage, scheduling, webhooks, and error handling.
version: 1.0.0
author: Keplars
license: MIT
tags: [email, api, transactional, smtp, webhooks]
---

# Keplars

Keplars is a priority-queue email API that routes messages through instant, high, normal, bulk, and scheduled tiers — each with different delivery SLAs.

**Base URL**: `https://api.keplars.com/api/v1`
**Auth**: `Authorization: Bearer $KEPLARS_API_KEY`
**Env var**: `KEPLARS_API_KEY`

---

## Reference

- [Endpoint selection by priority](endpoints/priority-queue.md)
- [Raw email request body](email/raw-email.md)
- [Template email request body](email/templates.md)
- [Scheduling](scheduling/scheduling.md)
- [Webhooks](webhooks/webhooks.md)
- [Response format & error codes](errors/responses.md)
- [Common mistakes](errors/common-mistakes.md)
- [Migrate from Resend](guides/migrate-from-resend.md)
- [Migrate from Mailgun](guides/migrate-from-mailgun.md)
- [Migrate from Postmark](guides/migrate-from-postmark.md)
