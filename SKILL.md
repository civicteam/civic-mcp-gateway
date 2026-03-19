---
name: civic-mcp-gateway
description: Connect AI agents to 85+ tools (Gmail, Google Calendar, PostgreSQL, Slack, and more) through Civic's MCP Gateway with guardrails, scoped permissions, audit trails, and revocable access. Use this skill when you need to call external tools securely with identity, authorization, and logging that the agent can't bypass.
---

# Civic MCP Gateway

Civic separates the permission layer from the AI agent so agents can't get around restrictions. Every tool call is logged, permissions are user-controlled, and access can be revoked instantly.

## When to use this skill

- When you need to call external tools (Gmail, Google Calendar, PostgreSQL, Slack, etc.) with guardrails
- When tool calls need audit trails with agent identity and timestamps
- When access must be scoped to specific tools or revocable at any time
- When sensitive data in tool responses needs redaction before entering the agent's context

## MCP Server Configuration

```json
{
  "mcp": {
    "servers": {
      "civic": {
        "url": "https://app.civic.com/hub/mcp",
        "headers": {
          "Authorization": "Bearer ${CIVIC_TOKEN}"
        }
      }
    }
  }
}
```

## Environment Variables

- `CIVIC_TOKEN` (required) — Bearer token generated at [app.civic.com](https://app.civic.com) during onboarding
- `CIVIC_PROFILE` (optional) — Toolkit profile slug appended as `?profile=value` to scope the gateway

## Setup

1. Sign up at [app.civic.com](https://app.civic.com) and generate a bearer token
2. Set `CIVIC_TOKEN` in your environment
3. Configure the MCP server above in your agent's settings

## Client-Specific Setup

**Claude Code:**
```bash
claude mcp add --transport http civic https://app.civic.com/hub/mcp
```

**Claude Desktop / Cursor** — add to MCP config:
```json
{
  "mcpServers": {
    "civic": {
      "url": "https://app.civic.com/hub/mcp"
    }
  }
}
```

**Windsurf** — add to `~/.codeium/windsurf/mcp_config.json`:
```json
{
  "mcpServers": {
    "civic": {
      "serverUrl": "https://app.civic.com/hub/mcp"
    }
  }
}
```

## Available Tools

Civic provides access to 85+ MCP servers including:

- **Gmail** — email management, drafts, filters, labels, search
- **Google Calendar** — events, scheduling, attendees, availability
- **PostgreSQL** — queries, schema inspection, health monitoring
- **Slack** — messaging, channels, threads
- And 80+ more across productivity, CRM, databases, finance, analytics, and infrastructure

## Guardrails

Civic supports request guardrails (block before execution), response guardrails (redact after execution), parameter guardrails, and transform guardrails — all configurable at [app.civic.com](https://app.civic.com).

Examples:
- Block Gmail sends containing secrets or to external domains
- Enforce read-only PostgreSQL access (block DROP, DELETE, ALTER)
- Redact PII from tool responses before they enter agent context
- Prevent bulk operations and enforce rate limits

## Audit

Every tool call is logged with: tool name, input parameters, output, timestamp, and caller identity. Query audit logs at [app.civic.com](https://app.civic.com) or via the Civic Audit Agent.

## Documentation

Full docs at [docs.civic.com](https://docs.civic.com/civic/quickstart)
