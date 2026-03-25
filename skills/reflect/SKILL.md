---
name: reflect
description: Analyze chat history and Claude configuration to suggest targeted improvements for CLAUDE.md files, skills, MCPs, hooks, and permissions.
---

# Reflect: Claude Configuration Analyzer

Analyze chat history and Claude configuration to suggest targeted improvements for CLAUDE.md files, skills, MCPs, hooks, and permissions.

## Phase 1: Discovery & Analysis

### Configuration Discovery

Scan for and read all relevant configuration files:

**Global Configuration (~/.claude/):**

- `~/.claude/CLAUDE.md` - Global instructions
- `~/.claude/settings.json` - Global settings, permissions, allow/deny lists
- `~/.claude/skills/` - User skills directory

**Repository Configuration (./):**

- `./CLAUDE.md` - Repo-level instructions
- `./.claude/settings.local.json` - Repo-specific settings
- `./.claude/commands/` - Custom commands
- `./.mcp.json` - MCP server configuration

**Nested Configuration:**

- `**/CLAUDE.md` - Module-specific instructions (limit depth to 3)

### Chat History Analysis

Examine the conversation for these friction patterns:

| Pattern                        | Indicators                                     | Improvement Type      |
| ------------------------------ | ---------------------------------------------- | --------------------- |
| **Repeated corrections**       | "No, I meant...", "Actually...", "Not that..." | CLAUDE.md clarity     |
| **Permission requests**        | Bash commands approved 2+ times                | Allow list addition   |
| **Permission denials**         | Commands user rejected/canceled                | Deny list addition    |
| **Context requests**           | "Where is...", "How does X work here?"         | Architecture docs     |
| **Style corrections**          | Formatting, naming, pattern fixes              | Style guide addition  |
| **Repetitive workflows**       | Same multi-step sequence 2+ times              | Skill candidate       |
| **Tool limitations**           | Workarounds, manual steps needed               | MCP candidate         |
| **Code quality issues**        | Linting, formatting corrections                | Pre-commit hooks      |
| **Architectural explanations** | Design pattern clarifications                  | Pattern documentation |

## Phase 2: Structured Report

Generate a comprehensive report:

```markdown
## Configuration Audit

### Files Discovered

| Tier   | File                    | Status        |
| ------ | ----------------------- | ------------- |
| Global | ~/.claude/CLAUDE.md     | Found/Missing |
| Global | ~/.claude/settings.json | Found/Missing |
| Repo   | ./CLAUDE.md             | Found/Missing |
| ...    | ...                     | ...           |

### Chat Patterns Detected

- [List specific patterns found with examples]
- [Quote relevant user corrections or requests]

### Current Configuration Issues

- [Gaps between what Claude needs and what's documented]
- [Outdated or contradictory instructions]
- [Missing permissions that caused friction]
```

```markdown
## Recommended Improvements

### Priority 1: Critical

[Improvements that caused significant friction or errors]

### Priority 2: High Value

[Improvements that would meaningfully improve workflow]

### Priority 3: Nice to Have

[Minor enhancements and polish]

---

Each improvement follows this format:

#### [Descriptive Title]

- **Type:** CLAUDE.md | Skill | MCP | Hook | Permission | Allow-List | Deny-List | Pre-commit | Architecture
- **Tier:** Global | Repo | Nested
- **File:** [exact path to modify]
- **Rationale:** [why this improvement matters, with evidence from chat]
- **Change:** [exact content to add/modify]
```

```markdown
## Allow List Additions
| Command Pattern | Times Approved | Suggested Entry |
|-----------------|----------------|-----------------|
| `npm test` | 5x | `Bash(npm test:*)` |

## Deny List Additions
| Command Pattern | Reason | Suggested Entry |
|-----------------|--------|-----------------|
| ... | ... | ... |

## Pre-commit Hook Suggestions
| Pattern Detected | Suggested Hook | Rationale |
|------------------|----------------|-----------|

## Architecture Documentation
| Pattern Observed | Suggested Addition | Location |
|------------------|-------------------|----------|
```

## Phase 3: Interactive Implementation

After presenting the report, implement changes interactively:

1. **Present each improvement individually** with a preview diff
2. **For Accept:** Apply the change, confirm success, move to next
3. **For Modify:** Ask what changes the user wants, show updated diff, apply
4. **For Skip:** Note it was skipped, move to next
5. **Summarize at end:** List all changes made, skipped improvements, next steps

## Tier Placement Guide

| Pattern                     | Tier                     | Rationale                   |
| --------------------------- | ------------------------ | --------------------------- |
| Communication preferences   | Global                   | Applies to all interactions |
| Personal coding style       | Global                   | Consistent across projects  |
| Project build/test commands | Repo                     | Project-specific            |
| Architecture descriptions   | Repo                     | Project-specific            |
| Module-specific patterns    | Nested                   | Scoped to module            |
| Personal tool permissions   | Global settings.json     | User workflow               |
| Project tool permissions    | Repo settings.local.json | Team safety                 |
| Team MCP servers            | Repo .mcp.json           | Shared tooling              |
| Personal MCP servers        | Global                   | Personal tools              |
| Project pre-commit          | Repo .husky/             | Team standards              |
| Universal allow/deny        | Global settings.json     | Always applies              |
| Project-dangerous commands  | Repo settings.local.json | Project safety              |

## Important Notes

- Always show diffs before making changes
- Never modify files without explicit approval
- Group related improvements when practical
- Explain the reasoning behind tier placement
- Consider team vs personal preferences for shared repos
