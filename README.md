# Esment Agent Plugin

The Esment Agent Plugin distribution — one package, two flavors, every
assistant. Esment is a **local-first memory engine**: the plugin points your
assistants at your own Esment server, on your machine, and the same package
also ships a cloud flavor for the web assistants.

```
esment-plugins/
├── .claude-plugin/
│   └── marketplace.json   # Claude marketplace index (git source)
├── plugins/
│   ├── esment/            # LOCAL flavor — stdio MCP, hooks, skills (desktop)
│   └── esment-cloud/      # CLOUD flavor — remote MCP URL + OAuth (web)
└── README.md
```

## The two flavors

| | `plugins/esment` (local) | `plugins/esment-cloud` (cloud) |
|---|---|---|
| MCP server | stdio, runs `esment-mcp` on your machine | `https://mcp.esment.notas.ai/mcp` + OAuth |
| Where the memory lives | **your disk** (`~/.esment`) | the Esment cloud |
| Used by | ChatGPT/Codex desktop, Claude Code, Claude Desktop, Cursor | ChatGPT web, Perplexity, any URL-based client |
| Hooks (deterministic injection) | bundled | — |

The flavor is chosen at **install time**: the Esment app's Connections card
exports/installs the local flavor with your machine's exact paths. The cloud
flavor is for assistants that cannot run a local process (web).

## Install

### Easiest path — the Esment app (macOS)

Install the app, open **Connections**, click **Connect** on each assistant
card. The app writes every client's config (MCP entries, hooks, the plugin
package, the local marketplaces) with your machine's paths. No terminal, no
config files.

### Claude Code / Claude Desktop (git marketplace)

```bash
claude plugin marketplace add https://github.com/xm1000z/esment-plugins
claude plugin install esment@esment
```

Claude Desktop: **Plugins → Add marketplace → repository URL**
(`https://github.com/xm1000z/esment-plugins`) → install. The Desktop's MCP
connector is set up by the Esment app card (it writes
`claude_desktop_config.json`); the plugin adds skills.

### ChatGPT / Codex desktop

Install from the **personal marketplace**: `~/.agents/plugins/marketplace.json`
→ source `~/.codex/plugins/esment` (the Esment app card does this for you).
Skills, hooks and the @Esment reference load; the bundled stdio MCP follows
the OpenAI plugin format (`mcpServers` → `./.mcp.json`, camel-case, the same
shape as OpenAI's bundled `computer-use` plugin).

> **Known limitation (Aug 2026)**: the ChatGPT desktop *alpha* build
> (`codex 0.147.0-alpha.6.5`) loads the plugin (skills/hooks/@mention) but
> does not yet attach bundled MCP tools from personal-marketplace plugins to
> conversations — verified with a minimal plugin that mirrors OpenAI's own
> bundled structure. The MCP server itself is proven (works in Codex CLI and
> Claude Desktop). The package is format-exact and works the moment the
> desktop wires it.

### ChatGPT web (cloud flavor)

The cloud flavor is meant for the **registered-connection flow** (OpenAI
developer mode): register the MCP connection in `chatgpt.com/plugins` (URL
`https://mcp.esment.notas.ai/mcp`, OAuth), then map it in the plugin's
`.app.json`. See the Esment docs for the walkthrough.

## Notes

- The local flavor's `.mcp.json` embeds the machine paths of the machine that
  exported it (the app binary + your config). The Esment app regenerates the
  package with **your** paths on every Connect; for manual installs, adjust
  the two lines.
- License: MIT. Icon: the Esment brand icon.
