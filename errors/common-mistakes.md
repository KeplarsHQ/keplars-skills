# Common Mistakes

The most frequent errors when integrating Keplars without this skill.

## 1. Using `/instant` for all emails

`/instant` is reserved for OTP, 2FA, and password resets - emails where the user is actively waiting with the app open. Routing all emails through it degrades delivery SLA for everyone.

Use `/async` as the default. Only escalate priority when real-time delivery genuinely matters.

## 2. Including `subject` and `body` with `template_id`

Template emails must omit both fields. The template owns the subject and body:

```json
// Wrong - returns 400
{
  "template_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "subject": "Welcome!",
  "body": "<p>Hi there</p>"
}

// Correct
{
  "template_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "params": { "firstName": "Alice" }
}
```

## 3. Omitting `from` when using a custom domain

If your workspace has a verified custom domain or SMTP credential configured, the `from` field is required and must match a verified sender on that domain. Omitting it returns a 400 validation error.

## 4. Scheduling in the past or beyond 365 days

`scheduled_at` must be at least 1 minute in the future and no more than 365 days ahead. Submitting a past time returns a 400 error immediately - the email is not queued.

Always validate scheduling times server-side before calling the API, especially if user input is involved.
