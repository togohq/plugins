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
   properties with `update_person_property`, before the code; the
   SDK installed with this repository's package manager, `identify()` and profile properties, and
   events the app really produces — never `test_event`. If this system's language has no Togo SDK,
   say so plainly and stop; never hand-roll an HTTP client.
5. **Prove it.** Run each flow with the test key, call the SDK's `flush()`, then check exactly
   what that run sent. If `track()`, `identify()` and `setPersonProperties()` returned event ids,
   pass them to `verify_tracking_by_event_ids`. Otherwise call `verify_tracking` with
   `filter.deviceId` (the SDK's `getDeviceId()` on the device you ran) and `expectedEventNames`
   (and `expectIdentify`, `expectProfileProperties`). Each event comes back with its diagnostics.
   You are done only when `errorCount`, `warningCount` and `missingCount` are all 0 — otherwise
   apply each diagnostic's `agentFix`, run the flow again and check the new run. If you cannot run
   the app, ask the person to click through the flows once.
6. **Leave the note.** The setup instructions end with a short "Togo analytics" section; fill it
   in and write it to this repository's agent-instructions file — `AGENTS.md`, or the one file
   your client reads when that is the only one; create `AGENTS.md` when there is none — updating
   the section if it is already there. It is how the next feature's author knows to write event
   definitions first.
7. **After the person deploys,** tell them to watch their first live events arrive in the
   dashboard — Events → Live View, then Production. The MCP tools check test-key events only.
8. **Report the install.** Before your final message — in this session, without waiting for the
   deploy in step 7 — call `give_feedback` once, with category
   `install_report` and the project's id: anything that failed or misled you, any workaround you
   had to invent, and the one capability you wished for. Send it even if everything worked, and
   say so — severity is the worst thing that happened, `low` if nothing went wrong. No personal
   data. Then tell the person in one line that you sent it.

Write keys are public and can only send events. Never ask the person for a secret, an admin key or
an API token. Finish by telling them, in plain words, which event definitions you wrote, which keys each system uses, and
anything you created or renamed, and which file got the note. The setup instructions are for you; never call them "the
playbook" to the person.
