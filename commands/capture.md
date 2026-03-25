---
description: "Capture an atomic thought into the Obsidian vault following Zettelkasten principles"
argument-hint: "<your thought or idea>"
---

# Capture Note

Capture an atomic thought into the Obsidian Zettelkasten vault.

User input: `$ARGUMENTS`

## Vault Location

The vault inbox is at: `$HOME/Library/Mobile Documents/iCloud~md~obsidian/Documents/dm@notes/001 inbox/`

Before writing, verify the vault directory exists. If it doesn't, stop and tell the user:
"Obsidian vault not found at the expected path. Make sure your vault is synced and accessible."

## Step 1: Understand the Thought

If `$ARGUMENTS` is empty or unclear, ask the user: "What's the thought you want to capture?"

If the user provided a thought, proceed immediately. Do NOT ask for confirmation.

## Step 2: Shape the Note

A Zettelkasten note is **atomic** — one idea, one note. If the user's input contains multiple distinct ideas, split them into separate notes and tell the user you're doing so.

For each note, determine:
- **Title**: A short, declarative statement of the idea (not a topic label). Good: "Feedback loops accelerate learning". Bad: "Feedback loops".
- **Body**: 2-5 sentences expanding on the idea. Keep it concise. The note should be understandable on its own without external context.
- **Tags**: 1-3 relevant tags, lowercase, no spaces (use hyphens). Think in terms of *retrieval* — what would you search for to find this note later?
- **Links**: If the thought clearly connects to a concept that likely has existing notes, add `[[wikilinks]]` inline in the body. Don't force links — only add them when the connection is genuine.

## Step 3: Write the Note

Generate a timestamp-based unique ID: `YYYYMMDDHHmm` (e.g., `202603071430`).

Create the file at: `<vault-inbox>/<timestamp> <title>.md`

Use this format:

```markdown
---
created: YYYY-MM-DDTHH:mm
tags:
  - tag-one
  - tag-two
---

# Title

Body text with optional [[wikilinks]] to related concepts.
```

## Step 4: Confirm

After writing, output:
- The note title
- The file path
- A brief summary: "Captured: [title]"
- **PARA hint**: Suggest which PARA folder this note might eventually belong in (see vault structure below). This is a suggestion only — the user moves it themselves.

If multiple notes were created, list them all.

## Vault Structure (PARA)

The vault follows the PARA method. Notes always land in `001 inbox/` first, but when confirming, suggest the likely destination:

| Folder | Purpose |
|--------|---------|
| `110 Projects` | Active projects with a defined outcome and deadline |
| `120 Areas` | Ongoing areas of responsibility (health, finances, etc.) |
| `130 Resources` | Topics of interest, reference material |
| `140 Archives` | Completed or inactive items |
| `200 work` | Work-related notes |
| `300 family` | Family-related notes |
| `900 personal` | Personal notes |
| `909 sometime` | Ideas to revisit later, no urgency |

## Rules

- **One idea per note.** Split multi-idea inputs into separate notes.
- **Declarative titles.** Titles are claims or statements, not topic labels.
- **No fluff.** The body should be dense and self-contained.
- **Inbox only.** Always write to `001 inbox/`. The user organizes from there.
- **Never modify existing notes.** Only create new ones.
- **Invoking `/capture` IS the request.** Don't ask for confirmation — shape the note and write it immediately.
