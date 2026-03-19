# PostgreSQL — Guardrails & Audit Trails

## The problem

An AI agent with database access can query, modify, and potentially destroy data. A single unguarded `DROP TABLE` or runaway query can cause irreversible damage. Without an audit trail, you have no forensic record of what queries ran, when, or which agent triggered them.

## What Civic adds

- **Audit trail** — every query logged: SQL statement, parameters, timestamp, which agent/session triggered it
- **Request guardrails** — block destructive operations before they reach the database
- **Response guardrails** — redact sensitive column data (SSNs, emails, card numbers) before it enters the agent's context
- **Revocation** — revoke the agent's token and database access drops instantly, no credential rotation required

## Setup

Add Civic to your MCP client (see [README](../../README.md)), then enable the PostgreSQL server inside [app.civic.com](https://app.civic.com) and provide your connection string.

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

Civic exposes PostgreSQL tools (`query`, `schema_inspect`, `health`) through the same connection. Your database credentials are stored in Civic's secret manager — the agent never sees them directly.

## Guardrails to configure

Configure in [app.civic.com](https://app.civic.com) or by asking your agent:

**Block all destructive operations:**
> "Block any PostgreSQL query containing DROP, DELETE, TRUNCATE, or ALTER."

**Enforce read-only access for analytics agents:**
> "Block any PostgreSQL tool call that isn't a SELECT statement."

**Redact sensitive columns from responses:**
> "Redact SSNs, email addresses, and credit card numbers from all PostgreSQL query results."

**Block access to sensitive tables:**
> "Block any query that references the `users`, `payments`, or `credentials` tables."

**Prevent expensive queries:**
> "Block queries that don't include a LIMIT clause."

**Block prompt injection via data:**
> "Enable prompt injection protection on all PostgreSQL response outputs."

## Audit queries

Ask the Civic Audit Agent at [app.civic.com](https://app.civic.com):

- *"Show me every database query my agent ran this week."*
- *"Show me all queries that touched the payments table in the last 30 days."*
- *"Were there any blocked PostgreSQL calls? Show me what was attempted."*
- *"Which agent sessions have accessed the database?"*
- *"Export my PostgreSQL audit log as a CSV."*

## What gets logged per call

| Field | Example |
|---|---|
| Tool | `query` |
| Parameters | `{sql: "SELECT * FROM orders WHERE user_id = $1", params: [42]}` |
| Timestamp | `2026-03-18T11:04:22Z` |
| Session | `claude-desktop / titus@civic.com` |
| Result | `{rows: 12, duration_ms: 38}` |

## Why this matters for compliance

Every query is attributed to a specific agent session and user identity — not just a shared database credential. This means your audit log answers:
- **Who** authorized the agent
- **What** query ran
- **When** it ran
- **What** it returned

This attribution is what makes Civic's audit trail useful for SOC 2, HIPAA, and internal security reviews — a shared `readonly` Postgres user tells you nothing about which AI agent touched which data.
