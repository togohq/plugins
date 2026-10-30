---
name: togo-analytics
description: Use when the user asks a product-analytics question about an app that sends events to Togo — how many, how often, which events, funnels, conversion, retention of a step, who a user is — or asks to declare, describe or merge events or person properties in Togo.
---

# Answering with Togo

The `togo` MCP server reaches every Togo project the signed-in account can access. Every tool but
`list_projects` and `create_project` needs a `projectId`.

1. **Pick the project from the code, never by guessing.** Call `list_projects`, then search this
   codebase for the write key passed to the Togo SDK's `init()`. A key belongs to exactly one
   project — use that project's id. If no key matches, or there is no code to search, ask the user
   which project they mean.
2. **Name events by registry key** (`snake_case`, from `list_events`), never by display name. Use
   `get_event` for the properties you can filter or break down by.
3. **Respect freshness.** Analytics data ends at `analyticsDataThrough`, curated every few hours.
   For "today" or "the last hour", say the data does not cover it yet rather than reporting zero.
   `verify_tracking` and `list_live_events` read test traffic — use them to check tracking, never
   to answer a question about real users.
4. **Read refusals.** A refusal carries a code and often a narrower question to ask instead; ask
   that rather than retrying the same call. `project_not_reachable` lists the projects the account
   can see.
5. **Writes are real.** `update_event`, `merge_events`, `update_person` and
   `update_person_property` change the project for everyone, and a merge cannot be undone: confirm
   with the user before `merge_events`.

Every answer names the project and what it covered; repeat both when you report numbers.
