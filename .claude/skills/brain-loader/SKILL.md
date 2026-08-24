---
name: brain-loader
description: Load persistent brain context — identity, global memory, and project state. Trigger on any mention of {PROJECT_A}, {PROJECT_B}, or {your domain vocabulary — the 5–10 words that mean "this is my work": product names, module numbers, trade terms}. Loads SOUL, global MEMORY, and the mentioned project's STATE + MEMORY only.
---

# Brain Loader

The brain lives at `{ABSOLUTE_PATH_TO_YOUR_BRAIN_REPO}`.

**The frontmatter `description` above is the trigger — edit it first.** Claude's skill
matcher only sees the frontmatter until the skill fires. Placeholders left in place mean
this never triggers and the brain never loads.

## When to trigger

- The session mentions {PROJECT_A} or {PROJECT_B} by name
- The session uses {your domain}'s vocabulary ({term}, {term}, {term})
- The session opens inside the brain repo itself

## Load order

1. **Always:** `.agent/SOUL.md` — identity
2. **Always:** `.agent/MEMORY.md` — global locked + open decisions
3. **Conditional** — pick by what the session mentions, not by directory:
   - {PROJECT_A} → `projects/{project-a}/STATE.md` + `projects/{project-a}/MEMORY.md`
   - {PROJECT_B} → `projects/{project-b}/STATE.md` (STATE only — it's not the active focus)
4. **On reference only:** files under `projects/*/artifacts/` — load when a task names
   them, never preemptively

## Don't load

- Everything. Be surgical: the mentioned project plus its depth, not all projects.
- Anything under `projects/*/artifacts/` without a direct reference.
