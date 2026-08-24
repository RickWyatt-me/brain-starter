---
type: claude-root
confidentiality: internal
updated: {DATE}
---

# Brain — Root Context

This repo is the persistent memory for {YOUR NAME}'s AI agents. Sessions load from it
first and write back to it last.

## Session Start Protocol

1. Read `.agent/SOUL.md` — identity. Always.
2. Read `.agent/MEMORY.md` — global locked + open decisions. Always.
3. Identify the session type, then load surgically:
   - **Working on {PROJECT_A}** → `projects/{project-a}/STATE.md` + `projects/{project-a}/MEMORY.md`
   - **Working on {PROJECT_B}** → `projects/{project-b}/STATE.md` (STATE only unless it becomes the focus)
   - **New project** → copy `projects/_template/` and fill in `STATE.md` before any code
   - **Strategy / cross-cutting** → the STATE.md of every active project, nothing deeper
4. Load artifacts in `projects/*/artifacts/` only when a task references them by name.

Never load everything. The ladder exists so context is spent on the session's actual
subject.

## Internal-Source Handling

Before generating any outward-facing content (README, blog, marketing, docs for
strangers): scan the frontmatter of every source file. `confidentiality: internal`
sources are reference only — model their patterns, never quote their content.

## Session End

Append three lines to `.agent/daily/YYYY-MM-DD.md` under a `## <session name>` heading:

- Worked on: ...
- Decided: ...
- Open: ...

If a decision was locked this session, move it from **Open Decisions** to **Locked
Decisions** in the relevant MEMORY.md before closing out — with a date and who decided.
