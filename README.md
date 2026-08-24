# brain-starter

Your AI agents forget everything between sessions. This repo is a starter for a
persistent **brain** — a small git repo that holds your agent's identity, your locked
decisions, and per-project state, loaded automatically at the start of every session so
your agents never relearn anything. It's the pattern extracted from a working setup
that's been running for months, stripped to a skeleton you fill in.

It pairs naturally with [dark-factory](https://github.com/RickWyatt-me/dark-factory):
the factory builds, the brain remembers. But it works alone with any Claude Code setup.

## How it works

One loading ladder, enforced by a tiny skill that auto-fires when your projects come up:

1. **Always:** `.agent/SOUL.md` — who the agent is, what it knows cold. Small, stable.
2. **Always:** `.agent/MEMORY.md` — global state: locked decisions, open questions.
3. **Only when a project is mentioned:** that project's `STATE.md` (+ `MEMORY.md` if
   it's the active one). Keyword-gated, so you never pay for context you don't need.
4. **Only when referenced:** anything in `projects/*/artifacts/`. Never pre-loaded.

Two files always. Everything else earns its way in. That's the whole trick.

## Get it

```bash
# 1. Make it yours (use as a template, or clone and re-point the remote)
gh repo create my-brain --private --template RickWyatt-me/brain-starter --clone
cd my-brain

# 2. Turn on the auto-sync hooks (auto-push on commit, auto-pull on checkout)
git config core.hooksPath .githooks

# 3. Install the loader skill where Claude Code finds it globally
cp -R .claude/skills/brain-loader ~/.claude/skills/
```

Then fill in the placeholders — every `{CURLY_TOKEN}` is yours to replace:

1. `.agent/SOUL.md` — rename from `SOUL.template.md`, fill in identity + domain.
2. `.agent/MEMORY.md` — start with one locked decision and one open question. Real ones.
3. Copy `projects/_template/` to `projects/<your-project>/` and fill in `STATE.md`.
4. Edit `~/.claude/skills/brain-loader/SKILL.md` — put YOUR project names and vocabulary
   in the frontmatter `description`. That description is what makes the skill fire;
   placeholders left in place mean it never triggers.

Keep the brain **private**. It exists to hold the stuff you wouldn't publish.

## The files

| Path | What it is |
|---|---|
| `CLAUDE.md` | The always-loaded contract: load ladder, session-end protocol |
| `.agent/SOUL.template.md` | Identity template — who the agent is, permanently |
| `.agent/MEMORY.md` | Global state: locked decisions vs open questions |
| `.agent/daily/` | Session logs — three lines per session, that's it |
| `projects/_template/` | Copy per project: `STATE.md` (current truth) + `MEMORY.md` (decisions) |
| `.claude/skills/brain-loader/` | The loader skill — the auto-trigger + the ladder |
| `.githooks/` | post-commit auto-push, post-checkout auto-pull; silent, never fail |

## Conventions that do the real work

- **Locked vs open are two sections, not tags.** Locked decisions are bullets with a
  date and who decided. Open questions are checkboxes. Deciding something = moving the
  line from one section to the other. Cheap to maintain, impossible to misread.
- **One home per fact.** State lives in `STATE.md`; every other file that needs it
  carries one sentence and a path. Copies go stale silently — pointers don't.
- **Three-line session log.** Every session appends to `.agent/daily/YYYY-MM-DD.md`:
  `Worked on:` / `Decided:` / `Open:`. Thirty seconds of discipline; next session
  starts warm.
- **Confidentiality frontmatter.** Every file carries `confidentiality: internal` or
  `public`. Before an agent generates anything outward-facing (a README, a blog post),
  it checks: `internal` sources are reference only, never quoted.
- **Paths in backticks, not markdown links.** `projects/vox/STATE.md` beats
  `[state](../..)` — agents resolve it, humans read it, refactors don't break it.

## What this is not

- **Not a RAG system.** No embeddings, no vector store, no retrieval service. It's
  markdown in git and a loading discipline. That's a feature: you can read every byte
  your agent knows.
- **Not a framework.** Nothing to install beyond one `cp` and one `git config`. If a
  convention here doesn't fit how you work, delete it — the ladder is the only
  load-bearing part.
- **Not self-maintaining.** The agent writes to it, but the three-line log and the
  locked/open discipline only work if sessions actually end with them. The `CLAUDE.md`
  contract handles that — as long as you keep it loaded.
