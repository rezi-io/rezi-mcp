# Rezi MCP

Rezi MCP lets supported AI clients connect to your Rezi account so they can read your resumes, update them, and look up jobs while helping you tailor an application.

## Server URL

```text
https://api.rezi.ai/mcp
```

## Connect

### Claude Code

```bash
claude mcp add rezi --transport http https://api.rezi.ai/mcp
```

### Cursor

Add this to your `mcp.json`:

```json
{
  "mcpServers": {
    "rezi": {
      "url": "https://api.rezi.ai/mcp",
      "transport": "streamable-http"
    }
  }
}
```

### Any compatible MCP client

If your client supports remote streamable HTTP MCP servers, point it to:

```text
https://api.rezi.ai/mcp
```

## First-time sign in

The first time your MCP client tries to use a Rezi tool, it opens the Rezi login flow in your browser. After you sign in, the client stores the access token and keeps using it until it expires. In normal use, you only need to sign in again when that token expires or if you reconnect the server.

## Available tools

| Tool | What it does |
|------|--------------|
| `list_resumes` | Shows your resumes, ordered by most recently updated. |
| `read_resume` | Returns the full JSON for a specific resume. |
| `write_resume` | Creates a new resume or updates an existing one. |
| `search_jobs` | Searches job listings by role and location. |
| `get_job_details` | Fetches the full details for a job found through `search_jobs`. |

## Typical workflows

### Update an existing resume

1. Use `list_resumes` to find the right resume.
2. Use `read_resume` to load it.
3. Ask your AI client to make changes.
4. Use `write_resume` with the updated fields.

When updating, only the fields you send are changed. Sections you do not include are preserved.

### Create a new resume

Call `write_resume` without `resume_id`. Rezi creates a new resume and fills in the standard defaults needed for it to work correctly in the product.

### Tailor a resume to a job

1. Use `search_jobs` to find a role.
2. Use `get_job_details` to read the full posting.
3. Use `read_resume` to load your current resume.
4. Ask your AI client to tailor the resume for that job.
5. Use `write_resume` to save the result.

## Notes

- You need an active Rezi subscription to use the tools.

## Session behavior and security

- Credentials are stored in memory and scoped to the MCP session ID assigned by the HTTP server.
- Session credentials are refreshed automatically when they are close to expiry.
- Resume reads and writes are performed using the authenticated user's Rezi session context.
- The service does not persist credentials to disk. If the service restarts, the user needs to sign in again.

This design keeps access aligned with the user's own Rezi permissions rather than introducing a separate privileged backend identity for resume operations.
