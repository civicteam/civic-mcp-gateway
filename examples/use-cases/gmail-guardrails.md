# Gmail — Guardrails & Audit Trails

## The problem

An AI agent with access to Gmail can read, draft, and send email on your behalf. Without guardrails, there's no check on what it sends, to whom, or what it reads. Without an audit trail, you have no record of what it did.

## What Civic adds

- **Audit trail** — every Gmail tool call logged: which tool, exact parameters, timestamp, which agent/session triggered it
- **Request guardrails** — block sends containing sensitive content before they leave
- **Response guardrails** — redact PII from email content before it reaches the agent's context
- **Revocation** — revoke the agent's token and it loses Gmail access instantly, across all clients

## Setup

Add Civic to your MCP client (see [README](../../README.md)), then enable the Gmail server inside [app.civic.com](https://app.civic.com).

**Claude Desktop** (`~/Library/Application Support/Claude/claude_desktop_config.json`):
```json
{
  "mcpServers": {
    "civic": {
      "url": "https://app.civic.com/hub/mcp"
    }
  }
}
```

Civic exposes Gmail tools (`send_gmail_message`, `search_gmail_messages`, `draft_gmail_message`, etc.) through the same connection — no separate Gmail MCP server needed.

## Guardrails to configure

Configure these in [app.civic.com](https://app.civic.com) or by asking your agent:

**Block outbound emails containing secrets:**
> "Add a guardrail to block any `send_gmail_message` call where the body contains 'password', 'secret', or 'api_key'."

**Redact PII from incoming email content:**
> "Enable email address and phone number redaction on all Gmail response outputs."

**Restrict sends to internal domain only:**
> "Block `send_gmail_message` calls where the `to` parameter is not a @yourcompany.com address."

**Prevent bulk operations:**
> "Block any Gmail tool call that operates on more than 10 messages at once."

## Audit queries

Ask the Civic Audit Agent at [app.civic.com](https://app.civic.com):

- *"Show me all `send_gmail_message` calls from the last 30 days"*
- *"What emails did my agent send this week? Include recipients and subjects."*
- *"Show me every Gmail tool call that was blocked by a guardrail."*
- *"Export my Gmail agent activity as a CSV."*

## What gets logged per call

| Field | Example |
|---|---|
| Tool | `send_gmail_message` |
| Parameters | `{to: "user@example.com", subject: "Follow-up", body: "..."}` |
| Timestamp | `2026-03-18T14:23:01Z` |
| Session | `claude-desktop / titus@civic.com` |
| Result | `{messageId: "msg_abc123"}` |
