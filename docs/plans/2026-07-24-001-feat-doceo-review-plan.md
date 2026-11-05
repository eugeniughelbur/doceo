---
artifact_contract: ce-unified-plan/v1
artifact_readiness: implementation-ready
product_contract_source: ce-brainstorm
execution: code
date: 2026-07-24
---

# Doceo Review Mode - Plan

## Goal Capsule

**Objective:** add a `review` mode to the doceo skill so a learner can re-quiz themselves on past lessons, closing the gap between the README's "spaced learning" promise and the skill's actual behavior.

**Product authority:** scope settled in brainstorm dialogue with Eugeniu on 2026-07-24. Lesson creation, artifact rendering, and the improvement loop are not active scope.

**Open blockers:** none.

## Product Contract

### Summary

`/doceo review` (or "quiz me") starts a review session: doceo picks up to 3 past lessons, re-asks each lesson's stored Check-yourself questions one at a time, and closes each lesson with one "explain it back in one sentence" Feynman check. Results update each lesson's rating and feed the existing feedback log.

### Requirements

- R1: `review` triggers on `/doceo review` and natural phrasings like "quiz me" or "review my lessons".
- R2: A session covers at most 3 lessons, selected worst-rated first, then longest-unreviewed (never-reviewed counts as oldest).
- R3: Questions come from each lesson note's existing Check yourself section, asked one at a time, answers withheld until the learner responds.
- R4: Each lesson's review ends with one explain-back prompt: the learner states the topic's core idea in one sentence, judged against the lesson's answer line.
- R5: After each lesson, doceo updates the note's `rating:` field and writes `last_review: YYYY-MM-DD` into its frontmatter.
- R6: Session results append one line per lesson to `_feedback.md` in the existing log format.
- R7: Lessons the learner failed rank first in the next session's selection (their updated rating carries this).
- R8: With no lessons on file, review says so in one line and suggests creating a first lesson.

### Acceptance examples

- A learner with 5 lessons, one rated "too technical", runs `/doceo review`: that lesson appears first, its quiz questions are re-asked, and after the session its frontmatter carries today's `last_review` and an updated rating.
- A learner answers an explain-back with a sentence that misses the lesson's core idea: doceo shows the lesson's answer line, marks the lesson weak, and it leads the next session.

### Key decisions

- KD1: Quiz replay + explain-back, not pure replay and not full spaced repetition. SRS scheduling was rejected for carrying cost on every note. (session-settled: user choice 2026-07-24)
- KD2: Selection is worst-rated then longest-unreviewed, capped at 3 lessons per session. (session-settled: brainstorm synthesis confirmed 2026-07-24)
- KD3: Reviews reuse existing memory files (`rating:`, `_feedback.md`); the only new state is the `last_review` frontmatter field.

### Out of scope

- Spaced-repetition scheduling (due dates, intervals, `next_review`).
- New HTML artifacts for review sessions.
- Changes to lesson creation, memory seeding, or the improvement loop.

### Outstanding questions

- None blocking. Whether explain-back grading should ever regenerate a lesson is deferred to real usage.

## Planning Contract

### Key technical decisions

- KTD1: doceo is a prose skill, so review mode lands as a new numbered section in `SKILL.md`, parallel in weight to section 6 (the improvement loop). No scripts.
- KTD2: `last_review` lives in lesson frontmatter next to `rating:`, not in a separate state file. One source of truth per lesson.
- KTD3 (revised in review, 2026-07-24): failed reviews get a dedicated `forgotten` verdict instead of reusing lesson-quality verdicts. Both review agents flagged that reusing quality verdicts for recall failures would pollute the section 6 distillation loop. Selection reads ratings as ordinal (forgotten and negative verdicts < pending < great). The explain-back alone decides pass/fail.
- KTD4 (added in review): review triggers must live in the SKILL.md frontmatter description, not just the body; the description is what routes invocation, so "quiz me" was a dead trigger without it.

### Implementation Units

### U1. Add review mode to SKILL.md

**Goal:** the skill can run a full review session per R1-R8.
**Requirements:** R1-R8.
**Dependencies:** none.
**Files:** `SKILL.md`.
**Approach:**
1. Extend section 1 (Resolve the input) with the `review` trigger (R1).
2. Add a new section "Review mode" after section 6: selection rule (R2), question flow (R3), explain-back (R4), memory updates (R5, R6), failure priority (R7), empty state (R8).
3. Keep it under ~35 lines, matching the file's existing density.
**Patterns to follow:** the numbered-section voice of `SKILL.md`; the one-question-at-a-time rule from section 6.
**Test scenarios:** Test expectation: none -- prose skill; verification is a dry-run transcript (see Verification Contract).
**Verification:** a fresh read of `SKILL.md` resolves every R-ID to a specific instruction; no contradiction with sections 5-6.

### U2. Surface review mode in README and llms.txt

**Goal:** the docs promise matches the skill again.
**Requirements:** R1 (discoverability of the trigger).
**Dependencies:** U1.
**Files:** `README.md`, `llms.txt`.
**Approach:** add `/doceo review` to the usage block and one FAQ entry ("How do I review what I learned?"); mention review mode in llms.txt if it lists capabilities.
**Test scenarios:** Test expectation: none -- docs only.
**Verification:** README usage block shows the new trigger; no other README claims changed.

## Verification Contract

- Dry-run: simulate `/doceo review` against a 3-lesson fixture memory (one rated negative, one pending, one great) and confirm selection order, question flow, and the frontmatter/`_feedback.md` writes match R2-R7.
- Empty state: simulate with zero lessons, expect the one-line R8 response.

## Definition of Done

- Both units landed, dry-run passes, plan file archived as implemented.
