---
name: webhook-edge-cases
description: Use when writing or changing code that handles webhooks or
  integrates with an event-driven third-party API (payments, KYC, logistics,
  CRM), especially a provider whose sandbox cannot trigger rare events on cue.
  Tests orderings, delays, redeliveries and bad signatures with TwinStub.
---

# Testing webhook edge cases with TwinStub

If the twinstub_* tools are unavailable, say this needs Claude Code with the
twinstub binary on PATH and stop.

1. Call twinstub_status. Note `mode` and `public_port`: the app under test
   must send its provider API calls to that port (`app_base_url`).
   Engine not reachable -> tell the user how to start it and stop.
2. Model the provider. Confirm response shapes against the provider docs
   or sandbox once. Write or update the scenario YAML, run
   twinstub_validate_config until clean, then twinstub_load_config.
   Use {{ .Webhook.At }} for event timestamps in delayed webhooks.
3. Write the spec BEFORE running anything: a table of
   "events in this order -> expected end state in OUR app".
   Always include rows for:
   - redelivery of the same event (replay mode redeliver)
   - out-of-order delivery (twinstub_deliver: completed before pending)
   - invalid and missing signature (must be rejected)
   - delayed reversal (chargeback or refund days later: advance_time)
   - retry after our handler returns 5xx
4. Point the app's provider base URL at TwinStub, and the scenario's
   webhook target URL at the app's webhook endpoint.
5. For each row: twinstub_start_session -> twinstub_trigger /
   twinstub_deliver / twinstub_advance_time /
   twinstub_replay_delivery (wait: true) -> twinstub_list_deliveries.
6. Assert the app's resulting state: DB record, API response, what the
   user can access. A 2xx on every delivery proves nothing.
7. Do not snapshot whole responses. A snapshot records current behavior,
   bugs included. The table is the spec.
8. On a 404 from TwinStub, call twinstub_explain_miss before editing YAML.
9. When a real production failure is reported, add it as a scenario row.
10. Call twinstub_reset between independent test runs.

advance_time moves TwinStub's clock, not the app's. If the app has its own
time-based logic (refund windows, expiry), it needs an injectable clock;
say so instead of claiming it was tested.

TwinStub is only as accurate as the scenario. If the provider docs and
the YAML disagree, trust the docs and fix the YAML.
