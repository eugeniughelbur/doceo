---
module: "skill-definition"
date: 2026-07-24
problem_type: convention
component: documentation
severity: medium
applies_when: "adding any new trigger phrase, mode, or entry point to a Claude Code skill"
symptoms:
  - "a documented trigger phrase never activates the skill"
  - "body text promises behavior the router cannot see"
tags: [claude-code, skill-authoring, frontmatter, triggers, routing]
---

# Skill triggers live in the frontmatter description, not the body

## Context

While adding review mode to doceo, the trigger phrases ("quiz me", "review my lessons") were written into the SKILL.md body (sections 1 and 7) but not into the frontmatter `description:`. A fresh-eyes review caught that Claude Code routes skill invocation on the description alone, so every natural-language trigger not mirrored there is dead: the user says "quiz me", nothing loads, and the body text never gets read.

## Guidance

When a skill gains a new mode or trigger, update the frontmatter `description:` in the same edit as the body. The description must name both the slash form and the natural phrasings.

Example from this fix (SKILL.md line 3):

> Also use for review: the user runs /doceo review or says "quiz me" / "review my lessons" to be re-quizzed on past lessons.

Checklist for any future doceo mode:

1. Trigger named in `description:` (slash form + natural phrasings).
2. Trigger listed in section 1 (Resolve the input).
3. Behavior defined in its own numbered section.
4. README usage block and llms.txt updated.

## Applicability note

This applies to every SKILL.md in every repo, not just doceo: the body is instructions for after the skill loads; the description is the only part the router reads before loading.

## Related learning (same session)

One frontmatter field should not serve two meanings. Reusing lesson-quality verdicts (`too technical`) for recall failures would have polluted the `_learnings.md` distillation loop; review mode got its own `forgotten` verdict instead.
