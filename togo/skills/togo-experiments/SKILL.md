---
name: togo-experiments
description: Use when the user wants to A/B test something in an app that sends events to Togo — test two versions of a page, button, flow or copy, split users between variants, or check how an experiment is set up — or asks to start, finish or change one.
---

# Running an experiment in Togo

An experiment is one immutable test: a name, its variants, who counts as one subject, and a
participation rate. The `togo` MCP server defines it, the SDK assigns it, and the person keeps
control of the two irreversible steps. Do not hand-roll a split with a random number or a
cookie: Togo decides, deterministically, and that is what makes the result readable.

1. **Define it** with `create_experiment`, as a draft. The unit has no default and is frozen
   when it starts, so choose it by when the id exists: `anonymous_id` when the flow runs before
   sign-in, `user_id` when a `userId` is expected at the call, `device_id` when the device itself
   is the subject. Never "user id if we have one, otherwise anonymous": one person would then be
   two subjects.
2. **Write the branch** with the SDK's `getExperiment()`, using the snippet from the tool's answer.
   Every branch keeps a default path, because `variant` is `null` when a client gets no answer:
   `if (result.variant === 'b') renderNew(); else renderCurrent();`
3. **Call it where the code branches**, never in a layout or a component mounted on every page.
   The first call records the person as exposed, so calling it early counts people who never saw
   the change.
4. **Test each variant** under the test key. Force one with `set_experiment_test_variant`, run the
   flow, and call the SDK's `flush()`. Then `verify_tracking` with `filter.deviceId` and
   `expectExperiments` shows the assignment each request received; `$experiment_entered` is the
   SDK's own event, so do not define it or send it. Clear the forced variant when you finish.
   A test key skips the SDK's cache and records one entry per experiment and variant, so a test
   run shows more requests and entries than production will. That is expected.
5. **Start it, as the last step of this same task.** Starting is safe before the code ships:
   an experiment with no callers simply has no exposures yet. Do not wait for a deploy you cannot
   see, or leave it for later: a draft answers production with not found and everyone silently
   takes the default path. Confirm with the person once, then call `start_experiment`. If they
   say not to start it, tell them plainly that it has not been started.
6. **Raise participation** with `set_experiment_participation` as confidence grows. Participation
   is the only part of a running experiment that can change.
7. **Read how it is going** with `get_experiment_results`: participants per variant, the
   sample-ratio check, and the primary metric's conversion per variant with an interval. A
   sample-ratio mismatch means the comparison cannot be trusted yet; say so before any number.
   Repeat its caveat beside any interval, and never call a winner from one look: the person
   decides. For any other event by variant, `get_trend` with breakdown `experiments.<name>`.
8. **Finish it** with `finish_experiment`, naming the winner; the baseline is the way to abandon.
   Finishing is irreversible and answers every later call with the winner, so code keeps working
   until someone removes the branch.

`list_experiments` shows every experiment, including one production is already calling while it
is still a draft: that is the sign someone forgot step 5. `get_experiment_assignment` is a dry run
of what any unit would get.
