---
name: game-docs-resume
description: Resumes ongoing work on an already-scaffolded Unity/Unreal project — reads Docs/current-state.md to pick up where the developer left off, scopes the next milestone's or phase's passes when the current one is exhausted, and enforces commit hygiene (one branch per milestone, manual verification before any commit, caveman-style commit messages) while keeping current-state.md/progress.md honest. Use when the developer says "what's next," "what pass am I on," "I finished this pass," wants to scope the next milestone or phase, or is about to commit finished work in a Unity/Unreal repo that already has its docs scaffolded. Not for a project with no design-doc.md yet or no plan.md yet — that's game-docs-setup.
---

# Game Docs: Resume & pass work

Keeps `Docs/current-state.md` telling the truth about where the project actually is, so any fresh session (or teammate) can open the repo cold and know what's landed, what's next, and what's still open. This is the common case on every invocation once a project is scaffolded.

Before starting: if `plan.md`, `progress.md`, `current-state.md`, or `technical-design-doc.md` looks non-conforming (not phase/milestone/pass-structured, a monolithic tech doc, merged progress/current-state), invoke `game-docs-conformance` first rather than proceeding — let it migrate the file, then continue here. If you were dispatched to this skill by `game-docs-creation`'s router, that check has already happened; don't repeat it. If `design-doc.md` or `plan.md` don't exist at all yet, this isn't the right skill — use `game-docs-setup` instead.

## Resume logic

Read `current-state.md`'s live snapshot first.

| current-state.md shows | Do this |
|---|---|
| A defined next pass | Jump straight to [Working a pass](#working-a-pass--commit-hygiene) — no scoping conversation needed |
| Current milestone's passes all landed/verified, next milestone has no passes defined | Check the backlog (below), then scope passes for the next milestone — see [Scoping milestones and passes](#scoping-milestones-and-passes) |
| Current phase's milestones exhausted, next phase has no milestones defined | Check the backlog, then scope the next phase's milestones the same way the first phase was scoped, then scope its first milestone's passes — see [Scoping milestones and passes](#scoping-milestones-and-passes) |

**Always check the Backlog — Open table in `progress.md` before scoping anything new** (a milestone's passes, or a phase's milestones): ask the developer whether any open backlog items (deferred features, known bugs) should be folded into what's about to be scoped. An item only clears the backlog once it's actually folded into a pass that lands — see `references/status-and-engine.md` for how backlog rows get created in the first place.

**Also check for system specs before scoping a milestone's passes.** If `Docs/spec-index.md` exists, find the system(s) the milestone builds. If one is missing from the index, or its status is `Not started` or `Draft`, offer to invoke `game-docs-spec` first so the rules are settled before passes are cut against them. The developer may decline and proceed; this is an offer, never a gate. If `Docs/spec-index.md` doesn't exist at all, mention once that `game-docs-spec` is available, then carry on without asking again.

## Scoping milestones and passes

**Milestones** (within a phase, only needed when a new phase is starting): propose candidates from the design doc plus what's already documented in `technical-design-doc.md`/`progress.md`; for an `asset` project's Release Prep phase, seed them from the release checklist in `references/status-and-engine.md` for the distribution channel(s) the design doc names. The developer picks, edits, or adds their own. Never present a take-it-or-leave-it list — these are starting points. **The moment the list is finalized, write it into `plan.md`** under that phase's heading (name + one-line "done" definition per the template in `references/doc-templates.md`) — before moving on to pass-scoping or any code. This isn't an optional follow-up: a phase's milestones existing only in the conversation, with `plan.md` still showing just the bare phase list, is a bug, not an acceptable interim state. Also ask whether the developer wants to plan all of this phase's milestones' passes now (one-shot) or one milestone at a time (incremental) — give a recommendation based on how large the phase looks, but the developer decides; ask again for each new phase since scope may differ.

**Passes** (within a milestone): number sequentially — 1, 2, 3... Whether you define all of a milestone's passes now or just the next one follows the one-shot/incremental choice above. Before finalizing, fold in any backlog items the developer chose to include in the check-in above.

**Sub-passes** (within a pass): never defined upfront. Only break a pass into sub-passes (1.a, 1.b...) when the developer actually reaches that pass and it turns out to be big enough to warrant it. Decide this in the moment, not during milestone scoping.

After scoping (milestones are already written to `plan.md` per above by this point), update `progress.md`/`current-state.md` per the ownership rules in `references/doc-templates.md` — the short version: `progress.md` freezes a full pass table only for *landed* milestones and just says "In Progress — see current-state.md" for the active one; `current-state.md` is the only place live pass-by-pass status actually lives.

## Working a pass & commit hygiene

1. **Branch**: one git branch per milestone. If not already on that milestone's branch, create/check it out before writing any code. Passes within a milestone are just sequential commits on that branch — never a sub-branch per pass.
2. **Write the code**, referencing the relevant system section(s) of `technical-design-doc.md` first so new work stays consistent with what's already documented. If a spec exists for the system being built (`Docs/specs/<system>.md`), read it too: it holds the intended rules, and its Tunables table is where placeholder values come from.
3. **Never commit without explicit manual verification.** Wait for the developer to confirm they've checked the change in-editor (and, for Unity with testing enabled, that tests pass too) — see the status list in `references/status-and-engine.md` for exactly what "verified" requires per engine/testing setup, including the Unreal build-before-play and Unity scene-reload notes. For an `asset` project (`Project type: asset` in the CLAUDE.md block), "verified" also means checked in a clean host project against the compatibility matrix the design doc claims; put that in the "Before you test" line.
4. **Commit message**: use the `caveman` skill if it's installed in this environment; if not, fall back to a normal, short, and concise message — don't pad it out just because caveman styling isn't available.
5. **Co-authored-by Claude line**: ask once per milestone (first commit on that branch), then remember the answer for the rest of the milestone. If the developer says never to ask again for this project, record that as a standing preference in the CLAUDE.md block and stop asking entirely.
6. **After the commit**: update `current-state.md` (status + commit hash, per `references/status-and-engine.md`'s state list) and, if the pass landed *incomplete*, add its leftover items as new rows in `progress.md`'s Backlog — Open table. If the implementation deviated from a spec in `Docs/specs/`, update that spec's rules and add a Changelog line; if the deviation looks like a deliberate design change rather than a shortcut, confirm with the developer first. Specs never record pass status. If this was the milestone's last pass and it verified complete, freeze the milestone's table into `progress.md`, clear the "current" pointer in `current-state.md`, and check whether the next milestone already has passes defined — if not, that's the next thing to scope (now, if the developer wants to keep going, or on the next invocation).

## The CLAUDE.md block

Only ever touch the section wrapped in `<!-- game-dev-docs:start -->` / `<!-- game-dev-docs:end -->` markers (template in `references/doc-templates.md`); never edit anything outside them, and regenerate the whole block in one go rather than patching pieces of it. This skill updates it when: the co-author-line preference changes (step 5 above), or the Unity testing preference changes (if the developer brings it up again).

## Reference files

- `references/doc-templates.md` — the exact structure for every doc, plus the CLAUDE.md block template.
- `references/status-and-engine.md` — the full pass-status state list (including the Unity-testing variant), the engine-specific verification notes, and how the Open/Closed backlog tables work.
