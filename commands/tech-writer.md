---
description: "Assess technical writing for information density, clarity, and engineering effectiveness"
argument-hint: "[file path, or paste/describe the content to assess]"
---

# Tech Writer Assessor

Assess technical writing for information density and effectiveness. Optimized for content consumed by engineers and technical leaders — people who value precision, brevity, and signal over noise.

Input from user: `$ARGUMENTS`

## Step 1: Identify the Content

- If the user provided a file path, read it
- If the user pasted content directly, use that
- If unclear, ask what content to assess

## Step 2: Assess Against All Dimensions

Analyze the content against EVERY dimension below. Score each dimension 1-5 (1 = poor, 5 = excellent). Provide specific examples from the text for every issue found.

---

### Dimension 1: Information Density (Signal-to-Noise Ratio)

Apply Tufte's data-ink ratio principle to prose: every word should carry information. Flag:

- **Throat-clearing openers**: Introductory sentences that delay the point ("Before we begin...", "In order to understand X, we first need to...")
- **Redundant restatement**: Saying the same thing twice in different words, or concluding a section by summarizing what was just said
- **Empty topic sentences**: Paragraphs that open with "This section covers..." or "Let's look at..." instead of leading with the actual content
- **Padding words**: "basically", "essentially", "actually", "really", "very", "quite", "fairly", "somewhat", "just" — remove and check if meaning changes. If not, they're noise
- **Inflated phrasing**: Using more words than needed ("in order to" → "to", "due to the fact that" → "because", "at this point in time" → "now", "a large number of" → "many", "in the event that" → "if", "prior to" → "before", "subsequent to" → "after", "in the process of" → "while", "on a daily basis" → "daily", "has the ability to" → "can")
- **Over-explanation**: Explaining things the target audience already knows. Engineers don't need definitions of common technical terms
- **Meta-commentary**: Writing about the writing ("As mentioned earlier...", "We'll discuss this later...", "This document describes...")

**What good looks like**: Every sentence advances the reader's understanding. You could not remove a sentence without losing information.

---

### Dimension 2: Precision and Specificity

Technical readers need concrete, verifiable claims. Flag:

- **Vague quantifiers**: "several", "many", "some", "a few", "a lot of", "significant", "substantial" — replace with actual numbers or ranges
- **Weasel words**: "it is widely known", "studies show", "experts agree", "best practice dictates" — without specific citations
- **Unanchored comparisons**: "faster", "better", "more efficient", "improved" — compared to what? By how much?
- **Undefined terms**: Jargon, acronyms, or project-specific terms used without definition on first use
- **Ambiguous pronouns**: "this", "it", "they" where the referent is unclear
- **Hedge stacking**: Multiple hedges in one statement ("It might possibly be somewhat better in certain cases") — either commit to the claim or drop it
- **Temporal vagueness**: "recently", "soon", "in the near future", "eventually" — use dates or version numbers

**What good looks like**: A reader could fact-check every claim. Numbers have units. Comparisons have baselines.

---

### Dimension 3: Structure and Scannability

Engineers scan before they read. Flag:

- **Wall-of-text paragraphs**: Any paragraph over 5 sentences. Break up or use lists
- **Buried leads**: The key point appears in the middle or end of a section instead of the first sentence
- **Missing hierarchy**: Flat document structure without headings, or headings that don't form a logical hierarchy
- **Headings as labels vs summaries**: "Configuration" tells less than "Configure retry limits with exponential backoff"
- **Missing context upfront**: No overview/TL;DR for documents over 500 words. The reader should know what to expect and whether this doc is relevant within 10 seconds
- **Deep nesting**: More than 3 levels of headings or list indentation
- **No progressive disclosure**: All detail at one level instead of layering summary → detail → deep-dive
- **Poor use of visual separation**: Related items not grouped; unrelated items not separated

**What good looks like**: A reader skimming only headings and first sentences gets 80% of the value.

---

### Dimension 4: Actionability

Technical docs should change what the reader does. Flag:

- **Description without prescription**: Explaining how something works without saying what to do about it
- **Missing context for decisions**: Presenting options without trade-offs, constraints, or recommendations
- **No examples**: Concepts explained abstractly when a concrete example would be clearer. Code, commands, config snippets, or sample inputs/outputs
- **Missing prerequisites**: Steps that assume unstated setup, permissions, or prior knowledge
- **Ambiguous instructions**: "Configure the service appropriately" — how? With what values?
- **Missing error paths**: Only documenting the happy path. What happens when things go wrong? What does the user do then?
- **No "why"**: Instructions without rationale. Engineers follow instructions better when they understand the reasoning

**What good looks like**: A reader could execute on the content without asking follow-up questions.

---

### Dimension 5: Correctness and Consistency

Trust is lost once, rebuilt slowly. Flag:

- **Tense inconsistency**: Switching between present, past, and future within a section
- **Voice inconsistency**: Switching between "you should", "we recommend", "one might", "the user must"
- **Terminology drift**: Using different words for the same concept (e.g., "service" / "server" / "instance" interchangeably)
- **Stale content markers**: "TODO", "TBD", "FIXME", references to deprecated tools or versions
- **Contradictions**: Statements that conflict with each other within the document
- **Broken references**: Links, section references, or figure numbers that don't resolve
- **Code/prose mismatch**: Explanations that don't match the accompanying code

