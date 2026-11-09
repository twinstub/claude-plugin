# Security Policy

## Supported versions

The latest version on `main` is supported. Fixes land there and reach
installed copies through the plugin's normal updates.

## Reporting a vulnerability

Please report security issues **privately** - do not open a public issue,
pull request, or discussion for anything you believe is exploitable.

Use GitHub's private vulnerability reporting:

1. Go to the [Security tab](https://github.com/twinstub/claude-plugin/security).
2. Click **Report a vulnerability**
   ([direct link](https://github.com/twinstub/claude-plugin/security/advisories/new)).
3. Describe the issue and how to reproduce it.

This routes the report straight to the maintainers through GitHub, so no
email address is involved.

A useful report includes the plugin version, the `twinstub version` output,
steps to reproduce, the impact you observed, and any suggested fix.

## What to expect

- We aim to acknowledge a report within a few days.
- We will confirm the issue, work on a fix, and keep you updated.
- Once a fix is released we will publish a security advisory and credit you
  (unless you prefer to stay anonymous).

## Scope

This repository holds the plugin: the skill, the MCP server definition and
the docs. The MCP server and the engine it starts are the `twinstub` binary
from [twinstub/twinstub](https://github.com/twinstub/twinstub); report
issues in them there, or here if you are not sure, and we will route them.

In scope and worth reporting: the plugin starting anything other than
`twinstub mcp`, the skill leading Claude to send data anywhere other than
the user's own scenario targets, an engine started by the plugin reachable
from another machine, and anything that contradicts
[What this plugin runs and sends](README.md#what-this-plugin-runs-and-sends)
or [PRIVACY.md](PRIVACY.md).
