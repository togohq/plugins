# Changelog

All notable changes to the Togo plugin. The format follows Keep a Changelog, and the plugin uses
semantic versioning.

## 1.6.1

- `togo-install` and `togo-analytics` name where each SDK is set up, not only `init()`: `init()` in
  browser and Node.js code, `TogoClientOptions` in .NET, and `TogoConfig` passed to `Togo.setup()`
  in an iOS or Android app. `togo-install` passes `onError` there, and `togo-analytics` finds the
  project's write key there.

## 1.6.0

- Both skills name the renamed property tools: `list_properties` (event or person scope) in place
  of `list_person_properties`, and `update_property_definitions` in place of
  `update_person_property`.
- `togo-install` and `togo-analytics` say what a definition should hold for the next analyst: an
  event's description (what happened, where, before and after, how often, good or bad), a
  property's description (meaning and unit, its values, whether it identifies someone, whether to
  break down by it), when a property is `required`, and when it needs a per-event `note`. A
  property is defined once and listed by name on every other event that sends it.
- `togo-install` ends with `list_tracking_issues` once production traffic is curated, and
  `togo-analytics` checks it when a number looks wrong: what real users' devices sent wrong in the
  last 7 days, with platform, app version and a fix. `verify_tracking` stays the check for a test
  run, now.

## 1.5.0

- `togo-analytics` names events and properties exactly as the app sends them — case, spaces and
  punctuation included — as `list_events` and `get_event` give them. "Plan Type" and "plan_type" are
  two properties until someone merges them.
- `togo-analytics` suggests merges and never makes them unasked: a name marked `looksLike` is put
  to the person with its evidence, then merged with `merge_events` or the new `merge_properties`
  only on a yes.
- `togo-install` proves tracking by calling `flush()` from the test run, never by adding it to the
  app's code, and says that a browser test intercepting requests (Playwright `page.route` or
  `context.route`) holds the batch sent as a page is left — call `page.unrouteAll()` before that
  click.
- `togo-install` asks feedback to separate what was seen from what was concluded: the test tool and
  what it intercepted, the browser or runtime, the SDK version, and whether the problem remains
  without the workaround.

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