**What good looks like**: A reader can trust every statement. Terminology is locked in and consistent throughout.

---

### Dimension 6: Audience Calibration

Writing for the wrong level wastes everyone's time. Flag:

- **Over-explaining for experts**: Defining terms like "API", "latency", "idempotent" for a senior engineering audience
- **Under-explaining for newcomers**: Using project-internal acronyms or tribal knowledge in onboarding docs
- **Mixed audience signals**: Switching between beginner-level explanations and expert-level assumptions
- **Missing audience statement**: For docs that could serve multiple audiences — no indication of who this is for
- **Wrong level of abstraction**: Architecture doc that includes implementation details, or how-to guide that includes design rationale

**What good looks like**: Every sentence is calibrated to the stated or implied audience. Nothing is too basic, nothing is unexplained.

---

### Dimension 7: Conciseness of Expression

Separate from information density — this is about sentence-level economy. Flag:

- **Passive voice (unjustified)**: "The configuration was updated" vs "We updated the configuration". Passive is fine when the actor is irrelevant; flag when it obscures responsibility
- **Nominalizations**: Turning verbs into nouns ("make a decision" → "decide", "perform an analysis" → "analyze", "give consideration to" → "consider")
- **Prepositional pile-up**: Chains of "of the... in the... for the..." ("the analysis of the performance of the system in the context of the production environment" → "analyzing production system performance")
- **Weak verbs + adverbs**: "runs very quickly" → "sprints". Strong verbs eliminate adverb need
- **Unnecessary "that" and "which" clauses**: Often removable without meaning loss
- **Expletive constructions**: "There is/are...", "It is... that..." — usually removable ("There are three services that handle auth" → "Three services handle auth")

**What good looks like**: Sentences are tight. Every word earns its place.

---

## Step 3: Report Findings

Present a structured report:

```
## Tech Writing Assessment

**Content**: [filename or "pasted content"]
**Target audience**: [inferred from content]
**Word count**: [N words]

### Scorecard

| Dimension | Score | Issues |
|-----------|-------|--------|
| Information Density | X/5 | N issues |
| Precision & Specificity | X/5 | N issues |
| Structure & Scannability | X/5 | N issues |
| Actionability | X/5 | N issues |
| Correctness & Consistency | X/5 | N issues |
| Audience Calibration | X/5 | N issues |
| Conciseness of Expression | X/5 | N issues |
| **Overall** | **X/5** | **N total** |

### Findings by Dimension

#### [Dimension Name] — X/5

1. **[Issue type]**: "[exact text from content]"
   → Suggestion: [specific rewrite or action]

2. ...

### Top 3 Improvements

The highest-impact changes, in priority order:

1. [Most impactful change — what to do and why]
2. [Second most impactful]
3. [Third most impactful]
```

### Scoring Guide

- **5**: Exceptional. Publication-ready for the target audience. No meaningful issues.
- **4**: Strong. Minor issues that don't impede comprehension.
- **3**: Adequate. Several issues that slow down or confuse readers.
- **2**: Weak. Significant problems that undermine the document's purpose.
- **1**: Poor. Fundamental rework needed in this dimension.

The **Overall** score is NOT an average — it reflects the document's effectiveness as a whole. A document with 4s across the board but a 1 in Correctness gets a low overall score because trust failures are catastrophic.

## Step 4: Offer Next Steps

After the report, ask:

> Want me to rewrite this content with these improvements applied? I'll maintain the technical substance while tightening the prose.

If the user says yes, rewrite following these principles:
- Lead every section with the key point
- Cut all noise words and throat-clearing
- Replace vague claims with specific ones (flag where you need data from the author)
- Use active voice by default
- Add structure where the original was flat
- Keep technical depth — never dumb down for brevity
- Mark `[NEEDS DATA]` where precision requires information you don't have

<!--
## Future Research Areas (next iterations)

Areas to investigate and potentially add as dimensions:

- **Visual communication**: When to use diagrams, tables, code blocks vs prose.
  Decision framework for choosing the right medium.
- **Narrative arc**: How to structure longer docs (RFCs, design docs, postmortems)
  with a compelling thread that maintains attention.
- **Cross-referencing and linking**: When to inline vs link out.
  Balancing self-containedness with avoiding duplication.
- **Code examples quality**: Assessing whether code samples are minimal, runnable,
  and representative. Anti-patterns in example code.
- **Accessibility**: Alt text, color-blind-safe diagrams, screen-reader-friendly
  structure, plain language alternatives.
- **Internationalization**: Writing for non-native English speakers. Avoiding idioms,
  cultural references, and unnecessarily complex grammar.
- **Maintenance burden**: How to write docs that age well. Avoiding date-sensitive
  language, version-specific screenshots, and brittle references.
- **Cognitive load theory**: Applying Sweller's research to technical docs — managing
  intrinsic, extraneous, and germane load.
- **Genre-specific templates**: Different rubrics for RFCs, runbooks, ADRs, READMEs,
  API docs, incident reports, onboarding guides.
- **Information architecture**: Document collections, not just single documents.
  Navigation, discoverability, and reducing duplication across a doc set.
-->
