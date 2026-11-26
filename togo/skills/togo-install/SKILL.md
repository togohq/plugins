---
name: togo-install
description: Use when the user wants to add analytics or Togo to an app, track an action such as signups or purchases, connect a new part of their product (a server, a mobile app, a marketing site) to Togo, or check whether tracking works — or when list_projects shows a project with no events yet.
---

# Setting Togo up in this app

The `togo` MCP server does the work; this skill is the order. Setting Togo up is what the person
asked for, and every step is reversible — picking or renaming the project, its time zone, key pairs,
the tracking plan — so do each one yourself without asking first, and report it at the end. Ask
only for what needs them: signing in, a project that might already be this product's, and
deploying. When you do ask, ask once, every question together, each as 2–4 choices with your
recommendation first and marked "(Recommended)" — with your multiple-choice question tool
when you have one. Never ask for a name you can choose.

1. **Find the product's project.** Call `list_projects` and search this repository for a Togo
   write key (`togo_pkt_…` test, `togo_pkl_…` live). A key in the code names its project.
2. **No key yet: one product is one project.** Its website, web app, mobile apps and server all
   send to the same project, because Togo follows a person across them only inside one. In this
   order:
   - A project that **has received events** and could be this product's — same name or domain, or
     this repository is one part of a product whose other parts already send — is a question to
     the person: "Add this to _Acme_ (Recommended), or a new product?"
   - Otherwise a project that **has never received an event** (`hasReceivedEvents` false) is the
     one signup made: use it, rename it after the product with `update_project`, and set its
     `reportingTimeZone` to this machine's IANA time zone. Don't ask.
   - Otherwise `create_project`, with the machine's time zone. Don't ask.

   Never create a project per environment.

3. **Give this system its own write-key pair.** Reuse the pair named for it, or call
   `create_write_key` with a name you choose — the system in lowercase kebab-case: `web-app`,
   `api-server`, `marketing-site`, `ios-app`. Never an environment in the name: the pair's test key
   is for development and staging, its live key for production. A pair named `Default` is signup's:
   don't use it and don't mention it.
4. **Follow the setup instructions.** Call `get_setup_instructions` for the project and follow it to the end:
   every event's event definition written in one `update_event_definitions` call, and profile
   properties in one `update_property_definitions` call (scope `person`), before the code; the
   SDK installed with this repository's package manager, `identify()` and profile properties, and
   events the app really produces — never `test_event`. If the app already reports errors (Sentry,
   Bugsnag, Datadog, its own logger), pass `onError` where the SDK is set up and send each error
   there — `init()` in browser and Node.js code, `TogoClientOptions.OnError` in .NET, and
   `TogoConfig` passed to `Togo.setup()` in an iOS or Android app: the SDK calls it for every event
   it will not record, in production too. If this system's language has no Togo SDK,
   say so plainly and stop; never hand-roll an HTTP client.

   **What a definition says.** An analyst — a person or an agent — reads it to segment and build
   funnels, so write it for them:
   - An event's `description`: what happened, for the user and for the business, in plain words;
     where in the product it happens (the screen, flow or product area); which event usually comes
     before it and which after; how often one user sends it (once ever, once per visit, or many
     times); and whether more of it is good or bad. Put the exact moment it fires in `firesWhen`,
     not here.
   - A property's `description`: what the value means and its unit; what each value means when
     there is a fixed set; whether it identifies a person or an account; whether it is useful for
     breaking results down or filtering; and what a missing value means.
   - A property is defined once, by name, for every event that sends it. Give `type` and
     `description` the first time; on another event, give only its `name` (with `required` and a
     `note` if they apply).
   - `required: true` only when every send of that event carries the property. A missing required
     property is reported, never dropped, so mark it to catch the bug — not to filter data.
   - A `note` only where the property means something particular on one event — a duration that is
     one step on one event and the whole flow on another. Most properties have none.

5. **Prove it.** Run each flow with the test key, call the SDK's `flush()` from the run (never add
   it to the app's code to make a check pass), then check exactly what that run sent. If `track()`, `identify()` and `setPersonProperties()` returned event ids,
   pass them to `verify_tracking_by_event_ids`. Otherwise call `verify_tracking` with
   `filter.deviceId` (the SDK's `getDeviceId()` on the device you ran) and `expectedEventNames`
   (and `expectIdentify`, `expectProfileProperties`). Each event comes back with its diagnostics.
   You are done only when `errorCount`, `warningCount` and `missingCount` are all 0 — otherwise
   apply each diagnostic's `agentFix`, run the flow again and check the new run. An event the SDK
   dropped before sending never reaches `verify_tracking`: the console names it with its code, as
   `onError` does. A browser test that intercepts requests (Playwright `page.route` or
   `context.route`, for any URL) holds the batch sent as a page is left, so events before a
   navigation go missing: call `page.unrouteAll()` before that click. If you cannot run
   the app, ask the person to click through the flows once.
6. **Leave the note.** The setup instructions end with a short "Togo analytics" section; fill it
   in and write it to this repository's agent-instructions file — `AGENTS.md`, or the one file
   your client reads when that is the only one; create `AGENTS.md` when there is none — updating
   the section if it is already there. It is how the next feature's author knows to write event
   definitions first.
7. **After the person deploys,** tell them to watch their first live events arrive in the
   dashboard — Events → Live View, then Production. `verify_tracking` checks test-key events only.
   Once production traffic has been curated — a few hours after the deploy — call
   `list_tracking_issues`: what real users' devices sent wrong in the last 7 days, the same
   diagnostics with the same `agentFix`, plus how often, from which platform and app version. It is
   production after curation, hours old; `verify_tracking` is a test run, now. Fix what it lists the
   way you fixed the test run, and confirm each fix with `verify_tracking`. If you are still in the
   session, do it then; otherwise say in your final message that it is the check to run after the
   deploy.
8. **Report the install.** Before your final message — in this session, without waiting for the
   deploy in step 7 — call `give_feedback` once, with category
   `install_report` and the project's id: anything that failed or misled you, any workaround you
   had to invent, and the one capability you wished for. For each problem, say what you saw apart
   from what you concluded: how you observed it (the test tool, what it intercepted, the browser or
   runtime, the SDK version) and whether it still happens without your workaround. Send it even if everything worked, and
   say so — severity is the worst thing that happened, `low` if nothing went wrong. No personal
   data. Then tell the person in one line that you sent it.

Write keys are public and can only send events. Never ask the person for a secret, an admin key or
an API token. Finish by telling them, in plain words, which event definitions you wrote, which keys each system uses, and
anything you created or renamed, and which file got the note. The setup instructions are for you; never call them "the
playbook" to the person.
