# Status States, Engine Specifics, and the Backlog

## The status list

This is the only vocabulary `current-state.md` (and the frozen tables in `progress.md`) should ever use for a pass's status. Don't invent new labels.

**Unreal, or Unity with testing disabled** (7 states):

1. **Not Started**
2. **In Progress**
3. **Completed — Not Verified** — coding is done; the developer still needs to check it in-editor.
4. **Verified — Complete** `(commit hash)` — the developer confirmed it in-editor and every described feature is present. Ready to commit, or already committed.
5. **Landed — Incomplete** `(commit hash)` — committed, but some described feature(s) or a known bug were deliberately left, because they're non-blocking or not prioritized right now. The pass's Notes/Features cell must say exactly what was left — that text is what feeds the backlog (see below).
6. **Skipped for Later** — deliberately deferred, not started, not blocking anything downstream right now.
7. **Omitted / Deprecated** — decided against entirely; kept in the table so the decision is visible rather than silently erased.

**Unity with testing enabled** (8 states) — insert one between 3 and 4:

3. Completed — Not Verified
4. **Completed — Tests Verified, Play Mode Unverified** — NUnit tests pass, but the developer hasn't confirmed it in Play Mode yet. A pass can't skip this rung when testing is enabled; it has to pass tests before play-mode verification counts.
5. Verified — Complete `(commit hash)`
6. Landed — Incomplete `(commit hash)`
...and so on, same as the 7-state list.

The Unity testing preference lives in the CLAUDE.md block (see `references/doc-templates.md`) and can be changed anytime the developer brings it up — it's not locked in at project setup.

### Landed vs. Verified — the distinction that matters most

These are easy to conflate; don't. **Verified — Complete** means the pass does everything it was scoped to do. **Landed — Incomplete** means it was committed anyway, on purpose, because what's missing genuinely isn't blocking. The tell is always the Notes/Features cell: if it names something left out, the pass is Landed — Incomplete, not Verified — Complete, even if the developer is happy to commit it as-is.

## Engine-specific verification

These are **not** separate statuses — they're a note, not a state. Write them into `current-state.md`'s "Before you test" line in the Snapshot section, refreshed whenever it's newly relevant:

- **Unreal**: code changes need a build/compile before the developer can Play-In-Editor to verify. Say so explicitly rather than assuming the developer remembers, especially after a session gap.
- **Unity**: if there's an already-open scene, it sometimes needs a reload before a code change takes effect in Play Mode. Flag it when it's likely to matter (e.g., after editing a MonoBehaviour that's already active in the open scene).

This is exactly why **Completed — Not Verified** exists as its own state, separate from **Verified**: it marks "the code is done" without implying "and the developer has actually seen it work," which is a meaningfully different fact given these extra editor-side steps.

## Pre-existing code / package audit (Path B3)

When there's existing code or packages to fold into `technical-design-doc.md` before it starts empty:

- **Depth**: a structural, system-by-system summary — what each system does and how it's organized — not a line-by-line walkthrough. This is a reference doc, not a code dump.
- **Third-party packages/plugins**: their own section (`technical-design/dependencies.md`), listing name, purpose, and anything unusual about how it's integrated, so a future session knows what's already available instead of reinventing it.
- Split the output the same way any other technical-design content is split — one file per system under `Docs/technical-design/`, indexed from `technical-design-doc.md`.

## The backlog

Lives as two tables inside `progress.md` — **Backlog — Open** and **Backlog — Closed** (see `references/doc-templates.md` for their exact shape). Two ways a row gets added to Open:

1. **Automatically**, whenever a pass lands as **Landed — Incomplete** — mirror whatever the Notes/Features cell says was left, as a new backlog row linked back to that pass. Nothing needs to be separately remembered; it falls out of updating the docs after the commit.
2. **Manually**, when a bug surfaces during verification that isn't tied to a specific pass's leftover scope — add it the same way, tagged with where it was found.

Each row gets the next sequential `No.` when added, never reused. Once it's actually folded into a pass that lands, move the row from Open to Closed (same `No.`, add a `Status` of `Resolved`) — never delete it outright, and never leave it duplicated in both tables; the record should stay honest about what was deferred and for how long. Before scoping any new milestone's passes or any new phase's milestones, check the Open table and ask the developer whether any open item should be folded in now — see Path C in SKILL.md.
