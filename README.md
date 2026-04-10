# Rezi MCP Server

The Rezi Model Context Protocol (MCP) server lets AI tools connect to a user's Rezi account and work directly with resume data. Instead of copying resume JSON into chat, users can access their resumes, read them, and write updates back to Rezi through MCP.

## Remote server

### Who can use this feature

- Any Rezi user with a Rezi account.
- Any MCP client that supports remote streamable HTTP servers and interactive tool use.

Examples include Codex, Claude Code, Claude Desktop, Cursor, Gemini CLI, Lovable, and other MCP clients with remote HTTP support.

## Server URL

MCP Server URL: `https://api.rezi.ai/mcp`

## Available tools and capabilities

Tool name | Description
--- | ---
`list_resumes` | Returns the authenticated user's resumes as lightweight summaries with ID, name, job title, and last updated time.
`read_resume` | Returns the full JSON document for a single resume by `resume_id`.
`write_resume` | Creates a new resume when `resume_id` is omitted, or updates an existing resume when `resume_id` is provided.

### Resume write shape

`write_resume` expects a `resume` object that follows the Rezi resume schema. In practice, the important top-level fields are:

- `name`
- `jobTitle`
- `jobDescription`
- `jobCompany`
- `template`
- `data`

The `data` object includes sections such as `contact`, `summary`, `experience`, `education`, `skills`, `projects`, `certification`, `coursework`, `involvement`, and `awards`. Collection-style sections should use UUID keys, plus `index` for ordering and `hide` for visibility.

## Implementation instructions

### Codex

1. Add the MCP server:

```bash
codex mcp add rezi --url https://api.rezi.ai/mcp
```

2. Verify it is configured:

```bash
codex mcp list
```

3. Start Codex and use the Rezi MCP server.

### Claude Code

1. Add the MCP server:

```bash
claude mcp add rezi --transport http https://api.rezi.ai/mcp
```

2. Start Claude Code:

```bash
claude
```

3. Use the Rezi MCP server from Claude Code.

### Claude Desktop

1. Open Claude Desktop.
2. Go to `Customize > Connectors`.
3. Add a new custom connector for the Rezi MCP server.
4. Set the server URL to:

```text
https://api.rezi.ai/mcp
```

5. Complete authorization if Claude prompts for it, then use the Rezi MCP server from Claude Desktop.

Note: for remote MCP servers, Claude Desktop uses Connectors. Do not add this remote server through `claude_desktop_config.json`.

### Cursor

Add this configuration to your `mcp.json`:

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

After saving the config, restart Cursor if needed, then use the Rezi MCP server from Cursor.

### Gemini CLI

1. Add the MCP server:

```bash
gemini mcp add --transport http rezi https://api.rezi.ai/mcp
```

2. Verify it is configured:

```bash
gemini mcp list
```

3. Start Gemini CLI and use the Rezi MCP server.

You can also add the server manually in `~/.gemini/settings.json`:

```json
{
  "mcpServers": {
    "rezi": {
      "httpUrl": "https://api.rezi.ai/mcp"
    }
  }
}
```

### Lovable

Lovable supports custom MCP servers as personal connectors on paid plans.

1. Open Lovable.
2. Go to `Settings -> Connectors -> Personal connectors`.
3. Click `New MCP server`.
4. Set the server name to `Rezi` or another name you prefer.
5. Set the server URL to:

```text
https://api.rezi.ai/mcp
```

6. Choose the appropriate authentication method if prompted, then add the server.

After that, the Rezi MCP server is available to Lovable as a personal connector.

### Any streamable HTTP MCP client

If your MCP client supports remote streamable HTTP servers, point it at:

```text
https://api.rezi.ai/mcp
```

Then use the Rezi MCP server to call the resume tools.

## How resume access works

`list_resumes` returns resumes that belong to the authenticated Rezi user as a compact summary list so the response stays small. `read_resume` returns the complete stored resume document. `write_resume` writes back through the same authenticated user context and supports both create and update flows.

On updates, Rezi MCP first reads the existing resume and verifies that it belongs to the authenticated user before writing changes.

## Session behavior and security

- Credentials are stored in memory and scoped to the MCP session ID assigned by the HTTP server.
- Session credentials are refreshed automatically when they are close to expiry.
- Resume reads and writes are performed using the authenticated user's Rezi session context.
- The service does not persist credentials to disk. If the service restarts, the user needs to sign in again.

This design keeps access aligned with the user's own Rezi permissions rather than introducing a separate privileged backend identity for resume operations.
