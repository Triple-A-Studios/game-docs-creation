# Spec Templates

Exact structure for the two kinds of files `game-docs-spec` owns. Use as-is; consistency across sessions matters more than any single session's stylistic preference. Spec docs are **optional**: they are not part of the plan/progress/current-state/technical-design set and are never a conformance gate.

Specs lock **rules and intent, never numbers.** Numbers live in each spec's Tunables table as placeholders to be playtested. Specs never record pass status: `current-state.md` owns that.

---

## `Docs/spec-index.md`

Named `spec-index.md` on purpose, not `design-spec.md`: the router fuzzy-matches the substring `design` and would mistake the latter for `design-doc.md`.

```markdown
# Spec Index

How each game system behaves: rules, states, edge cases, tunables. Specs lock rules and intent, not numbers. Read the relevant spec before touching a system; `game-docs-resume` updates it if the implementation deviates.

| System | Status | Spec | Built in milestone |
|---|---|---|---|
| Player controller | Hammered | [player-controller](specs/player-controller.md) | Movement |
| Combat | Draft | [combat](specs/combat.md) | Combat |
| Run generation | Not started | — | — |
```

Status values:
- **Not started**: listed, no spec file yet.
- **Draft**: interview in progress, or Open Questions remain that affect rules.
- **Hammered**: rules are settled. Only tunables and playtest items remain. The developer decides when a spec is Hammered.

The "Built in milestone" column is filled in once a milestone from `plan.md` actually builds the system; leave `—` until then.

---

## `Docs/specs/<system>.md`

One file per system. Filenames are kebab-case. Links to other specs are relative.

```markdown
# <System name> spec

## Purpose
One or two sentences: what this system does for the player and the game.

## Rules
The logic, in the order it happens, under what conditions. Rules only, no numbers.

## States & transitions
| State | Enters from | Leaves to | Interruptible? |
|---|---|---|---|

## Tunables
| Parameter | Placeholder | Notes | Playtest |
|---|---|---|---|
| Coyote time | ~0.1s | Feels forgiving without being floaty | Yes |

Placeholders are starting points, not decisions. Mark `Yes` for anything that needs playtesting.

## Edge cases
- <case>: <what happens>

## Interactions
- [Combat](combat.md): <what this system reads from or changes in combat>

Every interaction is recorded in both systems' specs.

## Open questions
- <question not yet decided>

## Changelog
- <date>: <one-line change>, <why> (added by `game-docs-resume` when an implementation deviates from the spec)
```

Sections that don't apply yet (for example Feedback & feel, when the design doc has no feel target) may be omitted rather than left empty. Add a section when a lens produces content for it.
