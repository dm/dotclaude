---
description: "Create Google Slides presentations from markdown outlines and publish to Google Workspace"
argument-hint: "<title> [outline or topic]"
---

# Slides from Markdown

Create a Google Slides presentation from a markdown outline using `gogcli`.

User input: `$ARGUMENTS`

## Prerequisites

Before proceeding, verify `gog` is available:

```bash
which gog
```

If not found, stop and tell the user:
"gogcli is not installed. Install it with: `brew install steipete/tap/gogcli`"

Also verify auth is configured:

```bash
gog auth list
```

If no accounts are configured, stop and tell the user:
"No Google account configured. Run `gog auth add your@email.com` to set up authentication."

## Step 1: Parse Input

Parse `$ARGUMENTS` to extract:
- **Title**: The first quoted string, or the first few words before any outline content
- **Outline/topic**: Everything after the title, or the user's surrounding context

If `$ARGUMENTS` is empty, ask: "What's the presentation about? Give me a title and an outline (or just a topic and I'll generate the outline)."

If only a topic is provided (no outline), generate a sensible outline of 5-8 slides based on the topic.

## Step 2: Shape the Markdown

Transform the user's outline into gogcli-compatible slide markdown. The format uses `---` as slide separators and `##` headings as slide titles.

### Format Rules

```markdown
## Slide Title

- Bullet point one
- Bullet point two
- Bullet point three

---

## Next Slide Title

Body text goes here as regular paragraphs.

- More bullets if needed

---

## Code Example

```language
code block here
```
```

### Slide Structure Guidelines

- **First slide**: Title slide with the presentation title and a subtitle or key message
- **Content slides**: Each should have a `## Title` and 3-5 bullet points or a short paragraph
- **Keep text minimal**: Slides are visual aids, not documents. Max 5 bullets per slide, max ~10 words per bullet
- **Last slide**: A summary, call-to-action, or "Questions?" slide
- Aim for 5-12 slides total unless the outline clearly needs more
- Each slide must have a `## Title` — slides without titles are discarded by gogcli

### What to avoid

- Don't use `#` (h1) — gogcli uses `##` (h2) for slide titles
- Don't put `---` inside content — it's the slide separator
- Don't include speaker notes in the markdown (gogcli doesn't parse them from markdown)

## Step 3: Review with the User

Show the shaped markdown to the user in a code block. Briefly describe the slide flow:

"Here's the deck outline — N slides covering [summary]. Want me to create it, or adjust anything first?"

Wait for confirmation before proceeding. If the user suggests changes, revise and show again.

## Step 4: Create the Presentation

Write the markdown to a temp file and invoke gogcli:

```bash
TMPFILE=$(mktemp /tmp/slides-XXXXXX.md)
cat > "$TMPFILE" << 'SLIDES_EOF'
<markdown content>
SLIDES_EOF
gog slides create-from-markdown "<title>" --content-file "$TMPFILE"
rm "$TMPFILE"
```

Parse the output for:
- **Presentation ID**
- **Link** (webViewLink)

## Step 5: Post-Creation Options

After successful creation, present the link and ask:

"Presentation created: [link]

Want me to:
- **Share it** with someone? (`gog drive share <id> --to user --email user@example.com --role writer`)
- **Export to PDF/PPTX**? (`gog slides export <id> --format pdf --out ./deck.pdf`)
- **Move it** to a specific Drive folder? (`gog drive move <id> --parent <folderId>`)"

If the user requests any of these, execute the corresponding `gog` command.

## Rules

- **Always preview before creating.** Never send to Google without user confirmation.
- **Keep slides concise.** If the user provides a wall of text, distill it into slide-friendly bullets.
- **One idea per slide.** Split dense content across multiple slides.
- **Title every slide.** Slides without `## Title` are silently dropped.
- **Clean up temp files.** Always remove the temp markdown file after creation.
- **Respect the outline.** If the user provides a detailed outline, follow its structure. Only restructure if the user gave a loose topic.
