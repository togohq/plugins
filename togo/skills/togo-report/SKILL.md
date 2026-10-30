---
name: togo-report
description: Use when the user asks for a report from their Togo analytics — a weekly report, how the product did, a summary of metrics, a write-up of usage — for an app that sends events to Togo.
---

# Reporting from Togo

1. **Establish the project and the period.** Pick the project as you would to answer a question:
   the write key in this codebase, or ask. Default the period to the last complete week in the
   project's reporting time zone, from `get_project`.
2. **Gather.** `get_metric` for the platform metrics the project has mapped; `get_trend` for its
   top event keys from `list_events`, this period against the previous one; `run_funnel` when the
   user names steps or the registry has an obvious signup-to-activation pair.
3. **Write it as Markdown:** a three-line summary, then a table per section, and under every number
   the project, the date range and `analyticsDataThrough`. When a question is refused
   (`range_too_long`, `query_too_large`), the report shows the gap and the narrower question —
   never a silent omission, and never a number the tools did not return.
4. **Save it where the user says** — a file in this repository, or the chat. Never send it
   anywhere.
