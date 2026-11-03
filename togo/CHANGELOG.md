# Changelog

All notable changes to the Togo plugin. The format follows Keep a Changelog, and the plugin uses
semantic versioning.

## 1.1.0

- `togo-install` proves a test run with `verify_tracking_by_event_ids` — the event ids the run's
  `track()`, `identify()` and `setPersonProperties()` calls returned — or `verify_tracking` with the
  device's id and the event names it expected, applying each diagnostic's `agentFix` until the
  error, warning and missing counts are all 0.
- **Breaking, shipped as a minor:** `list_live_events` is no longer called — the server replaced
  it with the two `verify_tracking` tools, which check test-key events only — so a `1.0.0` copy's
  install skill names a tool the server no longer has. It is a minor rather than a major because
  no install of `1.0.0` was known; such a copy is told to update. After a deploy, the person
  watches production in the dashboard's Live View.
- `togo-analytics` names both checking tools as test-only.

## 1.0.0

- First release: the Togo MCP server, and three skills — `togo-install` to set Togo up in an app,
  `togo-analytics` to answer questions from the data, and `togo-report` to write a report.
