# Publishing Rezi on Claude

The Rezi MCP endpoint can be submitted to Claude's Connectors Directory. The plugin in `plugins/rezi` can also be submitted to the plugin directory for Cowork and Claude Code. Hosting this repository's marketplace enables direct installation; it does not imply that Anthropic has listed or verified the connector or plugin.

## Connector listing

Use `https://api.rezi.ai/mcp` with Streamable HTTP and OAuth dynamic client registration. Every user connects to the same URL and authorizes access to their own Rezi account. The server advertises the `resumes` and `jobs` scopes and S256 PKCE.

Before submitting:

1. Exercise login, `list_resumes`, `read_resume`, `get_resume_format`, `write_resume`, `search_jobs`, and `get_job_details` using a dedicated, populated Rezi test account. For the write check, create a clearly named test resume, update that test resume, and read it back. Never use a customer's resume for submission tests. Check reconnect behavior and that another account cannot read or write the test resume.
2. Prepare the name, short tagline, description, icon, documentation URL, privacy-policy URL, support contact, use cases, account requirements, and reviewer access instructions.
3. Keep reviewer credentials out of GitHub, plugin files, and public documentation. Supply the dedicated test account through Anthropic's submission process.
4. Check current account requirements in Rezi's product documentation and ensure they agree with the listing. Check tool annotations and the latest directory review requirements.
5. Submit from a Claude Team or Enterprise organization with directory-management access. Complete the portal's acknowledgments only after verifying the statements. Track status and feedback in the same portal.

See [connector submission requirements](https://claude.com/docs/connectors/building/submission) and the [pre-submission checklist](https://claude.com/docs/connectors/building/review-criteria).

## Plugin listing

1. Release the plugin changes to this public repository.
2. Run `claude plugin validate --strict ./plugins/rezi` and `claude plugin validate --strict ./.claude-plugin/marketplace.json`.
3. Install from this marketplace in a test client, authenticate, and exercise `/rezi:tailor-resume` and `/rezi:find-jobs` using the test account.
4. Submit `https://github.com/rezi-io/rezi-mcp/tree/main/plugins/rezi` as the plugin source. The marketplace repository is `https://github.com/rezi-io/rezi-mcp`.
5. Use the [Claude plugin submission form](https://claude.ai/admin-settings/directory/submissions/plugins/new) with the appropriate organization access, or the [Console submission form](https://platform.claude.com/plugins/submit) with a Developer, Admin, or Owner role.

The plugin source must be public. Directory inclusion and the Anthropic Verified badge have separate review processes. See [plugin submission guidance](https://claude.com/docs/plugins/submit).

## Updates

Bump `plugins/rezi/.claude-plugin/plugin.json` on every plugin release. Keep the marketplace source pointing at the plugin directory and avoid duplicating the version in the marketplace entry. Validate both manifests before publishing. Users of this marketplace can refresh it with `/plugin marketplace update rezi-plugins` and update the plugin through Claude's plugin manager.
