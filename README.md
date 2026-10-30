# Togo plugins

The Togo plugin gives your coding agent Togo's product analytics: it sets Togo up in your app —
one project per product, a write key per system, events checked before they ship — and answers
questions and writes reports from the data.

## Install

Claude Code:

```
claude plugin marketplace add togohq/plugins
claude plugin install togo@togohq
```

Then start a new session, run `/mcp`, choose `togo` and sign in. Ask your agent to "add Togo
analytics to this app".

## What is in it

- **The Togo MCP server** at `https://mcp.togohq.ai/mcp`, signed in through your browser.
- **`togo-install`** — sets Togo up in this repository and proves events arrive.
- **`togo-analytics`** — answers questions from your data: trends, funnels, people, your event
  registry.
- **`togo-report`** — writes a report for a period, with the range and freshness under every number.

## Support

Write to hello@togohq.ai. Security issues: see SECURITY.md.

## License

Apache-2.0.
