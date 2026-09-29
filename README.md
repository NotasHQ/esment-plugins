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

The flavor is chosen at **install time**. The local flavor carries no
per-user path: `esment-mcp` and `esment-cli hook …` find your config on their
own (`$ESMENT_CONFIG`, else `~/.esment/config.toml`), so the same package works
for anyone who has the Esment app in `/Applications`. The cloud flavor is for
assistants that cannot run a local process (web).

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

> The marketplace points at the **local** flavor: it runs
> `/Applications/Esment.app/Contents/MacOS/esment-mcp` and reads **your**
> `~/.esment/config.toml`, so the Esment app must be installed (and opened
> once, which creates that config). Config somewhere else? Export
> `ESMENT_CONFIG=/path/to/config.toml` in the environment the assistant starts
> from. No app on the machine? Use the **cloud** flavor — see "Manual upload"
> below.

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

- The local flavor embeds only the app binary path
  (`/Applications/Esment.app/...`), never a config path: the binaries resolve
  `$ESMENT_CONFIG` → `~/.esment/config.toml` themselves, and never a
  `config.toml` in the assistant's working directory (that one belongs to your
  project). Regenerate with `esment plugin export --variant local` — without
  `--config`, which would bake that path back in.
- Both flavors ship `.claude-plugin/plugin.json` with the MCP server inline
  (camelCase `mcpServers`) — the shape `claude plugin validate` accepts.
- License: MIT. Icon: the Esment brand icon.
