# Template Email - Request Body

Use this shape when sending via a saved template. Templates are created in the Keplars dashboard or via the Template Engine API.

```json
{
  "to": "user@example.com",
  "template_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "params": {
    "firstName": "Alice",
    "resetLink": "https://app.example.com/reset?token=xyz"
  }
}
```

## Fields

| Field | Type | Required | Notes |
|---|---|---|---|
| `to` | string | yes | Recipient email address |
| `template_id` | UUID | yes | UUID of the template from the Keplars dashboard |
| `params` | object | depends | Key/value pairs for template variables |

> **Do NOT include `subject` or `body` when using `template_id`.** The template owns both. Sending them together returns a 400 validation error.

## Variable Syntax

Templates use Handlebars syntax. Variable names in `params` must match the template exactly (case-sensitive):

```handlebars
Hello, {{firstName}}!
Click here to reset: {{resetLink}}
```

```json
{
  "params": {
    "firstName": "Alice",
    "resetLink": "https://..."
  }
}
```

## Finding Template IDs

Retrieve your templates via:

```
GET /api/v1/templates
Authorization: Bearer $KEPLARS_API_KEY
```

The `id` field on each template object is the value to use as `template_id`.
