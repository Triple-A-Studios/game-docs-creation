# Doc Templates

Exact structure for every file this skill owns. Use these as-is — consistency across sessions matters more than any single session's stylistic preference. All five docs live in `root/Docs/`. Optional system specs (`Docs/spec-index.md` and `Docs/specs/<system>.md`) are owned by the `game-docs-spec` skill, which keeps its own templates; they are not part of this set.

## Filename rule

**Reading**: fuzzy-match. Any file whose name resembles `design`, `plan`, `progress`, `current-state`, or `technical-design` (case-insensitive, common variants like `design-spec`, `gdd`, `concept` for the design doc) counts as that doc for detection purposes.

**Writing**: always the canonical name, regardless of what a pre-existing file was called: `design-doc.md`, `plan.md`, `progress.md`, `current-state.md`, `technical-design-doc.md`.

---

## `design-doc.md`

Organized by the same categories as `references/question-bank.md` — one section per category, a short recap in the developer's own words for anything discussed, `*Not yet discussed*` for anything not yet touched. This is a living document; it can be reopened and extended even after project setup has happened.

---

## `plan.md`

The target only. **Never contains status** — that's `progress.md`'s job entirely. If scope changes, edit this file directly; don't let status docs drift out of sync with it.

```markdown
# Plan

## Phases

### Phase: Prototype
Milestones:
- **<Milestone name>** — <one-line definition of done>
- **<Milestone name>** — <one-line definition of done>

### Phase: MVP
Milestones:
- ...

### Phase: Alpha
...

### Phase: Beta
...

### Phase: Full Launch
...

### Phase: <Custom phase, if any> — Skipped
```

A skipped phase keeps its heading with `— Skipped` so it's visibly a deliberate choice, not a gap, and can have milestones added later if the developer changes their mind.

---

## `progress.md`

The historical record. Owns the **backlog tables** (Open and Closed). For any given milestone, only one of these three forms is present at a time:

```markdown
# Progress

## Backlog — Open

| No. | Item | Origin | Type |
|---|---|---|---|
| 1 | <short description> | [Pass 2, Milestone: Movement](current-state.md) | Deferred feature |
| 2 | <short description> | [Pass 1, Milestone: Movement](current-state.md) | Bug |

## Backlog — Closed

| No. | Item | Origin | Type | Status |
|---|---|---|---|---|
| 3 | <short description> | [Pass 1, Milestone: Movement](current-state.md) | Bug | Resolved |

## Phase: Prototype

### Milestone: Movement — Completed

| Pass | Status | Commit | Target |
|---|---|---|---|
| 1 | Verified — Complete | `abc1234` | Basic WASD movement |
| 2 | Landed — Incomplete | `def5678` | Jump + double jump (double jump deferred, see backlog) |

### Milestone: Combat — In Progress
See [current-state.md](current-state.md) for live pass status.

### Milestone: Enemy AI — Upcoming
See [plan.md](plan.md#phase-prototype) for scope.

## Phase: MVP — Upcoming
See [plan.md](plan.md#phase-mvp) for scope.
```

Rules:
- A **completed** milestone gets its full pass table frozen in permanently — copy it from `current-state.md` at the moment the milestone lands, then it stops changing.
- The **active** milestone never gets its own pass table here — just the one-line pointer to `current-state.md`. This is the single biggest anti-duplication rule in this whole skill: don't let two files claim to be the live source of truth for the same pass.
- **Upcoming** milestones/phases get a name and a link into `plan.md` — never a restated definition of done.
- Rows are added to **Backlog — Open** automatically whenever a pass lands incomplete (mirroring that pass's "features left" note) or when a bug surfaces during verification that isn't tied to a specific pass. Each row gets the next sequential `No.`, never reused.
- Once an open item is folded into a pass that lands, move its row to **Backlog — Closed** (same `No.`, add a `Status` of `Resolved`) and remove it from the Open table — never delete it outright, and never leave it duplicated in both tables.

---

## `current-state.md`

Live snapshot. The only place pass-by-pass status for the *active* milestone actually lives. Keep this lean — extra context belongs in `progress.md`, linked from here, not duplicated here.

```markdown
# Current State

## Snapshot
- **Last landed:** Pass 2 — Jump + double jump (`def5678`, incomplete — double jump deferred)
- **Now:** Pass 3 — Basic melee attack
- **Before you test:** <engine-specific verification note, e.g. "Rebuild before entering Play-In-Editor" or "Reload the open scene first">

## Milestone: Combat — passes

| Pass | Status | Notes / Features | Target |
|---|---|---|---|
| 1 | Verified — Complete (`aa11bb2`) | Hitbox detection | Melee range check |
| 2 | Landed — Incomplete (`def5678`) | Jump works; double jump deferred, see [backlog](progress.md#backlog--open) | Double jump |
| 3 | In Progress | — | Basic melee attack |
| 3.a | Not Started | — | Attack animation trigger |
| 3.b | Not Started | — | Damage application |

See [progress.md](progress.md) for the full project history and backlog, and [plan.md](plan.md) for what comes after this milestone.
```

Sub-passes (`3.a`, `3.b`, ...) are only added to the table once the current pass actually gets split — never pre-listed for future passes.

---

## `technical-design-doc.md`

An index, not a monolith — this doc only grows for the life of the project, so it's split system-wise from the start.

```markdown
# Technical Design Doc

Index of documented systems. Read the relevant file(s) before working in that system; update the file after every pass that touches it.

## Systems
- [Movement](technical-design/movement.md)
- [Combat](technical-design/combat.md)
- [Enemy AI](technical-design/enemy-ai.md)

## Dependencies
- [Third-party packages & plugins](technical-design/dependencies.md)
```

Each linked file (under `Docs/technical-design/`) documents one system: what it does, how it's structured, the key files/classes involved, and anything a future session needs to know before touching it. `dependencies.md` lists third-party packages/plugins/frameworks (from a B3 audit or added later) — name, purpose, and anything unusual about how it's integrated.

---

## The CLAUDE.md block

```markdown
<!-- game-dev-docs:start -->
## Game Dev Docs

This project uses the `game-dev-docs` skill. Before doing any work in a fresh session:
1. Read `Docs/current-state.md` first — the live snapshot of what's landed and what's next.
2. Read `Docs/plan.md` and `Docs/progress.md` for phase/milestone context as needed.
3. Read the relevant file(s) in `Docs/technical-design/` before touching a system you haven't worked in this session. If `Docs/specs/` exists, also read that system's spec there; it holds the intended rules.

**Project settings**
- Engine: <Unity | Unreal>
- Unity testing: <NUnit enabled | Play-mode only> <!-- omit this line for Unreal -->
- Co-author line: <ask once per milestone | never ask again for this project>

**Working hygiene**
- One git branch per milestone.
- Never commit without the developer's explicit manual verification.
- Commit messages: use the `caveman` skill if installed, else keep them short and plain.
- Update `Docs/current-state.md` after every commit.
<!-- game-dev-docs:end -->
```

Regenerate the whole block in one edit when anything inside it changes — never hand-patch a single line, since stale surrounding text is easy to miss.
