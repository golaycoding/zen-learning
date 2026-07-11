# How to Set Up a New Skill + Daily Schedule

Reference notes from setting up `daily-reading-worksheet`. Use this as a template for future automations.

## Part 1 — Create the Skill

1. Open the `skill-creator` skill (or Settings > Capabilities > Skills > Create skill) and describe what you want automated.
2. Give it:
   - **Name** — short, kebab-case (e.g. `daily-reading-worksheet`)
   - **Description** — one line stating what it does and when it should trigger (e.g. "Generates the daily 7th-grade reading worksheet from the topic queue. Trigger when asked to make today's reading worksheet.")
3. Write the skill's instructions as a complete, self-contained recipe. Include everything it needs to run with no outside context:
   - Where to read input from (exact file path)
   - The full output format/spec (don't leave this vague — put the actual rules in the skill itself)
   - Where and how to name the output file
   - Any state to update afterward (e.g. remove a used item from a queue file, log it elsewhere)
   - A fallback/warning condition (e.g. "if fewer than 3 topics remain, flag it")
4. Save the skill.
5. **Test it manually** before scheduling — type a trigger phrase in chat and confirm the output and any file updates are correct. Fix the skill instructions, not the schedule, if something's off.

## Part 2 — Schedule It

There is no Settings page for this — scheduling happens by asking Claude to create a scheduled task in chat.

6. Ask directly: "Schedule the `[skill-name]` skill to run every day at [time]."
7. Behind the scenes this uses a cron expression in **local time**, e.g. `40 8 * * *` = 8:40 AM daily.
8. Keep the scheduled task's prompt minimal. If the skill already contains the full instructions, the task prompt only needs to say which skill to run and any path it needs (the skill runs with no memory of this conversation, so it can't infer context). Don't duplicate format/spec details already in the skill — redundant and hard to keep in sync.
9. Confirm the task after creation — check the reported schedule matches what you asked for.

## Known Quirks

- **Dispatch delay is normal.** Recurring tasks get a small deterministic delay (a few minutes) at run time to balance server load. A task set for 8:40 AM may report "next run" closer to 8:50 AM — the cron expression itself is still correct.
- **Connected folders persist across runs.** A folder connected via Cowork stays linked at the app level, not per chat session — so a scheduled task run days later can still read/write files there. The temporary "outputs" scratchpad does not persist this way.
- **Managing tasks:** ask in chat to list, pause, re-enable, or update a scheduled task's time or prompt — no separate UI page for this.

## Worked Example — `daily-reading-worksheet`

- **Skill:** reads the top topic from `reading-topics.md`, generates a formatted worksheet, exports as `.docx`, removes the used topic, logs it to `topics-log.md`.
- **Schedule:** `40 8 * * *` (daily, ~8:40–8:50 AM local time)
- **Task prompt:** "Run the daily-reading-worksheet skill using the connected folder [path]."
- **Delete later:** "Delete later"
