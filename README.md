# Civic MCP Gateway

The Civic MCP Gateway gives AI agents a verified identity, scoped permissions, audit trails, and revocable access when calling MCP tools.

This repository contains integration metadata, configuration files, and client examples. The gateway itself runs at **[app.civic.com](https://app.civic.com)**.

---

## What it does

Without an identity layer, any AI agent can call any MCP tool with no record of who authorized it, what it accessed, or how to revoke its access. Civic fixes that:

- **Agent identity** — agents authenticate with a Civic-issued bearer token
- **Scoped permissions** — grant agents access to specific tools only
- **Audit trail** — every tool call is logged with agent identity and timestamp
- **Revocable access** — revoke a token and the agent loses access immediately
- **Guardrails** — rate limits, approval workflows, and deny lists via the configurator

---

## MCP Endpoint

```
https://app.civic.com/hub/mcp
```

**Transport:** Streamable HTTP
**Auth:** Bearer token (generated at [app.civic.com](https://app.civic.com))

---

## Quickstart

### 1. Get a token

Sign in at [app.civic.com](https://app.civic.com) and generate a bearer token from the onboarding flow.

### 2. Configure your MCP client

**Claude Desktop** — native Streamable HTTP:

Add to `~/Library/Application Support/Claude/claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "civic": {
      "url": "https://app.civic.com/hub/mcp"
    }
  }
}
```

**Claude Code** — native HTTP transport (no bridge needed):

```bash
claude mcp add --transport http civic https://app.civic.com/hub/mcp
```

Or for stdio via Hub Bridge:
```bash
claude mcp add civic "npx @civic/hub-bridge"
```

**Cursor** — native Streamable HTTP:

Add to your Cursor MCP config:
```json
{
  "mcpServers": {
    "civic": {
      "url": "https://app.civic.com/hub/mcp"
    }
  }
}
```

**Windsurf** — native Streamable HTTP, no bridge required:

Add to `~/.codeium/windsurf/mcp_config.json`:
```json
{
  "mcpServers": {
    "civic": {
      "serverUrl": "https://app.civic.com/hub/mcp"
    }
  }
}
```
Windsurf handles OAuth automatically on first connection.

### 3. Optional: lock to a profile

Append `?profile=your-toolkit` to scope the gateway to a specific configuration:

```
https://app.civic.com/hub/mcp?profile=your-toolkit
```

### Automated agents (token-based auth)

For non-interactive agents that can't complete an OAuth flow, generate a bearer token at [app.civic.com](https://app.civic.com) and pass it directly:

```bash
curl -X POST https://app.civic.com/hub/mcp \
  -H "Authorization: Bearer $CIVIC_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"tools/list","id":1}'
```

---

## Documentation

Full documentation at **[docs.civic.com](https://docs.civic.com/civic/quickstart)**

---

## Working example

See [civicteam/nexus-demo](https://github.com/civicteam/nexus-demo) for a full client integration using Next.js, federated auth, and MCP tool visualization.

---

## License

MIT
