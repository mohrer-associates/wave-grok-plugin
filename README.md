# Wave plugin for Grok Build

[Wave](https://wave.co) records your meetings, phone calls, and in-person conversations, then stores a transcript, a summary, and structured action items for each one. This plugin connects Grok Build to your Wave library through the hosted [Wave MCP server](https://mcp.wave.co), so you can search what was said, prep for meetings, pull verbatim quotes, and manage action items without leaving your terminal.

## What's included

| Component | Path | Purpose |
|---|---|---|
| MCP server | `.mcp.json` | Connects to the hosted Wave MCP server at `https://mcp.wave.co/` (HTTP transport) |
| Skill | `skills/wave/SKILL.md` | How to search, synthesize, and quote from Wave recordings effectively |

## Install

```bash
grok plugin install wave
```

Or browse to it with `/marketplace` inside Grok Build.

## Authentication

On first use, the Wave MCP server initiates **OAuth 2.0 with PKCE**: your browser opens app.wave.co, you sign in to your Wave account, and Grok Build receives a scoped token. No API keys to copy.

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
- Credentials: an OAuth bearer token (or manual `wave_mcp_*` token) stored by Grok Build's standard MCP credential handling. The plugin bundles no hooks, no shell commands, and no local code execution — it is an MCP server config plus a markdown skill.

## About Wave

Wave is an AI notetaker for meetings, phone calls, and everything in between — on mobile, desktop, and the web. Learn more at [wave.co](https://wave.co). This plugin is maintained by the Wave team ([Mohrer Associates](https://github.com/mohrer-associates)).

## License

[MIT](LICENSE)
