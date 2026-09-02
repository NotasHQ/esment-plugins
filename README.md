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
│   ├── esment/            # LOCAL flavor — stdio MCP, hooks, skills, Claude manifest
│   └── esment-cloud/      # CLOUD flavor — remote MCP URL + OAuth, Claude manifest
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

The repo ships the Claude layout (`.claude-plugin/plugin.json` in both
flavors) and validates with `claude plugin validate` — installing is two
commands:

```bash
claude plugin marketplace add https://github.com/NotasHQ/esment-plugins
claude plugin install esment@esment
```

Claude Desktop: **Plugins → Add marketplace → repository URL**
(`https://github.com/NotasHQ/esment-plugins`) → install.

> The marketplace points at the **local** flavor, whose MCP runs the
> `esment-mcp` binary of the machine that exported the package. For your own
> install, let the Esment app card do it (it regenerates the package with
> **your** paths and writes the Desktop's `claude_desktop_config.json`). For a
> plug-and-play install that works for anyone, use the **cloud** flavor
> instead — see "Manual upload" below.

### Claude Desktop — manual upload (custom plugin zip)

Everywhere else (web downloads, other users' machines) the cloud flavor is the
portable one: point Claude at a zip whose root is `.claude-plugin/` and it
connects to `https://mcp.esment.notas.ai/mcp` (OAuth). Built from
`plugins/esment-cloud`:

```bash
cd plugins/esment-cloud && zip -r ../esment-plugin-cloud.zip .claude-plugin plugin.json mcp.json skills
```

Then Claude Desktop → **Plugins → Upload plugin** (or the browser-equivalent
"custom plugin" import) and pick the zip.

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
- Both flavors ship `.claude-plugin/plugin.json` with the MCP server inline
  (camelCase `mcpServers`) — the shape `claude plugin validate` accepts.
- License: MIT. Icon: the Esment brand icon.
