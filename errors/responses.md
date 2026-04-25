# Response Format

## Success (2xx)

```json
{
  "success": true,
  "message": "Email queued successfully",
  "email_id": "em_7f3a9b2c1d4e",
  "queue": "async",
  "estimated_delivery": "0-5 minutes"
}
```

## Error (4xx / 5xx)

```json
{
  "success": false,
  "error": "INVALID_TEMPLATE",
  "message": "Template tpl_xyz not found in this workspace"
}
```

## Error Codes

| HTTP Status | Error Code | Meaning |
|---|---|---|
| 401 | `UNAUTHORIZED` | Missing or invalid API key |
| 400 | `VALIDATION_ERROR` | Missing required field or invalid value |
| 400 | `INVALID_TEMPLATE` | `template_id` not found in this workspace |
| 400 | `INVALID_SCHEDULE_TIME` | `scheduled_at` is in the past or exceeds 365 days |
| 429 | `RATE_LIMITED` | Plan email quota reached for this billing period |
| 403 | `PLAN_REQUIRED` | Feature requires LAUNCH plan or higher |
| 500 | `INTERNAL_ERROR` | Unexpected server error - retry with exponential backoff |
