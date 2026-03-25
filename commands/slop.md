---
description: "Scan content for AI writing patterns (slop) and flag them for removal"
argument-hint: "[file path, or paste/describe the content to scan]"
---

# Slop Detector

Scan content for common AI-generated writing patterns and flag them for revision. The goal is natural, human-sounding prose — not generic AI output.

Input from user: `$ARGUMENTS`

## Step 1: Identify the Content

- If the user provided a file path, read it
- If the user pasted content directly, use that
- If unclear, ask what content to scan

## Step 2: Scan for Slop Patterns

Analyze the content against ALL of the following categories. For each match found, note the exact text and its location.

### Category 1: Dead Giveaway Words

Words that immediately signal AI authorship. Flag every instance:

- **delve** — the #1 AI tell
- **tapestry** (used metaphorically, e.g. "rich tapestry of")
- **landscape** (used abstractly, e.g. "the evolving landscape of")
- **realm** ("in the realm of")
- **embark** / **embark on a journey**
- **navigate** (used metaphorically for non-physical movement)
- **leverage** (as a verb meaning "use")
- **harness** ("harness the power of")
- **foster** ("foster innovation/growth/collaboration")
- **pivotal** / **crucial** / **vital**
- **intricate** / **nuanced** / **intricacies**
- **comprehensive**
- **multifaceted**
- **groundbreaking** / **cutting-edge** / **game-changer**
- **revolutionize** / **disruptive**
- **streamline**
- **robust**
- **seamless** / **seamlessly**
- **elevate**
- **empower**
- **unlock** (metaphorical, e.g. "unlock potential")
- **illuminate** / **shed light on**
- **underscore**
- **showcase**
- **garner**
- **endeavor** / **endeavour**
- **elucidate**
- **utilize** / **utilizing** (just say "use")
- **facilitate**
- **optimal** / **optimize** (when "best" or "improve" works)
- **enhancement** (when "improvement" works)
- **stakeholder** (outside actual business/legal contexts)
- **synergy**
- **holistic**
- **paradigm**
- **testament** ("stands as a testament to")
- **beacon** ("a beacon of hope/innovation")
- **interplay** ("the interplay between")
- **bolster** / **bolstered**
- **meticulous** / **meticulously**
- **vibrant**
- **enduring**
- **spearhead**
- **captivating** / **fascinating** / **breathtaking** / **stunning**
- **resonate** / **resonates with**
- **cultivate**
- **encompass**
- **myriad** (especially "a myriad of")
- **profound** / **profoundly**
- **stark** (as in "stark contrast" / "stark reminder")
- **cornerstone**
- **burgeoning**
- **nascent**
- **underscore**
- **salient**
- **commendable**
- **ever-evolving**

### Category 2: Filler Phrases

Phrases that pad word count without adding meaning:

- "It's important to note that..."
- "It's worth mentioning that..."
- "It should be noted that..."
- "In today's [fast-paced/ever-changing/digital] world..."
- "In an era of..."
- "In a world where..."
- "When it comes to..."
- "At the end of the day..."
- "In conclusion..." / "To sum up..." / "In summary..."
- "Let's dive in..." / "Let's explore..."
- "Without further ado..."
- "That being said..."
- "Having said that..."
- "It goes without saying..."
- "Needless to say..."
- "As we all know..."
- "The fact of the matter is..."
- "At its core..."

### Category 3: Performative Structure

Structural patterns that feel formulaic:

- **The triple list**: "X, Y, and Z" used repeatedly (rule of three overuse)
- **Not just X, but Y**: "It's not just about X — it's about Y" or "not merely X, but also Y" or "not only X but also Y"
- **The false journey**: "Let's embark on..." / "Join me as we explore..."
- **Question-then-answer**: Opening with a rhetorical question immediately answered ("Ever wondered about X? Well...")
- **The grand conclusion**: Ending with sweeping, vague statements about the future
- **Mirrored parallelism**: "From X to Y, from A to B" used for dramatic effect
- **The inflation pattern**: Simple ideas dressed in unnecessarily complex language
- **The result? pattern**: "The result? More people are..." — faux-dramatic sentence fragment followed by answer
- **Understanding/Importance headings**: Repetitive subheadings like "Understanding X", "The Importance of Y", "The Future of Z"
- **The setup-payoff cliché**: "Whether you're a X or a Y, this guide will..."
- **Numbered listicle framing**: "Here are N ways to..." / "N things you need to know about..."

### Category 4: Hedging and Over-Qualification

AI's tendency to never commit to a position:

- "It's important to consider..."
- "One might argue that..."
- "To some extent..."
- "In many ways..."
- "It could be argued that..."
- "From a broader perspective..."
- "Generally speaking..."
- "For the most part..."
- Presenting "both sides" when the answer is clear
- Excessive use of "may", "might", "could", "potentially", "arguably"

### Category 5: Transition Word Overuse

AI leans heavily on formal connectives:

- **Moreover** / **Furthermore** / **Additionally**
- **However** / **Nevertheless** / **Nonetheless**
- **Consequently** / **Subsequently**
- **Hence** / **Thus** / **Therefore** (when overused)
- **Conversely**
- **Notably**
- **Importantly**
- **Ultimately**
- **Indeed**

