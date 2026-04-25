# Keplars Skills

Context file for AI coding agents (Claude Code, Cursor, Windsurf, Codex, Antigravity) that teaches them the Keplars API - endpoint selection, request shapes, template usage, scheduling, webhooks, and error handling.

Agents load this skill and generate correct Keplars integration code on the first try, without hallucinating endpoint paths or wrong field names.

## Install

```bash
npx skills add KeplarsHQ/keplars-skills
```

This installs the skill into your agent's config directory automatically:

| Agent | Config location |
|---|---|
| Claude Code | `.claude/CLAUDE.md` |
| Cursor | `.cursor/rules/keplers.mdc` |
| Windsurf | `.windsurf/rules/keplers.md` |
| Codex | `.codex/instructions.md` |

## What the skill teaches

- **Priority queue selection** - which of the 5 endpoints to use (`/instant`, `/high`, `/async`, `/bulk`, `/schedule`) and when
- **Raw email body** - all fields, types, required vs optional, and when `from` becomes required
- **Template emails** - `template_id` + `params` shape, Handlebars variable syntax, what NOT to include
- **Scheduling** - date format, timezone (IANA), constraints, cancel and list endpoints
- **Response shapes** - success and error JSON with all error codes
- **Webhooks** - full event list, payload shape, HMAC-SHA256 signature verification
- **Common mistakes** - the 4 things agents most often get wrong without this skill

## Usage

After installing, ask your agent:

> "Add email sending to my signup flow with Keplars"

> "Send a password reset email using the Keplars API"

> "Set up a Keplars webhook to track email opens and bounces"

The agent reads the skill and generates working code using the correct endpoints, field names, and patterns.

## Security

This skill contains only what is already on [docs.keplars.com](https://docs.keplars.com):
- Public API endpoint paths
- Request body field names and types
- Response shapes
- Webhook event names

No API keys, no secrets, no internal architecture. You supply your own `KEPLARS_API_KEY` from your environment.

## Links

- [Keplars](https://keplars.com)
- [Keplars Dashboard](https://dash.keplars.com)
- [API Documentation](https://docs.keplars.com)
- [skills.sh marketplace](https://skills.sh)

## License

MIT
