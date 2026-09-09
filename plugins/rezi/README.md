# Rezi for Claude

Connect your Rezi account to Claude, tailor a resume to a role, and search for matching jobs. This plugin bundles the hosted Rezi MCP connection with two workflow skills. It uses the same server as other Rezi MCP clients.

## Install in Claude Code

Run inside Claude Code:

```text
/plugin marketplace add rezi-io/rezi-mcp
/plugin install rezi@rezi-plugins
```

Follow the installation prompts. If the install summary asks you to reload plugins, run `/reload-plugins`. Open `/mcp`, select the Rezi connection supplied by the plugin, and complete the browser sign-in and consent flow with your Rezi account. You do not need to enter an API key into this plugin.

If Rezi is already connected separately, use one connection to avoid duplicate tool entries. The plugin supplies its own connection; adding it with `claude mcp add` as well is unnecessary.

## Workflows

```text
/rezi:tailor-resume Tailor my engineering resume to this job description: ...
/rezi:find-jobs Find remote product designer roles in the United States.
```

You can also ask Claude to use Rezi in ordinary conversation. Resume tailoring reads the selected resume and job requirements, prepares supported changes, and saves when you ask it to. Job search uses your requested role, location, and remote preference, then retrieves details for promising listings. It does not submit applications.

## Claude web, Desktop, and Cowork

To use the MCP connection directly, open Claude's connector settings and add a custom connector with this URL:

```text
https://api.rezi.ai/mcp
```

Sign in to Rezi and approve the consent screen. Custom-connector availability and organization permissions depend on your Claude account. This manual connection provides Rezi tools; plugin skills are included when the plugin is installed in a supported surface.

## Account and connection behavior

- Use a Rezi account with access to the MCP feature. See the [current Rezi setup guide](https://www.rezi.ai/rezi-docs/resume-mcp-server) for account requirements.
- Rezi uses OAuth with a browser consent flow. The connector has access to resumes owned by the signed-in account and to job search.
- The current server issues access tokens for 30 days and does not issue refresh tokens. Reconnect if authorization expires or is revoked.
- Resume writes change data in your Rezi account. Review the target resume and requested changes when Claude asks for tool permission.
- To disconnect in Claude Code, disable or uninstall the plugin. Manage the account's connected access in Rezi as well if you want to revoke its credential.

## Privacy and support

The plugin connects to `https://api.rezi.ai/mcp`. Resume content and tool inputs and outputs pass between Claude and Rezi to perform the requested task. The plugin contains no embedded credentials, local server, install script, or background hooks. Credentials are managed by the MCP client and Rezi's OAuth service.

Read [Rezi's privacy policy and terms](https://www.rezi.ai/legal) and [MCP setup guide](https://www.rezi.ai/rezi-docs/resume-mcp-server). Contact [Rezi support](mailto:support@rezi.io) for help.

## Local validation

From the repository root:

```bash
claude plugin validate --strict ./plugins/rezi
claude plugin validate --strict ./.claude-plugin/marketplace.json
claude --plugin-dir ./plugins/rezi
```

Authenticate using `/mcp`, then try the two workflow commands against a dedicated test account before publishing. Manifest validation does not verify OAuth or tool execution.