Flag these when they appear with high frequency (3+ in a short piece, or used as paragraph openers repeatedly). One or two natural uses are fine.

### Category 6: Excessive Enthusiasm and Inflation

AI tends to oversell everything:

- "Exciting" / "Remarkable" / "Extraordinary"
- "Powerful" (for mundane things)
- "Transform" / "Transformative"
- "Revolutionize"
- "Incredible" / "Amazing"
- "Essential" / "Indispensable"
- "Unparalleled"
- "World-class"
- Exclamation marks used for emphasis in professional/technical writing

### Category 7: Punctuation and Formatting Tells

- **Em dash overuse** — using em dashes in nearly every paragraph — as a universal connector
- **Colon-list pattern**: Sentence ending with a colon followed by a list, used repeatedly
- **Bold for emphasis** on every other sentence
- **Uniform paragraph length**: All paragraphs roughly the same size
- **Uniform sentence length**: Lack of natural rhythm variation

### Category 8: Hollow Summarization

- Restating what was just said in slightly different words
- Concluding paragraphs that add no new information
- "As we've seen..." / "As discussed above..."
- "This highlights the importance of..."
- "This demonstrates that..."
- "This underscores the need for..."
- "No discussion of X would be complete without..."

### Category 9: Participle Tacking (-ing Phrases)

AI appends superficial "-ing" analysis to the end of sentences to fake depth. Flag when used repeatedly:

- "...highlighting the importance of..."
- "...making it a valuable resource for..."
- "...ensuring that stakeholders can..."
- "...demonstrating the need for..."
- "...underscoring the significance of..."
- "...paving the way for..."
- "...reflecting a broader trend toward..."
- "...showcasing the potential of..."
- "...contributing to a growing body of..."
- "...symbolizing the shift toward..."
- "...solidifying its position as..."

The pattern: `[factual statement], [present participle] [vague importance claim]`. One or two is fine. Three or more in a piece is a strong AI signal.

### Category 10: Promotional and Glazing Language

AI drifts into advertising copy, overselling importance and significance:

- "stands as a testament to..."
- "plays a vital/crucial/pivotal role in..."
- "has become a cornerstone of..."
- "rich cultural heritage"
- "stunning natural beauty" / "breathtaking scenery"
- "world-renowned" / "internationally acclaimed"
- "boasts" (as in "the city boasts a vibrant...")
- "is home to a thriving..."
- "a hub for innovation"
- "at the forefront of..."
- "a shining example of..."
- "sets the stage for..."
- Treating mundane subjects with grandiose reverence

### Category 11: Weasel Words and False Attribution

AI hedges claims by attributing them to unnamed sources:

- "Many experts believe..."
- "Studies have shown..."
- "Research suggests..."
- "It is widely regarded as..."
- "It is commonly understood that..."
- "According to experts..."
- "Some argue that..." / "Critics argue that..."

Flag when no specific source, study, or expert is named. Vague attribution is a hallmark of AI-generated content that sounds authoritative without being verifiable.

### Category 12: Chatbot Leakage

Conversational artifacts that leak through when AI text isn't properly cleaned:

- "I hope this helps!"
- "Certainly!" / "Absolutely!" / "Great question!"
- "Let me know if you have any questions"
- "Feel free to reach out"
- "Here's a breakdown of..."
- "Let me break this down..."
- "Think of it this way..."
- "In this article, we'll explore..."
- "By the end of this guide, you'll..."
- Addressing the reader as "you" excessively in formal/technical content
- Starting with "So," in written content (conversational carry-over)

## Step 3: Report Findings

Present a structured report:

### Format

```
## Slop Report

**Content**: [filename or "pasted content"]
**Slop density**: [Low / Medium / High / Critical]

### Findings

#### [Category Name] — [count] issues

1. **"[exact flagged text]"** (line/paragraph N)
   → Suggestion: [plain alternative or "remove entirely"]

2. ...

### Summary

- Total issues: N
- Top categories: [which categories had the most hits]
- Worst offenders: [the 2-3 patterns that appear most]
- Overall assessment: [1-2 sentences on the tone/feel]
```

### Density Scale

- **Low** (0-4 issues): Minor polish needed. Mostly human-sounding.
- **Medium** (5-12 issues): Noticeable AI flavour. Needs a revision pass.
- **High** (13-20 issues): Reads like lightly edited AI output. Significant rewriting recommended.
- **Critical** (21+ issues): Unmistakably AI-generated. Consider rewriting from scratch or providing the core ideas and asking for a complete rewrite with explicit anti-slop instructions.

## Step 4: Offer Next Steps

After the report, ask:

> Want me to rewrite this content with the slop removed? I'll preserve the meaning and structure while making it sound natural.

If the user says yes, rewrite the content following these principles:
- Use plain, direct language
- Vary sentence length naturally
- Cut filler — if a sentence adds nothing, delete it
- Replace inflated words with simple ones
- State positions clearly instead of hedging
- Let the content speak for itself without performative framing
- Keep the author's voice and intent intact
