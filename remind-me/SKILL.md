---
name: remind-me
description: Summarize recent code changes to remind you what was done. Looks at git log, finds the last date code was changed, and summarizes all commits in the 3 days leading up to (and including) that date, broken down by day. Ends with suggestions for next steps.
disable-model-invocation: true
user-invocable: true
---

# Remind Me: What Was I Working On?

You are helping the user remember what they were working on recently. Follow these steps exactly:

## Step 1: Find the last change date (LASTDATE)

Run `git log -1 --format="%Y-%m-%d"` to get the date of the most recent commit. Call this **LASTDATE**.

## Step 2: Compute the 3-day window

Calculate **STARTDATE** = LASTDATE minus 2 days (so you cover LASTDATE, LASTDATE-1, and LASTDATE-2 — 3 days inclusive).

## Step 3: Get all commits in that window

Run `git log --after="STARTDATE_MINUS_1" --before="LASTDATE_PLUS_1" --format="%H %ai %s" --reverse` to get all commits in the 3-day window (adjust the boundary dates so the range is inclusive of both STARTDATE and LASTDATE).

Also run `git log --after="STARTDATE_MINUS_1" --before="LASTDATE_PLUS_1" --stat --reverse` to see which files were changed in each commit.

## Step 4: For each day with commits, summarize

Group the commits by date. For each day (in chronological order), produce a summary:

### [DATE] — [Day of Week]

- List each commit with a brief description of what it did
- Mention key files that were changed
- Note the overall theme/goal of that day's work if apparent

If a day in the 3-day window has no commits, skip it (don't mention it).

## Step 5: Check for uncommitted work

Run `git diff --stat` to check for uncommitted changes on tracked files. If there are any, also run `git diff` to see the actual changes.

If uncommitted changes exist, add a final section **after** the per-day summaries:

### Uncommitted Work (in progress)

- Summarize what the uncommitted changes are doing (which files, what kind of changes)
- Note whether they look like work-in-progress on the same theme as recent commits, or something new

If there are no uncommitted changes, skip this section.

## Step 6: Read PROJECT_LOG.md (if it exists)

Check if `PROJECT_LOG.md` exists. If so, read it for additional context about project goals and next steps. Use this to inform your suggestions.

## Step 7: Suggestions for next steps

End the report with a section:

### Suggested Next Steps

Provide 4-6 concrete suggestions, organized into these categories:

1. **Continue current work** — What seems unfinished or partially done based on the recent commits?
2. **Code quality / Refactoring** — Any patterns in the recent changes that suggest cleanup opportunities?
3. **Testing** — What new or changed code lacks test coverage? What tests should be added or updated?
4. **Other** — Any other observations (docs, config, dependencies, etc.)

Base suggestions on actual code changes you observe, not generic advice. Read changed files if needed to make specific suggestions.

## Formatting

Use clear markdown formatting. Keep the report concise but informative — aim for a quick 2-minute read that gets the user back up to speed.
