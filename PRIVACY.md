# Privacy

The TwinStub plugin runs entirely on your machine.

- It collects no data, has no telemetry and sends nothing to TwinStub or
  anyone else.
- Its MCP server (`twinstub mcp`) talks only to the TwinStub engine: one
  it starts on your machine, or the admin API at the address you set with
  `--admin-url` or `TWINSTUB_ADMIN_URL`.
- The engine sends webhooks only to the target URLs written in your
  scenario files, and answers only the requests your app makes to it.
- It reads only the config files you or Claude pass to it, and the files
  those configs include. It runs no shell commands.
- Nothing is stored beyond the engine's in-memory sessions and delivery
  journal, which end when the engine stops.

Questions: open an issue at https://github.com/twinstub/claude-plugin/issues.
