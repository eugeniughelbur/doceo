# doceo

*doceo* (Latin): **I teach.**

A Claude Code skill that turns anything you want to learn into a short, visual, plain-language lesson, never a book. And it gets better every time you use it.

## The problem

Ask an LLM to explain something and you get a wall of text. Technically correct, exhausting to read, gone from your head by tomorrow. Learning a new codebase, a company's data model, or any unfamiliar tech this way feels like reading a book a day.

## What doceo does instead

```
/doceo our billing data model
/doceo https://github.com/some/repo
/doceo src/services/
/doceo                      ← teach me the confusing thing we're discussing right now
```

Every lesson is one screen, with a fixed shape:

1. **The answer** — one plain sentence at the top.
2. **The picture** — one diagram (boxes, arrows, "you are here").
3. **The analogy** — one real-world comparison from *your* world.
4. **Where it fits** — why this matters to you, in your project, in your role.
5. Baby steps (when it's a task), a tiny self-quiz, and opt-in "go deeper" links.

You get a rendered HTML lesson page to learn from, plus a markdown copy saved to your lessons folder (an Obsidian vault works great — diagrams render natively).

## The memory

Doceo keeps a small learning memory next to your lessons:

- `_profile.md` — who you are, what you already know, how you like to be taught
- `index.md` — every lesson, one line each
- `maps/<domain>.md` — one growing map per domain: your whole territory, one picture
- lessons that **build on each other** — lesson 12 assumes lessons 1–11, and links back instead of re-explaining

## The loop

After every lesson, doceo asks one question: *did this land?*
Your answer becomes feedback, repeated feedback becomes rules in `_learnings.md`, and every future lesson starts by reading those rules. The skill you have after 50 lessons is not the skill you installed.

## Install

```bash
git clone https://github.com/YOURNAME/doceo ~/.claude/skills/doceo
```

First run asks one question (where to save lessons) and interviews you for 30 seconds to seed your profile. Config lives in `~/.doceo/config.json`, so the same skill on your work machine keeps a completely separate memory.

## Privacy by design

- The skill contains nothing about you; all memory is local, outside the repo.
- Lessons capture *your understanding in your own words*. The skill is instructed to never paste employer documents, proprietary code, or credentials into your notes.
- Work machine and personal machine never share a memory.

## License

MIT
