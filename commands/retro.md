---
description: "Retrospective on recent sessions - analyze patterns, improve skills/agents, capture learnings"
argument-hint: "[last N | session-id | skill <skill-name>]"
---

# Session Retrospective

You are conducting a retrospective on recent Claude Code sessions. Your primary purpose is to help the user **iteratively improve their skills, agents, and workflows** through conversational analysis of what happened in past sessions.

## Input Parsing

Arguments: `$ARGUMENTS`

Parse the arguments:
- **No arguments / `last`** → Analyze the most recent session for the current project
- **`last N`** (where N is 2-5) → Analyze the N most recent sessions. Cap at 5, never analyze all.
- **A UUID** → Analyze that specific session
- **`skill <name>`** → Jump straight to improving a specific skill (skip to Phase 3 with that focus)

## Phase 1: Session Discovery & Analysis

**Goal**: Read and understand what happened in the session(s).

### Step 1: Extract session data using the helper script

The `retro-extract` script in `~/.dotfiles` does the heavy lifting of parsing large `.jsonl` transcripts. Use it via `uv run` from the dotfiles directory.

**Determine the correct command based on parsed arguments:**

- Default / `last`: `uv run --project ~/.dotfiles python -m scripts.retro_extract "$(pwd)" --last 1`
- `last N`: `uv run --project ~/.dotfiles python -m scripts.retro_extract "$(pwd)" --last N`
- UUID: `uv run --project ~/.dotfiles python -m scripts.retro_extract "$(pwd)" --session-id <UUID>`
- To list available sessions: `uv run --project ~/.dotfiles python -m scripts.retro_extract "$(pwd)" --list`

**Run the appropriate command via Bash** and read the output. The script returns a condensed markdown summary with:
- Session metadata (branch, CLI version, turns, errors)
- Tool usage distribution
- Commands/skills invoked
- Agents/tasks launched
- Git commits
- User messages (conversation flow)

### Step 2: Summarize findings to the user

Based on the script output:
- What the session(s) accomplished
- Which skills/agents were used and how
- Notable patterns (good or problematic)

## Phase 2: Retrospective Analysis

**Goal**: Identify what worked, what didn't, and what could improve.

Present findings conversationally, organized as:

### What Went Well
- Smooth workflows, effective skill usage, good outcomes

### What Could Be Better
- Friction points, repeated manual steps, places where Claude got stuck
- Skills/agents that were invoked but didn't fully deliver
- Patterns that suggest a missing skill or agent

### Skill/Agent Effectiveness
For each skill or agent used in the session:
- **Was it invoked correctly?** (right trigger, right arguments)
- **Did it produce the desired outcome?** (or did the user have to course-correct)
- **What was missing or excessive?** (gaps in the prompt, unnecessary steps)

### Workflow Observations
- Steps that could be automated or streamlined
- Recurring patterns that suggest a new command or agent
- CLAUDE.md or memory updates worth capturing

**After presenting, ask the user**: "What stands out to you? Is there a specific skill or pattern you want to dig into?"

## Phase 3: Skill Improvement (Primary Focus)

**Goal**: Conversationally iterate on a specific skill or agent.

This is the core of `/retro`. When the user identifies a skill to improve (or when invoked with `skill <name>`):

1. **Read the current skill/agent file**:
   - Commands: `~/.claude/commands/<name>.md`
   - Agents: `~/.claude/agents/<name>.md`
   - Plugin commands: search in `~/.claude/plugins/cache/`

2. **Analyze against session evidence**:
   - How was it actually used in the session(s)?
   - Where did the skill's instructions lead to good outcomes?
   - Where did the instructions fail, cause confusion, or miss the mark?
   - What did the user have to manually correct or supplement?

3. **Propose specific improvements conversationally**:
   - Don't dump a full rewrite. Start with the highest-impact change.
   - Explain **why** based on session evidence: "In your session, X happened because the skill says Y. If we change it to Z, that friction goes away."
   - Ask if the user agrees before making changes.
   - Iterate: make one change, discuss, make the next.

4. **When the user approves changes**, edit the skill file directly.

5. **After editing, ask**: "Want to keep improving this one, or look at something else?"

### Improvement Categories

When analyzing a skill, consider:

- **Missing instructions**: Things the skill should say but doesn't
- **Overly rigid instructions**: Steps that should be flexible or conditional
- **Wrong sequencing**: Phases or steps in the wrong order
- **Missing context**: Information the skill needs but doesn't gather
- **Scope creep**: The skill tries to do too much
- **Unclear triggers**: Description or argument-hint that doesn't match actual usage
- **Tool access**: Does the skill need tools it doesn't have? (`allowed-tools`)

## Phase 4: Broader Improvements

**Goal**: Capture learnings beyond individual skills.

After skill-level improvements, briefly address:

### New Skill/Agent Suggestions
If session analysis reveals a pattern not covered by existing skills:
- Describe what the new skill would do
- Explain the evidence from sessions
- Offer to create a draft
- Ask whether it should be a command or agent (commands for user-invoked workflows, agents for background/delegated analysis)

### CLAUDE.md / Memory Updates
If the session revealed learnings worth persisting:
- Propose specific additions to CLAUDE.md or memory files
- Focus on gotchas, patterns, or workflow preferences
- Only suggest genuinely useful additions (not noise)

### Workflow Pattern Suggestions
If sessions reveal repeated multi-step workflows:
- Suggest combining steps into a skill
- Identify manual steps that could be automated

## Conversation Style

- **Be conversational, not report-like.** This is a dialogue, not an audit.
- **Lead with evidence.** Every suggestion should reference what actually happened.
- **One thing at a time.** Don't overwhelm with 10 suggestions. Focus on the highest-impact item, resolve it, then move on.
- **Ask before editing.** Never modify a skill file without user confirmation.
- **Be opinionated but flexible.** State what you think the best change is, but defer to the user's judgment.

## Important Constraints

- **Never analyze ALL sessions.** Maximum 5 at a time.
- **Session transcripts can be large.** Focus on extracting the key signals (user messages, command invocations, friction points) rather than reading every tool call verbatim.
- **Don't fabricate session data.** If you can't find or read a transcript, say so.
- **Respect the user's time.** If a session was straightforward with no issues, say "this session went smoothly, nothing jumps out" and ask if they want to focus on a specific skill anyway.
