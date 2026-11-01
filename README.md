# TwinStub plugin for Claude Code

When Claude Code writes a webhook handler, it usually checks the happy path
and moves on. This plugin lets it test the cases that break integrations
in production: the same event delivered twice, `completed` arriving before
`pending`, a missing or wrong signature, a chargeback 30 days later. It
works for any provider you can describe in YAML, not only the ones with a
ready-made emulator, and it controls event order and time.

The plugin adds the [TwinStub](https://github.com/twinstub/twinstub) MCP
server and a skill, `webhook-edge-cases`, that tells Claude when to use it
and how to judge the result: by the state of your app, not by HTTP 200s.

## Install

1. Install the TwinStub engine, version 1.1.0 or newer:

   Download a binary from the
   [releases page](https://github.com/twinstub/twinstub/releases) and put
   it on your PATH, or build it with Go:

   ```sh
   go install github.com/twinstub/twinstub/cmd/twinstub@latest
   ```

   `twinstub version` should print 1.1.0 or later. `go install` puts the
   binary in `$(go env GOPATH)/bin`, which is often not on PATH; if
   `/mcp` in Claude Code shows the twinstub server as failed, that is the
   usual reason.

2. Add the plugin in Claude Code:

   ```
   /plugin marketplace add twinstub/claude-plugin
   /plugin install twinstub@twinstub
   ```

That is all. The plugin starts `twinstub mcp` from your PATH. If an engine
already runs on `localhost:9090` (`twinstub serve`), it uses that one;
otherwise it starts its own with `twinstub.yaml` from the project, or an
empty one that Claude fills.

### Without the binary: Docker

Run the engine with Docker and use the server definition in
[extras/docker.mcp.json](extras/docker.mcp.json): copy it into your
project's `.mcp.json` instead of installing the plugin's server.

```sh
docker run --rm -v "$PWD:/work" -p 8080:8080 -p 9090:9090 ghcr.io/twinstub/twinstub:1.1.0 serve
```

The MCP server in the container reaches that engine through
`host.docker.internal`.

## Try it

Ask Claude something like:

> Write a webhook handler for Acme Pay payment events in this service and
> test duplicate and out-of-order delivery with TwinStub.

Claude models the provider in a scenario, writes a table of "events in
this order, expected state of our app", then delivers events out of order,
redelivers them, breaks signatures and moves time forward, and checks what
your app did.

`examples/agent-demo` in the main repository is a small Go service with two
deliberate webhook bugs; both answer 200, so only a check of the app's
state finds them.

## Tools

| Tool | What it does |
|---|---|
| `twinstub_status` | which engine is used and the port your app must call |
| `twinstub_list_scenarios` | states, rules, webhooks, delays, targets |
| `twinstub_validate_config` / `twinstub_load_config` | check and load scenario YAML |
| `twinstub_start_session` / `twinstub_get_session` | start a session, read its state and history |
| `twinstub_trigger` | move a session to a state |
| `twinstub_deliver` | send queued webhooks now, in a chosen order |
| `twinstub_advance_time` | move a session's clock forward, `30d` included |
| `twinstub_list_deliveries` | what was sent and how your app answered |
| `twinstub_replay_delivery` | resend: `redeliver`, `duplicate`, `bad_signature`, `no_signature`, `as_is` |
| `twinstub_explain_miss` | why a request to TwinStub got a 404 |
| `twinstub_reset` | clean slate between test runs |

Details: [docs/mcp.md](https://github.com/twinstub/twinstub/blob/main/docs/mcp.md).

## Good to know

- `twinstub_advance_time` moves TwinStub's clock, not your app's. If your
  app has its own time rules (refund windows, expiry), they need an
  injectable clock to be tested the same way.
- A scenario is only as accurate as what you write in it. Check response
  shapes against the provider's documentation once.
- Plugin version 1.1.0 needs engine 1.1.0 or newer. `twinstub_status`
  says so if the engine is older.

## License

MIT, like the engine. Both are free to use, including commercially. See
[LICENSE](LICENSE) and [PRIVACY.md](PRIVACY.md).
