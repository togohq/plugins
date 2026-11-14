# Changelog

All notable changes to the Togo plugin. The format follows Keep a Changelog, and the plugin uses
semantic versioning.

## 1.4.0

- `togo-install` wires the SDK's `onError` into the app's own error reporting when it has one
  (Sentry, Bugsnag, Datadog, its own logger), so every event the SDK will not record is reported, in
  production too.
- `togo-install` says that an event the SDK dropped before sending never reaches `verify_tracking`,
  and that the console, like `onError`, names it with its code.

## 1.3.0

- A fourth skill, `togo-experiments`, to run an A/B experiment: define it as a draft with
  `create_experiment`, write the branch with the SDK's `getExperiment()` (always keeping a default
  path for a `null` variant), test each variant under the test key, start it with
  `start_experiment` as the last step of the same task after confirming once with the person, read
  it with `get_experiment_results`, and finish it only when the person names the winner.
- `togo-report` cites each number by its answer link when the tool returned one (`answer.url`), and
  never makes one up. A link back into Togo is not sending the report anywhere; email, messages and
  other services still are.

## 1.2.0

- `togo-install` ends with a step 8: report the install with `give_feedback`, category
  `install_report` — anything that failed or misled the agent, any workaround it invented, and the
  one capability it wished for — sent even when everything worked, then one line to the person.

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
