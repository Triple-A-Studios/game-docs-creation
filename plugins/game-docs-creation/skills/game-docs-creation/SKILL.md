---
name: game-docs-creation
description: Manages the full project-documentation and phase/milestone/pass workflow for Unity and Unreal game projects inside Claude Code — creates and maintains Docs/design-doc.md, plan.md, progress.md, current-state.md, and a system-organized technical-design-doc.md, keeps a managed section of CLAUDE.md current so every fresh session knows what to read, and enforces git hygiene (one branch per milestone, manual verification before any commit, caveman-style commit messages). Use this whenever the developer is working in a Unity or Unreal repo and wants to start a new game project's docs, pick up where they left off, check what milestone/pass they're on, scope the next milestone or phase, or commit finished work. Also trigger near the start of a coding session in a Unity/Unreal repo so Claude knows whether project setup is needed before doing anything else. Trigger phrases include "let's start building this game," "what's next," "set up my project docs," "what pass am I on," "I finished this pass," or any mention of phases/milestones/passes/backlog in a game-dev context.
---

# Game Dev Docs (Unity/Unreal)

Turns a game idea into a living set of project docs, then keeps them honest pass by pass. This skill has one job above all else: **make sure `Docs/current-state.md` always tells the truth about where the project actually is**, so that any fresh Claude Code session (or any teammate) can open the repo cold and know exactly what's landed, what's next, and what's still open.

