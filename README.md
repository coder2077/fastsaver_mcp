# FastSaver API for AI agents

Turn Instagram, TikTok, YouTube, Pinterest, Facebook, X, Rutube and Likee links
into JSON with direct media download URLs, from your AI agent or coding
assistant.

This repository has three things:

- **An agent skill** ([`skills/fastsaver-api/SKILL.md`](skills/fastsaver-api/SKILL.md)).
  It teaches a coding agent how to integrate FastSaver API into an app, bot or
  script: endpoints, prices, response shapes, errors, retries and code.
- **A remote MCP server** at `https://mcp.fastsaver.io/mcp`. Your assistant
  calls FastSaver directly, with seven tools.
- **A Claude Code plugin** that installs both in one step.

## Get an API key

Sign up at https://api.fastsaver.io/auth. The key is on the dashboard.

## Claude Code: skill and MCP server together

```
/plugin marketplace add coder2077/fastsaver_mcp
/plugin install fastsaver-api@fastsaver
```

Claude Code asks for your API key when the plugin is enabled and keeps it in
secure storage. To set or change it later:

```
/plugin configure fastsaver-api@fastsaver
```

## The skill alone

Copy the `skills/fastsaver-api` folder into your agent's skills folder. In
Claude Code that is `~/.claude/skills/`. The skill follows the open Agent
Skills format (https://agentskills.io), so other agents that read
`SKILL.md` skills can use it too.

## The MCP server alone

- URL: `https://mcp.fastsaver.io/mcp` (Streamable HTTP, stateless, protocol
  2026-07-28 and 2025-11-25).
- Auth: your key in the `X-Api-Key` header, or `Authorization: Bearer <key>`.
  Never put the key in the URL.
- Listing the tools and `list_supported_platforms` work without a key.

Claude Code:

```bash
claude mcp add --transport http fastsaver https://mcp.fastsaver.io/mcp --header "X-Api-Key: YOUR_KEY"
```

Cursor (`~/.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "fastsaver": {
      "url": "https://mcp.fastsaver.io/mcp",
      "headers": { "X-Api-Key": "${env:FASTSAVER_API_KEY}" }
    }
  }
}
```

VS Code (`.vscode/mcp.json`):

```json
{
  "inputs": [
    { "type": "promptString", "id": "fastsaver-key", "description": "FastSaver API key", "password": true }
  ],
  "servers": {
    "fastsaver": {
      "type": "http",
      "url": "https://mcp.fastsaver.io/mcp",
      "headers": { "X-Api-Key": "${input:fastsaver-key}" }
    }
  }
}
```

Codex CLI (`~/.codex/config.toml`):

```toml
[mcp_servers.fastsaver]
url = "https://mcp.fastsaver.io/mcp"
bearer_token_env_var = "FASTSAVER_API_KEY"
```

Gemini CLI:

```bash
gemini mcp add --transport http --header "X-Api-Key: YOUR_KEY" fastsaver https://mcp.fastsaver.io/mcp
```

Any other client that can send a header: the URL above plus the `X-Api-Key` header.

## Tools

| Tool | What it does | Credits |
|---|---|---|
| `fetch_media` | Direct links and metadata for one post, reel, story, video or pin | 1.5; Instagram stories and highlights 5; Rutube, Likee 3 |
| `youtube_info` | Title, author, duration and formats with file sizes | 2 |
| `youtube_search` | Search YouTube videos, one page at a time | 2 |
| `youtube_download` | A temporary direct link: audio, or 144p to 2160p | 15; 1440p and 2160p 25 |
| `music_charts` | Shazam top chart for a country or the world | 1 |
| `get_balance` | Plan, credits left and rate limit | free |
| `list_supported_platforms` | Platforms, link forms and prices | free, no key |

A call that fails costs 0.1, even for an unsupported link or one with no post or
video id. A tool result is the same JSON the REST API returns.

## Links

- Docs: https://api.fastsaver.io/docs
- MCP setup for each client: https://api.fastsaver.io/docs/mcp
- Prices: https://api.fastsaver.io/pricing
- Full reference for agents: https://api.fastsaver.io/llms-full.txt
- Privacy: https://api.fastsaver.io/privacy
- Terms: https://api.fastsaver.io/terms

## License

MIT, see [LICENSE](LICENSE). It does not cover the FastSaver name or logo.
