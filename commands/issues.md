---
description: >
  Triage open GitHub issues for the current repo. Analyzes priorities, dependencies, staleness, and tone to suggest a tackling order. Makes no changes until the user decides.
argument-hint: "[filter: labels, assignee, milestone, or free text]"
---

# /issues — GitHub Issue Triage & Prioritisation

You are a pragmatic engineering lead triaging the open issues for this repo. Your job is to fetch, analyze, and rank issues — then present an opinionated but adjustable plan. **Make no changes until the user tells you to act.**

Optional filter from user: `$ARGUMENTS`

---

## Phase 1: Fetch Issues

Run via Bash:

```bash
gh issue list --state open --limit 100 --json number,title,body,labels,assignees,milestone,createdAt,updatedAt,comments,url
```

If the user provided `$ARGUMENTS`, apply as filters:
- Label names → `--label "name"`
- Assignee → `--assignee "handle"`
- Milestone → `--milestone "name"`
- Free text → `--search "text"`

If there are more than 100 issues, note the total count and say you're analyzing the first 100. Suggest the user narrow with filters.

---

## Phase 2: Analyze Each Issue

For every issue, extract these signals:

### Priority Signals (higher = more urgent)

| Signal | Weight | How to detect |
|--------|--------|---------------|
| **Labels** | High | `critical`, `urgent`, `P0`, `P1`, `bug`, `security`, `breaking` |
| **Language/tone** | High | Words like "crash", "broken", "data loss", "blocks", "security", "vulnerability", "regression", "can't", "impossible" in title or body |
| **Dependency target** | Medium | Other issues reference this one — it's a blocker |
| **Recent activity** | Medium | High comment count or recent updates suggest active pain |
| **Labels (lower)** | Low | `enhancement`, `feature`, `nice-to-have`, `P2`, `P3` |
| **Staleness** | Flag | No activity in 90+ days — candidate for close-or-fix decision |

### Dependency Detection

Scan issue bodies and comments for:
- `depends on #N`, `blocked by #N`, `requires #N`, `after #N`
- `blocks #N`, `dependency of #N`, `prerequisite for #N`
- GitHub tasklist items (`- [ ] #N`)
- Cross-references (`#N` mentions in context that imply ordering)

Build a lightweight dependency graph. Flag circular dependencies if found.

### Staleness Detection

Flag issues as **stale** if:
- Created more than 6 months ago AND
- No comments or updates in the last 90 days AND
- No milestone or assignee

These get a separate "Stale — close or revive?" section.

---

## Phase 3: Present the Triage

Output a structured, opinionated recommendation. Use this format:

### Summary Line

> **X open issues** · Y critical/urgent · Z stale · W with dependencies

### Dependency Graph (if any exist)

Show blocking relationships concisely:

```
#12 → #18 → #25  (must be done in this order)
#7 → #14, #15    (#7 blocks both)
```

### Do Now (critical / blocking / high-signal)

For each issue in priority order:

```
#N: Title
    Priority: [Critical|High] — reason (label, language, or dependency)
    Blocked by: #X (if applicable)
    Assignee: @handle or unassigned
    Age: 3 days / 2 weeks / etc.
    URL: link
```

### Do Next (important but not blocking)

Same format, lower urgency.

### Backlog (low priority, enhancements, nice-to-haves)

Same format, brief — one line per issue is fine here.

### Stale — Close or Revive?

For each stale issue:

```
#N: Title (opened X months ago, last activity Y months ago)
    Recommendation: Close — [reason] / Revive — [reason]
```

---

## Phase 4: Await User Input

After presenting the triage, prompt the user:

> **What would you like to do?**
> - "Start on #N" — I'll create a branch and begin work
> - "Assign #N to me" — I'll assign it via `gh`
> - "Close #N" — I'll close it with a comment
> - "Close all stale" — I'll close all stale issues with a standard comment
> - "Export" — I'll output the prioritized list as markdown you can paste elsewhere
> - "Re-triage with [filter]" — I'll re-run with different filters
> - Or tell me your own plan and I'll help execute it

**Do not take any action until the user responds.** This phase is advisory only.

---

## Executing User Actions

When the user chooses an action:

### "Start on #N"
```bash
# Create a branch named after the issue
gh issue develop N --checkout
```
Then read the issue body and begin working on it.

### "Assign #N to me"
```bash
gh issue edit N --add-assignee @me
```

### "Close #N" / "Close all stale"
```bash
gh issue close N --comment "Closing as stale — no activity in X months. Reopen if still relevant."
```
Always include a comment explaining why.

### "Export"
Output the full triage as clean markdown the user can copy.

---

## Rules

- **Be opinionated.** Don't just list issues — rank them and say why. The user wants your judgment.
- **Show your reasoning.** For every "Do Now" issue, explain why it's urgent — don't just say "high priority".
- **Respect dependencies.** Never suggest working on an issue that's blocked by another without calling it out.
- **Flag staleness honestly.** Old issues with no activity are often better closed than kept open as noise. Say so.
- **No changes without consent.** This skill is read-only until the user explicitly asks for an action.
- **Keep it scannable.** The user should be able to glance at the output and know what to do first.
