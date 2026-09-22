# Wave plugin for Grok Bot, Cursor, and Grok Build

[Wave](https://wave.co) records your meetings, phone calls, and in-person conversations, then stores a transcript, a summary, and structured action items for each one. This plugin connects your agent to your Wave library through the hosted [Wave MCP server](https://mcp.wave.co), so you can search what was said, prep for meetings, pull verbatim quotes, and manage action items without leaving your workflow.

One repo, three hosts:

| Host | Manifest it reads | Where to install |
|---|---|---|
| Grok Bot | `.cursor-plugin/plugin.json` + `mcp.json` | Settings → Plugins → search **Wave** → Add (loads the Cursor Marketplace) |
| Cursor | `.cursor-plugin/plugin.json` + `mcp.json` | [cursor.com/marketplace](https://cursor.com/marketplace) → Wave → Install |
| Grok Build | `.grok-plugin/plugin.json` + `.mcp.json` | `grok plugin install wave` or `/marketplace` in the terminal |

## What's included

| Component | Path | Purpose |
|---|---|---|
| MCP server | `mcp.json` / `.mcp.json` | Connects to the hosted Wave MCP server at `https://mcp.wave.co/` (Streamable HTTP) |
| Skill | `skills/wave/SKILL.md` | How to search, synthesize, and quote from Wave recordings effectively |

No hooks, commands, rules, or local code — the plugin is a server config plus one markdown skill.

## Install

**Grok Bot / Cursor:** install **Wave** from the marketplace, then click **Connect** when the sign-in card appears. Your browser opens app.wave.co; sign in and approve. No API key to paste.

**Grok Build:**

```bash
grok plugin install wave
```

Or browse to it with `/marketplace` inside Grok Build.

**Local testing (Cursor):** clone this repo and symlink it to `~/.cursor/plugins/local/wave`, then reload Cursor.

## Authentication

On first use, the Wave MCP server initiates **OAuth 2.0 with PKCE**: your browser opens app.wave.co, you sign in to your Wave account, and your agent (Grok Bot, Cursor, or Grok Build) receives a scoped token. No API keys to copy.

Alternatively, you can mint a manual token (`wave_mcp_...`) from your [app.wave.co](https://app.wave.co) settings and supply it as a bearer token.

Every tool is scoped to the authenticated user — the server can only ever see your own sessions.

## Tools

Read:

- `search_sessions` — semantic search across summaries and transcripts, with folder/tag filters
- `list_sessions` — reverse-chronological listing with filters and pagination
- `get_session` — one session's metadata, summary, and optional transcript
- `get_transcript` — plain, segmented, or speaker-labeled transcript
- `get_action_items` — structured action items with a version for safe updates
- `list_folders` — folder ids, names, colors, and counts
- `search` / `fetch` — thin search/fetch interface for deep-research clients

Write (narrowly scoped — nothing here can delete a session or alter a recording or transcript):

- `update_action_items` — version-checked full replacement of a session's action items
- `create_folder`, `add_session_to_folder`, `remove_session_from_folder` — idempotent folder management

## Network endpoints and data access

Declared for review, per marketplace guidelines:

- **`https://mcp.wave.co/`** — the only endpoint this plugin calls. Hosted MCP server (JSON-RPC over HTTP).
- **`https://app.wave.co`** — opened in your browser during the OAuth flow; never called directly by the plugin.
- Credentials: an OAuth bearer token (or manual `wave_mcp_*` token) stored by the host's standard MCP credential handling. The plugin bundles no hooks, no shell commands, and no local code execution — it is an MCP server config plus a markdown skill.

## About Wave

Wave is an AI notetaker for meetings, phone calls, and everything in between — on mobile, desktop, and the web. Learn more at [wave.co](https://wave.co). This plugin is maintained by the Wave team ([Mohrer Associates](https://github.com/mohrer-associates)).

## License

[MIT](LICENSE)
