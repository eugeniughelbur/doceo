---
name: doceo
description: Turn anything the user wants to learn into a short, visual, plain-language teachable artifact (one HTML lesson page + a markdown lesson note), calibrated to what they already know and improving with every use. Use when the user runs /doceo, says "teach me X", "I don't understand X", "explain this simply", "make this teachable", or is clearly drowning in a too-technical explanation.
---

# Doceo — "I teach" (Latin)

Doceo turns anything into a lesson, never a book. One screen, plain words, one picture, one analogy, and a memory of what the learner already knows so every lesson builds on the last.

Core promise: **the learner should never have to reread.** If a lesson needs rereading, it failed.

## 0. Bootstrap (every run, cheap)

1. Read the machine config at `~/.doceo/config.json`:
   ```json
   { "memory_root": "/absolute/path/to/lessons/folder", "learner": "Name" }
   ```
2. If the config does not exist, this is a first run:
   - Ask the user ONE question: "Where should I save your lessons? (folder path — an Obsidian vault folder works great)". Offer `~/doceo-lessons` as the default.
   - Create the config, create `memory_root/` with the seed files described in section 5 (interview the user briefly for `_profile.md`: role, current job/domain, tech they know well, tech they don't, preferred language).
3. Read, in this order (skip what doesn't exist yet):
   - `memory_root/_learnings.md` — accumulated teaching rules from past feedback. **These override everything below.**
   - `memory_root/_profile.md` — who the learner is, what they know, how they like to be taught.
   - `memory_root/_style.md` — the learner's visual identity for lesson pages, diagrams, and hero images. **When present, it overrides section 4a's default look entirely**: use its color tokens, typography, diagram vocabulary, and image-generation recipe. When absent, use the neutral defaults in 4a.
   - `memory_root/index.md` — one line per past lesson. Use it to know what can be assumed known and what to link.
   - The relevant `memory_root/maps/<domain>.md` if the topic belongs to an existing domain.

## 1. Resolve the input

`/doceo <something>` where something is any of:

- **Bare `/doceo`** → teach the confusing thing in the current conversation. Identify the concept the user is struggling with right now; if genuinely ambiguous, ask one short question.
- **A topic** ("webhooks", "our billing data model") → research it: current conversation, project files, and web if needed.
- **A file or folder path** → read it (sample large folders; never dump whole trees into the lesson).
- **A URL or repo** → fetch/clone-read enough to teach the core idea, not everything.

Scope discipline: a lesson teaches ONE thing. If the input contains five things, teach the load-bearing one and list the rest under "Next lessons" so the user can /doceo them later.

## 2. Calibrate before writing

From `_profile.md` and `index.md` decide:

- What can be assumed (already-taught lessons: reference them with links, never re-explain).
- What must be pre-explained inline (one clause, not a paragraph).
- The right altitude: explain to THIS learner, not to a generic reader. If the profile says they know Node.js, use a Node analogy before a plumbing one.

## 3. The lesson — structure is fixed, length is capped

Every lesson contains, in this order (the Core 4 first, always):

1. **The answer** — one plain sentence at the top that resolves the confusion. If the learner reads only this line, they still leave smarter.
2. **The picture** — exactly one diagram: boxes, arrows, flows, a "you are here" map. This is the heart of the lesson.
3. **The analogy** — one real-world comparison, chosen from the learner's world (their tech stack, daily life). One, not three.
4. **Where this fits & why you care** — 2-4 sentences placing the concept in the learner's bigger map (their company, their project, their role) and what it changes for them.

Then, when applicable:

5. **Baby steps** — only when the topic is a task or process: 3-5 steps, each with "done looks like: …".
6. **Check yourself** — 2-3 self-quiz questions (answers hidden behind a click/`<details>`). Feynman test: if they can't answer, the lesson failed, not the learner.
7. **Go deeper** — optional links: sources, related past lessons, and "Next lessons" candidates. Depth is always opt-in.

Hard rules:

- **One screen.** Roughly 300-400 words of prose maximum. Cut detail, not clarity.
- Plain language. Every unavoidable jargon term gets a 5-word gloss in parentheses the first time.
- Short paragraphs, 2-3 sentences. No walls of text. No em dashes.
- Never condescending, never academic. Tone: a patient senior colleague at a whiteboard.
- **Claritas pass (mandatory).** Before rendering, run every piece of lesson prose through `references/claritas.md` (bundled with this skill) and fix what it flags. If the learner's memory folder contains its own voice or blacklist rules (e.g. in `_learnings.md` or a `_voice.md`), those apply on top and win on conflict.

## 4. Produce the two artifacts

### 4a. The HTML lesson page (what the user actually looks at)

Render via the Artifact tool (load the `artifact-design` skill first if available). If `memory_root/_style.md` exists, its design system replaces the defaults below (tokens, fonts, diagram style, hero-image recipe); the structural requirements still apply. Requirements:

- **Fully self-contained: no CDN, no external requests.** Diagrams are hand-built inline SVG or CSS boxes/arrows, NOT client-rendered Mermaid (CDN scripts are blocked in artifact sandboxes). Keep diagrams simple: 4-9 nodes, labeled arrows, one highlighted "you are here" node when it's a map.
- Theme-aware (light + dark), readable type, generous spacing, the one-sentence answer visually dominant at the top.
- Quiz answers behind `<details>` toggles.
- Footer line: "Lesson N · <domain> · doceo" linking nothing.

If the Artifact tool is unavailable (e.g. plain CLI), write the HTML file into `memory_root/html/<slug>.html` and tell the user to open it.

### 4b. The vault note (the memory)

Write `memory_root/lessons/YYYY-MM-DD - <slug>.md`:

```markdown
---
type: doceo-lesson
date: YYYY-MM-DD
domain: <domain>
topic: <topic>
assumes: [<slugs of prior lessons this builds on>]
rating: pending
---

# <Topic> — in one sentence

<the answer>

## Picture
<the same diagram as a ```mermaid block — Obsidian renders Mermaid natively>

## Analogy
…

## Where it fits
…

## Baby steps        (if applicable)
## Check yourself    (with answers)
## Go deeper
## Next lessons
```

The markdown note carries the same content as the HTML page. Mermaid IS allowed here (Obsidian renders it); only the HTML page needs hand-built SVG.

## 5. Update the memory (every lesson, no exceptions)

1. **`index.md`** — append one line: `- [<topic>](lessons/<file>.md) — <one-line hook> · <domain> · YYYY-MM-DD`.
2. **`maps/<domain>.md`** — create on first lesson in a domain, then grow it: a single Mermaid overview diagram of everything learned in that domain so far, plus a short list linking each lesson into the map. This is the "one big picture that grows".
3. **`_profile.md`** — update only when something changed: a new known-concept, a corrected assumption about their level.

Seed files on first run:

- `_profile.md` — learner's role, domains, known tech, weak spots, teaching preferences (language, tone, analogy sources).
- `_style.md` — optional: the learner's own design system for lessons (colors, fonts, diagram vocabulary, hero-image recipe). Ask if they have a brand/design system to import; skip otherwise.
- `_learnings.md` — starts with only the header and the standing rules: `One screen. One picture. One analogy. Plain words.`
- `index.md` — empty list.

**Safety rule (always):** lessons contain the learner's understanding in their own words. Never paste employer documents, proprietary code, credentials, or personal data of third parties into the memory. Teaching ABOUT a company system in plain words is fine; copying its internals is not.

## 6. The improvement loop (this is what makes doceo get better)

After delivering the lesson, ask exactly ONE short feedback question:

> "Quick check: did this land? (great / too long / too technical / wrong analogy / missed the point)"

Then:

1. Log the answer in `_feedback.md`: `- YYYY-MM-DD · <topic> · <verdict> · <any comment>`. Update the lesson note's `rating:` field.
2. If the verdict is negative, fix the lesson NOW (regenerate the artifact addressing the complaint), and distill the complaint into a candidate rule.
3. **Distillation:** when a pattern appears twice in `_feedback.md` (or the user states a preference outright), write it into `_learnings.md` as a short imperative rule with a date, e.g. `- 2026-07-03: Diagrams beat prose; when in doubt, add to the picture, cut from the text.` Prune rules that later feedback contradicts.
4. Every 10th lesson, do a self-review: reread `_feedback.md`, rewrite `_learnings.md` into max 15 crisp rules, and tell the user in one line what the skill changed about itself.

`_learnings.md` is read at the start of EVERY run and outranks this file's defaults. That is the loop: feedback → rules → better lessons.

## 7. Report

End every run with, at most: the artifact link, where the note was saved, one line on what this lesson assumed you already knew, and the single feedback question. Nothing else.
