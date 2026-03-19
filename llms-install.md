# Civic MCP Gateway — Installation Guide for AI Agents

## What this server does

Civic MCP Gateway connects AI agents to 85+ MCP tools (Gmail, Google Calendar, PostgreSQL, Slack, and more) with guardrails, audit trails, scoped permissions, and revocable access.

## Prerequisites

- A Civic account and bearer token from https://app.civic.com
- If the user does not have a token, direct them to sign up at https://app.civic.com and generate one from the onboarding flow

## Installation steps

### Step 1 — Ask the user for their Civic token

Ask the user: "Do you have a Civic bearer token from app.civic.com? If not, visit https://app.civic.com to create an account and generate one."

The token looks like a long alphanumeric string. Store it as `CIVIC_TOKEN`.

### Step 2 — Add to Cline MCP settings

Add the following to the user's Cline MCP configuration (VS Code settings → Cline → MCP Servers, or `cline_mcp_settings.json`):

```json
{
  "mcpServers": {
    "civic": {
      "url": "https://app.civic.com/hub/mcp",
      "headers": {
        "Authorization": "Bearer CIVIC_TOKEN"
      }
    }
  }
}
```

Replace `CIVIC_TOKEN` with the token from Step 1.

### Step 3 — Optional: lock to a profile

If the user has a specific Civic toolkit profile, append it to the URL:

```
https://app.civic.com/hub/mcp?profile=their-profile-slug
```

### Step 4 — Verify

After configuration, ask Cline to list available tools. The user should see tools from their enabled Civic toolkit (e.g. Gmail, Google Calendar, PostgreSQL tools).

If tools don't appear, verify the token is valid by visiting https://app.civic.com.

## No local installation required

Civic MCP Gateway is a remote server — no npm install, no Docker, no local process. The only requirement is a valid bearer token.

## Troubleshooting

- **No tools listed** — token may be invalid or expired. Generate a new one at https://app.civic.com
- **Auth errors** — ensure the `Authorization: Bearer` header is set correctly with no extra spaces
- **Wrong tools** — the toolkit profile controls which tools are available. Configure at https://app.civic.com
