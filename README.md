# Go Bananas plugins

Use [Go Bananas](https://gobananasai.com) from your coding agent: generate and edit images, keep
characters and products consistent across scenes, and make posters. Everything is saved to your
Go Bananas gallery.

The plugin adds the Go Bananas MCP server (`https://mcp.gobananasai.com`, OAuth sign-in), a
skill that tells your agent how to use it well, and a `start` skill for your first image.

## Claude Code

Run these one at a time: send the first, wait for it to finish, then send the second.

1. Add the marketplace:

   ```
   /plugin marketplace add davendra/go-bananas-plugins
   ```

2. Install the plugin:

   ```
   /plugin install go-bananas@go-bananas
   ```

Then run `/mcp`, choose **go-bananas** and sign in.

## Claude app (claude.ai, Desktop, mobile)

The plugin adds instructions only. To generate images, add the connector: **Settings → Connectors →
Add custom connector**, URL `https://mcp.gobananasai.com`.

## Codex

Run these one at a time:

1. Add the marketplace:

   ```
   codex plugin marketplace add davendra/go-bananas-plugins
   ```

2. Install the plugin:

   ```
   codex plugin add go-bananas@go-bananas
   ```

Sign in to Go Bananas when Codex asks.

## First image

Once you are signed in, ask for your first picture:

- Claude Code: run `/go-bananas:start`.
- Codex: ask "Use $start", or just "What can Go Bananas do?"

It says how many images you have left this month, offers three ready-made starters and makes the
one you pick. The image is saved to your Go Bananas gallery.

## Other agents

See [gobananasai.com/connect](https://gobananasai.com/connect) for Claude, ChatGPT, Cursor, VS Code,
Gemini CLI and the command-line tool.

## Support

ai@3rdeye.co.uk
