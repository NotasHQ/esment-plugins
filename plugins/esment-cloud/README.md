# Esment for Claude

Esment is a personal, long-term memory that works across your AI assistants.
This plugin connects Claude to your Esment account so it can recall what you
have shared before (your projects, preferences, decisions and notes) before it
answers, and save new facts as you share them. Every memory stays inspectable:
you can review, edit or delete it from Esment at any time.

## What the plugin contains

- **MCP connector** — the remote Esment MCP server at
  `https://mcp.esment.notas.ai/mcp`, authenticated with OAuth. You sign in to
  your Esment account the first time Claude uses it.
- **Skill `recall`** — tells Claude when to search your memory
  (`search_context`, `get_context`) and when to save (`add_memory`), how to
  correct a stored fact (`update_memory`, `forget_memory`), and to keep using
  Claude's own memory alongside Esment.

## What it sends and where

The plugin runs no local code, hooks or scripts. Claude calls the Esment MCP
server over HTTPS only when a tool is used: search queries go to the server to
find matching memories, and facts you choose to share are stored in your Esment
account. Nothing is sent to any other destination. See the [privacy policy](https://esment.notas.ai/privacy).

## Requirements

An Esment account — sign up at https://esment.notas.ai.

## Example prompts

- "What do you know about my current projects?"
- "Remember that I prefer short answers with code first."
- "Turn my latest voice note into memories."

## Support

https://github.com/NotasHQ/esment-plugins/issues

## License

MIT — see `LICENSE`.
