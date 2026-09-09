# Rezi MCP

Rezi MCP lets supported AI clients connect to your Rezi account so they can read your resumes, update them, and look up jobs while helping you tailor an application.

## Server URL

```text
https://api.rezi.ai/mcp
```

## Connect

### Claude Code

Install the Rezi plugin to get the MCP connection plus resume-tailoring and job-search workflows. Run inside Claude Code:

```text
/plugin marketplace add rezi-io/rezi-mcp
/plugin install rezi@rezi-plugins
```

Follow the installation prompts, reload plugins if requested, and open `/mcp` to sign in to Rezi. Then try `/rezi:tailor-resume` or `/rezi:find-jobs`. See the [plugin guide](plugins/rezi/README.md) for examples and setup details.

To connect just the MCP server without installing the plugin, run in your terminal:

```bash
claude mcp add rezi --transport http https://api.rezi.ai/mcp
```

Open `/mcp` inside Claude Code to complete authentication. Choose either the plugin or the direct connection to avoid duplicate Rezi tools.

### Claude web, Desktop, and Cowork

Open Claude's connector settings and add a custom connector with URL `https://api.rezi.ai/mcp`. Sign in to Rezi and approve the consent screen. Available connector settings depend on your Claude plan and organization permissions.

The custom connector provides the Rezi tools. The plugin additionally bundles workflow skills for supported Claude surfaces. See [publishing on Claude](docs/claude-publishing.md) for the separate directory-submission steps; this repository's marketplace does not imply Anthropic listing or verification.

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
| `read_resume` | Returns editable resume content and summary metadata for a specific resume, excluding internal persistence fields. |
| `get_resume_format` | Describes supported resume sections and content fields. Use it before preparing writes. |
| `write_resume` | Creates a new resume or updates an existing one. |
| `search_jobs` | Searches job listings by role and location. |
| `get_job_details` | Fetches the full details for a job found through `search_jobs`. |

## Typical workflows

### Update an existing resume

1. Use `list_resumes` to find the right resume.
2. Use `read_resume` to load it.
3. Use `get_resume_format` to check the current content format.
4. Ask your AI client to make changes.
5. Use `write_resume` with the existing `resume_id` and only the updated fields, then read the resume back to verify.

When updating, only the fields you send are changed. Sections you do not include are preserved.

### Create a new resume

Call `get_resume_format` to check the current content format, then call `write_resume` without `resume_id`. Rezi creates a new resume and fills in the standard defaults needed for it to work correctly in the product. Reuse the returned ID for subsequent updates.

### Tailor a resume to a job

1. Use `search_jobs` to find a role.
2. Use `get_job_details` to read the full posting.
3. Use `read_resume` to load your current resume.
4. Use `get_resume_format` and ask your AI client to tailor the resume for that job.
5. Use `write_resume` with the existing resume ID to save the requested changes, then read the resume back to verify.

## Notes

- You need a Rezi account with access to the MCP feature. See the [current Rezi setup guide](https://www.rezi.ai/rezi-docs/resume-mcp-server) for account requirements.

## Authentication and data access

- Rezi uses OAuth with a browser consent flow, dynamic client registration, and S256 PKCE.
- The MCP client manages its stored access token. Rezi stores a hashed, revocable credential using its MCP key store.
- The current server issues 30-day access tokens and does not issue refresh tokens. Reconnect when a token expires or is revoked.
- Resume reads and writes are authorized against the signed-in account's ownership. The server uses stateless Streamable HTTP; authorization is not tied to one server process or an in-memory MCP session.
- Resume content and tool inputs and outputs pass between the AI client and Rezi to perform requested tasks. Never put account passwords or access tokens in public issues or plugin configuration committed to GitHub.

See [Rezi's privacy policy and terms](https://www.rezi.ai/legal), the [MCP setup guide](https://www.rezi.ai/rezi-docs/resume-mcp-server), or contact [support@rezi.io](mailto:support@rezi.io).

## Plugin development

The marketplace catalog is `.claude-plugin/marketplace.json`. The self-contained Claude plugin lives in `plugins/rezi`; it includes the remote MCP connection and two workflow skills, with no local server or install scripts.

```bash
claude plugin validate --strict ./plugins/rezi
claude plugin validate --strict ./.claude-plugin/marketplace.json
claude --plugin-dir ./plugins/rezi
```

See [publishing on Claude](docs/claude-publishing.md) for testing and directory submission. Manifest validation alone does not verify login or tool execution.
