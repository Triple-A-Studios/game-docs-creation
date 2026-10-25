---
name: game-docs-creation
description: Entry point for the game-docs skill family (game-docs-setup, game-docs-resume, game-docs-conformance, game-docs-spec) for Unity/Unreal projects — figures out which stage a project's Docs/ folder is in and dispatches to the right skill. Use this whenever you're not sure which game-docs skill applies: at the start of a coding session in a Unity/Unreal repo, or whenever the developer's request is ambiguous about whether they want to start fresh, resume ongoing work, fix broken docs, or pin down how a game system works. If it's already clear which applies (a brand-new idea, an obviously-scaffolded project asking "what's next," docs the developer says don't conform, or "spec out the combat rules"), invoke that skill directly instead of this one.
---

# Game Dev Docs (Unity/Unreal) — router

This skill's only job is Step 0: look at `root/Docs/` and dispatch to the right specialist skill via the Skill tool. It never does the actual work itself.

## Step 0 — Figure out where this project is

Look at the top-level files of `root/Docs/` only. Don't recurse into subfolders: `Docs/legacy/` holds retired pre-migration files, `Docs/technical-design/` holds per-system technical docs, and `Docs/specs/` holds per-system specs, none of which are doc-set members. Match files whose names resemble (case-insensitively, substring or close variants of) (case-insensitively, substring or close variants of) `design`, `plan`, `progress`, `current-state`, `technical-design`. Also read the managed block in `CLAUDE.md` if one exists — it carries engine, testing preference, co-author preference, and the `docs-conformant` flag so you don't have to re-detect them every time.

`plan.md`, `progress.md`, `current-state.md`, and `technical-design-doc.md` are referred to below as **the B7 set** (they're created together during scaffolding). `design-doc.md` is always checked separately since everything else depends on it. `Docs/spec-index.md` and `Docs/specs/` (owned by `game-docs-spec`) are optional and deliberately outside the B7 set: their absence never changes the route below.

| `design-doc.md` | B7 set | Conformance | Route |
|---|---|---|---|
| Absent | Absent | — | Invoke **`game-docs-setup`** — fresh start |
| Absent | Any member present | — | **Anomaly** — a plan or tech doc existing with no design doc is abnormal. Ask the developer whether one of the existing docs should be adopted/renamed as `design-doc.md` before invoking anything. Don't default to setup. |
| Present, has placeholders (`*Not yet discussed*` or similar) | any | — | Invoke **`game-docs-setup`** — gap-fill; ignore B7-set state until the design doc is settled |
| Present, fully answered but doesn't map to `question-bank.md`'s categories at all (not just missing answers — a genuinely different shape) | any | — | Invoke **`game-docs-conformance`** first (it retires the old doc and hands back to setup's gap-fill against that content), then **`game-docs-setup`** |
| Present, complete and conforming | Absent | — | Invoke **`game-docs-setup`** — fresh scaffold |
| Present, complete and conforming | Partial (some but not all four exist) | Check existing members ([below](#checking-conformance)) | If all existing members conform: invoke **`game-docs-setup`** (its doc-creation step is idempotent — skips what exists, fills in the rest; its partial-rescaffold guard skips B1-B6 if that info already exists). If any existing member fails: invoke **`game-docs-conformance`** first, then **`game-docs-setup`**. |
| Present, complete and conforming | Full set present | All conform | Invoke **`game-docs-resume`** |
| Present, complete and conforming | Full set present | One or more fail | Invoke **`game-docs-conformance`** first, then **`game-docs-resume`** |

A design doc that exists but still has unanswered/placeholder sections is **not** "no design doc" — route to `game-docs-setup`'s gap-fill case, not a fresh scaffold. Don't skip straight to scaffolding on an incomplete doc.

### Checking conformance

Don't read every doc in full just to route — that's wasted cost on the common case where everything already matches this skill family's structure. Instead:

1. **Cheap structural pass, once per project.** Only for files that are actually present, and only if the CLAUDE.md block doesn't already say `docs-conformant: true`. Grep-level checks, not full reads: does `design-doc.md` (once past the placeholder check above) read as organized by `question-bank.md`'s categories at all, even loosely — not a different shape entirely? Does `plan.md` have phase/milestone/pass headers? Does `technical-design-doc.md` read as an index — a `## Systems` section linking out to separate per-system files — rather than one undivided dump of prose/code sitting directly in the file? If both `progress.md` and `current-state.md` exist, are they structurally distinct (different header sets) rather than one's content duplicated into the other? If only one of the two exists, don't wave it through by default — check whether it reads as carrying live/active status (language like "currently," "in progress," "next up," a named active task) rather than pure frozen history; if so it's silently playing both roles and fails conformance even though its sibling is merely absent, not overlapping.
2. **Full read only on suspicion.** If the structural pass flags a file, that's a failure for routing purposes — the actual full read and migration happen inside `game-docs-conformance`, not here.
3. Once routed, `game-docs-conformance` is responsible for caching `docs-conformant: true` in the CLAUDE.md block after migration finishes, so future invocations of this router skip the structural pass entirely.

## Dispatching

If the developer's request is specifically about defining how a game system behaves (rules, states, edge cases, tunables for the player controller, combat, and so on), invoke **`game-docs-spec`** directly instead of routing through the table above; it only needs `design-doc.md` to exist.

Once the route is decided, invoke the target skill via the Skill tool and stop — don't start doing that skill's work yourself. If a route needs two skills in sequence (a conformance fix before setup/resume), invoke the first, let it finish, then invoke the second.
