# Google Calendar — Guardrails & Audit Trails

## The problem

An AI scheduling agent can create, modify, and delete calendar events on your behalf. Without guardrails, it can book time without limits, invite external parties, or delete existing events. Without an audit trail, you can't reconstruct what was booked or by whom.

## What Civic adds

- **Audit trail** — every Calendar tool call logged: which tool, exact parameters, timestamp, which agent/session triggered it
- **Request guardrails** — prevent destructive operations (deleting events, inviting external domains)
- **Response guardrails** — redact attendee PII from responses before they reach the agent's context
- **Revocation** — revoke the agent's token and it loses Calendar access instantly

## Setup

Add Civic to your MCP client (see [README](../../README.md)), then enable the Google Calendar server inside [app.civic.com](https://app.civic.com).

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

Civic exposes Calendar tools (`create_event`, `list_events`, `update_event`, `delete_event`, etc.) through the same connection.

## Guardrails to configure

Configure in [app.civic.com](https://app.civic.com) or by asking your agent:

**Prevent event deletion:**
> "Block all `delete_event` tool calls."

**Restrict bookings to business hours:**
> "Block `create_event` calls where the start time is outside 9am–6pm Monday to Friday."

**Block external attendees:**
> "Block `create_event` or `update_event` calls that add attendees not from @yourcompany.com."

**Prevent back-to-back overloading:**
> "Block `create_event` if there are already 3 or more events on the same day."

**Read-only mode for reporting agents:**
> "Block all Calendar tool calls except `list_events` and `get_event`."

## Audit queries

Ask the Civic Audit Agent at [app.civic.com](https://app.civic.com):

- *"What calendar events did my agent create this week?"*
- *"Show me all `delete_event` calls in the last 30 days."*
- *"Which agents have accessed my Google Calendar?"*
- *"Show me every Calendar operation that was blocked by a guardrail."*
- *"Export my Calendar agent activity as a CSV."*

## What gets logged per call

| Field | Example |
|---|---|
| Tool | `create_event` |
| Parameters | `{title: "Q1 Review", start: "2026-03-20T10:00:00Z", attendees: ["..."]}`|
| Timestamp | `2026-03-18T09:15:44Z` |
| Session | `claude-desktop / titus@civic.com` |
| Result | `{eventId: "evt_abc123", status: "confirmed"}` |
