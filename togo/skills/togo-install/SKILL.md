---
name: togo-install
description: Use when the user wants to add analytics or Togo to an app, track an action such as signups or purchases, connect a new part of their product (a server, a mobile app, a marketing site) to Togo, or check whether tracking works — or when list_projects shows a project with no events yet.
---

# Setting Togo up in this app

The `togo` MCP server does the work; this skill is the order. Do every step yourself and ask the
person only for what needs them: signing in, choosing when you are unsure, and deploying.

1. **Find the product's project.** Call `list_projects` and search this repository for a Togo
   write key (`togo_pkt_…` test, `togo_pkl_…` live). A key in the code names its project.
2. **No key yet: one product is one project.** Its website, web app, mobile apps and server all
   send to the same project, because Togo follows a person across them only inside one. Use this
   product's project if `list_projects` shows one — same name or domain, or this repository is one
   part of a product whose other parts already send. A project with no events and no key in any
   code is the one signup made: use it, and rename it with `update_project`. Call `create_project`
   only when nothing could be this product's, and ask the person first if one might be. Never
   create a project per environment.
3. **Give this system its own write-key pair.** Reuse the pair named for it, or call
   `create_write_key` with the system's name in lowercase kebab-case — `web-app`, `api-server`,
   `marketing-site`, `ios-app`. Never an environment in the name: the pair's test key is for
   development and staging, its live key for production.
4. **Follow the playbook.** Call `get_setup_instructions` for the project and follow it to the end:
   the tracking plan declared with `update_event` and `update_person_property` before the code, the
   SDK installed with this repository's package manager, `identify()` and profile properties, and
   events the app really produces — never `test_event`. If this system's language has no Togo SDK,
   say so plainly and stop; never hand-roll an HTTP client.
5. **Prove it.** Run the app and trigger each flow. `list_live_events` shows the test key's events
   within seconds, as sent; `verify_tracking` checks them against the plan. You are done only when
   `verify_tracking` says `allClear` — otherwise fix what it names and repeat. If you cannot run the
   app, ask the person to click through the flows once.
6. **After the person deploys,** `list_live_events` with `stream` "production" confirms the first
   live events.

Write keys are public and can only send events. Never ask the person for a secret, an admin key or
an API token. Finish by telling them what you declared, which keys each system uses, and anything
you created.