It works alongside two other skills rather than duplicating them:
- **`game-ideation`** is the general-purpose, surface-agnostic idea-building skill (works in web/mobile chat too). This skill has its own *lighter*, repo-bound version of that same flow — see [Path A](#path-a-no-design-doc-yet) — because it needs to land in `Docs/design-doc.md` specifically, and needs to know when to stop and move into project scaffolding.
- **`game-critique-panel`** never fires automatically anywhere in this flow. It's the developer's to reach for manually whenever they want outside perspective on the design.

## Step 0 — Figure out where this project is

Every invocation starts here. Look in `root/Docs/` for files matching (case-insensitively, substring or close variants of) `design`, `plan`, `progress`, `current-state`, `technical-design`. Also read the managed block in `CLAUDE.md` if one exists (see [CLAUDE.md block](#the-claudemd-block)) — it carries engine, testing preference, and co-author preference so you don't have to re-detect them every time.

| Found | Situation | Go to |
|---|---|---|
| No design-doc-like file | Nothing started yet | [Path A](#path-a-no-design-doc-yet) |
| Design doc exists, but no `plan.md` | Idea is settled, project isn't scaffolded | [Path B](#path-b-design-doc-exists-project-not-yet-scaffolded) |
| All five docs exist | Project is up and running | [Path C](#path-c-project-scaffolded--resume-or-scope-next) |

A design doc that exists but still has unanswered/placeholder sections is **not** "no design doc" — it's Path A's fill-the-gaps case (below), not Path B. Don't skip straight to scaffolding on an incomplete doc.

## Path A: No design doc yet

This mirrors `game-ideation`'s shape but stays in this repo, writes to one fixed location, and stops at a different point (when the developer is ready to build, not when the doc feels "complete").

### A1. Locate or start the doc

- If a partially-answered doc already exists in `Docs/` (has `*Not yet discussed*` placeholders or similar), that's your starting point — skip straight to A3, asking only about what's still unanswered.
- Otherwise, start a short chat-only conversation, no written doc yet.

### A2. Light pass (new ideas only)

Pull the **Tier 1** questions from `references/question-bank.md`, categories **Core Concept & Hook**, **Core Gameplay Loop & Mechanics**, and **Player Experience, Emotion & Fantasy** only — skip the Genre/Platform confirmation entirely, since engine is already implied by the repo you're sitting in. Ask a couple at a time, conversationally, not as a recited list. This should feel like 4-6 exchanges, not an interview. The goal is just enough shape to seed phase/milestone planning later, not a build-ready GDD.

Write a short recap into a working doc (not yet `Docs/design-doc.md`) as you go, organized by category, same as `game-ideation` does — this is the one doc for the rest of this path, never spin up a second file.

### A3. Offer depth

Once the light pass has a coherent shape (or once the gap-fill in A1 is done), ask the developer directly: *go deeper on this now, or move forward with what we have?*

- **Deeper** → build the full tiered questionnaire (T2 + T3, covered/not-covered logic exactly as `game-ideation` step 3) from `references/question-bank.md`, hand it off, merge answers back into the same doc when returned. Ask again after merging — this can repeat.
- **Move forward** → leave any untouched categories as `*Not yet discussed*` placeholders and proceed. Nothing is lost; they're revisitable later by reopening the doc.

### A4. Hand off to project setup

Watch for the developer signaling readiness — phrases like "let's start building," "move forward," "start coding," "let's go." That's the trigger, not a fixed number of questions answered. When it happens:

1. Save the working doc as `Docs/design-doc.md`, **regardless of what it was titled or named before** — this is the one canonical name every other doc and this skill's own detection logic expects.
2. Continue immediately into [Path B](#path-b-design-doc-exists-project-not-yet-scaffolded) in the same session — no need to wait for a re-invocation.

## Path B: Design doc exists, project not yet scaffolded

This is a one-time setup conversation (per project, not per session). Everything here happens once, in order.

### B1. Detect the engine

Check for `.uproject` + `Source/` (Unreal) vs `Assets/` + `ProjectSettings/` (Unity). If genuinely ambiguous or neither is present, just ask. Once known, write it into the [CLAUDE.md block](#the-claudemd-block) so it's never re-detected.

### B2. Unity only — ask about test-mode verification

If Unity: ask whether the developer wants NUnit test verification as part of pass completion, or play-mode verification alone. Mention that for small/prototype-scope projects, play-mode-only is usually enough — but it's their call. Store the answer in the CLAUDE.md block; see `references/status-and-engine.md` for how this changes the status list. This is changeable at any point later, just ask again if the developer brings it up — it isn't locked in for the life of the project.

### B3. Audit pre-existing code, if any

If `Assets/`/`Source/` (or equivalent) contains more than engine-default boilerplate, ask whether Claude should audit it into `technical-design-doc.md` now rather than starting that doc empty. If yes, follow the audit approach in `references/status-and-engine.md` — a structural, system-by-system summary plus a Dependencies section for third-party packages, not a line-by-line dump.

### B4. Break the game into phases

Ask the developer to divide the project into: **Prototype → MVP → Alpha → Beta → Full Launch**. Any phase can be skipped (recorded as "Skipped" in `plan.md`/`progress.md`, never silently dropped, so it can be reinstated later if plans change) or the developer can add phases of their own. Record the final phase list.

### B5. Scope the first phase's milestones

For the first non-skipped phase, propose milestone candidates grounded in the design doc (and, if B3 ran an audit, in what's already built). Present them as suggestions, not a fixed list — the developer picks, edits, drops, or adds their own. See [Scoping milestones and passes](#scoping-milestones-and-passes) for the suggestion pattern.

### B6. Decide planning granularity

Ask whether the developer wants to plan **all of this phase's milestones' passes now** (one-shot) or **one milestone at a time** as each is reached (incremental). Give a recommendation based on how large the phase looks from the design doc — small/tightly-scoped phases favor one-shot, large or uncertain ones favor incremental — but the developer decides. Store nothing permanent here; this is asked again per-phase in [Path C](#path-c-project-scaffolded--resume-or-scope-next) since a later phase might warrant a different answer.

### B7. Create the docs

1. `plan.md` — phases + milestones + what "done" means for each, using the template in `references/doc-templates.md`. This is the target; it never records status.
2. `progress.md`, `current-state.md` — created but left as empty skeletons (headers only) until B8/the first pass is defined.
3. `technical-design-doc.md` — empty, or pre-populated with the B3 audit results, split system-wise per `references/doc-templates.md`.
4. Install/update the [CLAUDE.md block](#the-claudemd-block).

### B8. Move straight into scoping

Don't stop here — go directly into [Scoping milestones and passes](#scoping-milestones-and-passes) for the first milestone of the first phase, so the developer leaves this conversation with an actual next pass to work on.

## Path C: Project scaffolded — resume or scope next

This is the common case on every later invocation. Read `current-state.md`'s live snapshot first.

| current-state.md shows | Do this |
|---|---|
| A defined next pass | Jump straight to [Working a pass](#working-a-pass--commit-hygiene) — no scoping conversation needed |
| Current milestone's passes all landed/verified, next milestone has no passes defined | Check the backlog (below), then scope passes for the next milestone — see [Scoping milestones and passes](#scoping-milestones-and-passes) |
| Current phase's milestones exhausted, next phase has no milestones defined | Check the backlog, then run B5+B6+scoping for the next phase, same pattern as first-time setup |

**Always check the backlog table in `progress.md` before scoping anything new** (a milestone's passes, or a phase's milestones): ask the developer whether any open backlog items (deferred features, known bugs) should be folded into what's about to be scoped. An item only clears the backlog once it's actually folded into a pass that lands — see `references/status-and-engine.md` for how backlog rows get created in the first place.

## Scoping milestones and passes

**Milestones** (within a phase): propose candidates from the design doc plus what's already documented in `technical-design-doc.md`/`progress.md`; the developer picks, edits, or adds their own. Never present a take-it-or-leave-it list — these are starting points.

**Passes** (within a milestone): number sequentially — 1, 2, 3... Whether you define all of a milestone's passes now or just the next one follows the B6 choice (or, for later milestones/phases, ask again since scope may differ). Before finalizing, fold in any backlog items the developer chose to include in Path C's check-in.

**Sub-passes** (within a pass): never defined upfront. Only break a pass into sub-passes (1.a, 1.b...) when the developer actually reaches that pass and it turns out to be big enough to warrant it. Decide this in the moment, not during milestone scoping.

After scoping, update `plan.md` (if new milestones/phases were added to the target) and `progress.md`/`current-state.md` per the ownership rules in `references/doc-templates.md` — the short version: `progress.md` freezes a full pass table only for *landed* milestones and just says "In Progress — see current-state.md" for the active one; `current-state.md` is the only place live pass-by-pass status actually lives.

## Working a pass & commit hygiene

1. **Branch**: one git branch per milestone. If not already on that milestone's branch, create/check it out before writing any code. Passes within a milestone are just sequential commits on that branch — never a sub-branch per pass.
2. **Write the code**, referencing the relevant system section(s) of `technical-design-doc.md` first so new work stays consistent with what's already documented.
3. **Never commit without explicit manual verification.** Wait for the developer to confirm they've checked the change in-editor (and, for Unity with testing enabled, that tests pass too) — see the status list in `references/status-and-engine.md` for exactly what "verified" requires per engine/testing setup, including the Unreal build-before-play and Unity scene-reload notes.
4. **Commit message**: use the `caveman` skill if it's installed in this environment; if not, fall back to a normal, short, and concise message — don't pad it out just because caveman styling isn't available.
5. **Co-authored-by Claude line**: ask once per milestone (first commit on that branch), then remember the answer for the rest of the milestone. If the developer says never to ask again for this project, record that as a standing preference in the CLAUDE.md block and stop asking entirely.
6. **After the commit**: update `current-state.md` (status + commit hash, per `references/status-and-engine.md`'s state list) and, if the pass landed *incomplete*, add its leftover items as new rows in `progress.md`'s backlog table. If this was the milestone's last pass and it verified complete, freeze the milestone's table into `progress.md`, clear the "current" pointer in `current-state.md`, and check whether the next milestone already has passes defined — if not, that's the next thing to scope (now, if the developer wants to keep going, or on the next invocation).

## The CLAUDE.md block

If `CLAUDE.md` doesn't exist, create one. If it does, only ever touch the section wrapped in:

```
<!-- game-dev-docs:start -->
...
<!-- game-dev-docs:end -->
```

Never edit anything outside those markers. Regenerate the whole block in one go rather than patching pieces of it. It should tell a fresh session: which docs exist and in what order to read them, the detected engine, the Unity testing preference (if applicable), the co-author-line preference (if the developer opted out permanently), and the one-line instruction to always check `current-state.md` before starting work and update it after every commit.

## Reference files

- `references/question-bank.md` — the forked, repo-bound question bank used in Path A. Evolves independently from `game-ideation`'s copy from here on.
- `references/doc-templates.md` — the exact structure for every doc this skill creates, plus the CLAUDE.md block template.
- `references/status-and-engine.md` — the full pass-status state list (including the Unity-testing variant), the engine-specific verification notes, and how the backlog table works.
