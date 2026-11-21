---
name: togo-analytics
description: Use when the user asks a product-analytics question about an app that sends events to Togo — how many, how often, which events, funnels, conversion, retention of a step, who a user is — or asks to define, describe or merge events or person properties in Togo (an event definition: what an event means and when it fires).
---

# Answering with Togo

The `togo` MCP server reaches every Togo project the signed-in account can access. Every tool but
`list_projects` and `create_project` needs a `projectId`.

1. **Pick the project from the code, never by guessing.** Call `list_projects`, then search this
   codebase for the write key passed to the Togo SDK's `init()`. A key belongs to exactly one
   project — use that project's id. If no key matches, or there is no code to search, ask the user
   which project they mean.
2. **Name events and properties exactly as the app sends them**, as `list_events` and
   `get_event` give them — case, spaces and punctuation included: "Plan Type" and "plan_type" are
   two properties until someone merges them. Use `get_event` for the properties you can filter or
   break down by, and the names merged into each.
3. **Respect freshness.** Analytics data ends at `analyticsDataThrough`, curated every few hours.
   For "today" or "the last hour", say the data does not cover it yet rather than reporting zero.
   `verify_tracking` and `verify_tracking_by_event_ids` check what a test run sent with the test
   key, and never answer a question about how many users or events there were.
4. **Read refusals.** A refusal carries a code and often a narrower question to ask instead; ask
   that rather than retrying the same call. `project_not_reachable` lists the projects the account
   can see.
5. **Writes are real.** `update_event_definitions`, `merge_events`, `merge_properties`,
   `update_person` and `update_person_property` change the project for everyone, and a merge cannot
   be undone: confirm with the user before `merge_events` or `merge_properties`.
6. **Suggest merges, never make them unasked.** A name marked `looksLike` in `list_events`,
   `get_event` or `list_person_properties` is probably the same event or property spelled
   differently — "Plan Type" from the web and "plan_type" from the server. So is a typo the
   registry cannot see, such as "playn_type" beside "plan_type". Put the merge to the user with
   the evidence — both names, the events that carry each, how much traffic each has — and merge
   only on a yes.

Every answer names the project and what it covered; repeat both when you report numbers.
