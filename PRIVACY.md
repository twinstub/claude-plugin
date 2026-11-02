# Privacy

The TwinStub plugin runs entirely on your machine.

- It collects no data, has no telemetry, checks for no updates and sends
  nothing to the TwinStub authors or anyone else.
- Its MCP server (`twinstub mcp`) talks only to the TwinStub engine: one
  it starts on your machine, one you run on localhost:9090, or the admin
  API at the address you set with `--admin-url` or `TWINSTUB_ADMIN_URL`.
  It runs no shell commands.
- An engine it starts listens on this machine only (127.0.0.1 and ::1).
- The engine sends webhooks only to the target URLs written in your
  scenario files, and answers only the requests your app makes to it.
- It reads `twinstub.yaml` in your project, or the config file you or
  Claude pass to it, the files that config includes, and the environment
  variables it references. Inline YAML given to `twinstub_validate_config`
  goes to a temporary file that is deleted right after.
- Nothing is stored beyond the engine's in-memory sessions, delivery
  journal and 404 log, which end on `twinstub_reset` or when the engine
  stops.

Questions: open an issue at https://github.com/twinstub/claude-plugin/issues.
