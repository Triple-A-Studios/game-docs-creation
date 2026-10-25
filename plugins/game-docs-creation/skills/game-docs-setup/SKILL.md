---
name: game-docs-setup
description: Turns a game idea into Docs/design-doc.md, then scaffolds a Unity/Unreal project's remaining docs (plan.md, progress.md, current-state.md, technical-design-doc.md) and the CLAUDE.md block from it. Use when a developer wants to start a new game project's docs, has a design doc but hasn't scaffolded the project yet, or says things like "let's start building this game," "set up my project docs," "I have an idea for a game," or "move forward with what we have." Also handles the design-doc gap-fill case (an existing design doc with unanswered/placeholder sections). Not for an already-scaffolded project picking up ongoing work — that's game-docs-resume.
---

# Game Docs: Setup (ideation → scaffold)

Turns a game idea into a living design doc, then scaffolds the rest of the project's docs from it. This is a one-time flow per project — once scaffolding (Path B below) finishes, ongoing work belongs to `game-docs-resume`.

Before starting: if `root/Docs/` already has a `plan.md`, `progress.md`, `current-state.md`, or `technical-design-doc.md` that looks non-conforming (not phase/milestone/pass-structured, a monolithic tech doc, merged progress/current-state), invoke `game-docs-conformance` first rather than proceeding — let it migrate the file, then continue here. If you were dispatched to this skill by `game-docs-creation`'s router, that check has already happened; don't repeat it.

This mirrors `game-ideation`'s shape but stays in this repo, writes to one fixed location, and stops at a different point (when the developer is ready to build, not when the doc feels "complete"). It works alongside two other skills rather than duplicating them:
- **`game-ideation`** is the general-purpose, surface-agnostic idea-building skill (works in web/mobile chat too). This skill has its own *lighter*, repo-bound version of that same flow — see [Path A](#path-a-no-design-doc-yet) — because it needs to land in `Docs/design-doc.md` specifically, and needs to know when to stop and move into project scaffolding.
- **`game-critique-panel`** never fires automatically anywhere in this flow. It's the developer's to reach for manually whenever they want outside perspective on the design.

## Path A: No design doc yet

### A1. Locate or start the doc

- If a partially-answered doc already exists in `Docs/` (has `*Not yet discussed*` placeholders or similar), that's your starting point — skip straight to A3, asking only about what's still unanswered.
- If `game-docs-conformance` retired a mis-structured design doc and handed off here, treat its retired content (in `Docs/legacy/`) as the source for this gap-fill, not a blank slate.
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

1. Save the working doc as `Docs/design-doc.md`, **regardless of what it was titled or named before** — this is the one canonical name every other doc and this skill family's detection logic expects.
2. Continue immediately into [Path B](#path-b-design-doc-exists-project-not-yet-scaffolded) in the same session — no need to wait for a re-invocation.

## Path B: Design doc exists, project not yet scaffolded

This is a one-time setup conversation (per project, not per session). Everything here happens once, in order.

**Partial-rescaffold guard**: if you were dispatched here to fill in only a subset of the B7 set (some of `plan.md`/`progress.md`/`current-state.md`/`technical-design-doc.md` already exist and conform — e.g. `game-docs-conformance` just fixed the only broken one, or a previous setup session was interrupted), and the CLAUDE.md block already records the engine and the existing docs already show real phases/milestones, **skip straight to B7** — B1-B6 are a one-time conversation for a genuinely new project, not something to re-run because one file needed regenerating. Only fall through to B1 if the CLAUDE.md block or existing docs don't already answer that step's question.

### B1. Detect the engine

Check for `.uproject` + `Source/` (Unreal) vs `Assets/` + `ProjectSettings/` (Unity). If genuinely ambiguous or neither is present, just ask. Once known, write it into the [CLAUDE.md block](#the-claudemd-block) so it's never re-detected.

### B2. Unity only — ask about test-mode verification

If Unity: ask whether the developer wants NUnit test verification as part of pass completion, or play-mode verification alone. Mention that for small/prototype-scope projects, play-mode-only is usually enough — but it's their call. Store the answer in the CLAUDE.md block; see `references/status-and-engine.md` for how this changes the status list. This is changeable at any point later, just ask again if the developer brings it up — it isn't locked in for the life of the project.

### B3. Audit pre-existing code, if any

If `Assets/`/`Source/` (or equivalent) contains more than engine-default boilerplate, ask whether Claude should audit it into `technical-design-doc.md` now rather than starting that doc empty. If yes, follow the audit approach in `references/status-and-engine.md` — a structural, system-by-system summary plus a Dependencies section for third-party packages, not a line-by-line dump.

### B4. Break the game into phases

Ask the developer to divide the project into: **Prototype → MVP → Alpha → Beta → Full Launch**. Any phase can be skipped (recorded as "Skipped" in `plan.md`/`progress.md`, never silently dropped, so it can be reinstated later if plans change) or the developer can add phases of their own. Record the final phase list.

### B5. Scope the first phase's milestones

For the first non-skipped phase, propose milestone candidates grounded in the design doc (and, if B3 ran an audit, in what's already built). Present them as suggestions, not a fixed list — the developer picks, edits, drops, or adds their own. **The moment the list is finalized, write it into `plan.md`** under that phase's heading (name + one-line "done" definition per the template in `references/doc-templates.md`) — before moving on. Don't leave this step done only in conversation; B7 depends on the phase's milestones already being decided, and a phase's milestones existing only in the conversation while `plan.md` still shows just the bare phase list is a bug, not an acceptable interim state.

### B6. Decide planning granularity

Ask whether the developer wants to plan **all of this phase's milestones' passes now** (one-shot) or **one milestone at a time** as each is reached (incremental). Give a recommendation based on how large the phase looks from the design doc — small/tightly-scoped phases favor one-shot, large or uncertain ones favor incremental — but the developer decides. Store nothing permanent here; `game-docs-resume` asks again per-phase since a later phase might warrant a different answer.

### B7. Create the docs

Idempotent — if any of the four below already exists and conforms (e.g. because `game-docs-conformance` already migrated it, or a prior partial setup created it), skip it; for the rest, generate skeletons, inferring from whichever of the four already exist where possible (e.g. build `current-state.md`'s skeleton from `plan.md`'s milestone list if `plan.md` is already there).

1. `plan.md` — phases + milestones + what "done" means for each, using the template in `references/doc-templates.md`. This is the target; it never records status.
2. `progress.md`, `current-state.md` — created but left as empty skeletons (headers only) until B8/the first pass is defined.
3. `technical-design-doc.md` — empty, or pre-populated with the B3 audit results, split system-wise per `references/doc-templates.md`.
4. Install/update the [CLAUDE.md block](#the-claudemd-block), including the `docs-conformant: true` flag once every present doc has been confirmed to conform.

### B8. Move straight into scoping

Don't stop here — go directly into scoping the first milestone's passes (numbered sequentially, 1, 2, 3...; whether you define all of this milestone's passes now or just the next one follows the B6 choice) so the developer leaves this conversation with an actual next pass to work on. After scoping, update `progress.md`/`current-state.md` per the ownership rules in `references/doc-templates.md`, then hand off to `game-docs-resume` for the actual pass work (branching, commit hygiene) — see its "Working a pass" flow. Optionally mention `game-docs-spec` first: if the first milestone builds a system whose rules aren't pinned down yet (a player controller, combat), the developer can hammer out its spec before or alongside pass scoping. It's an offer, not a step; carry on if they decline.

## The CLAUDE.md block

If `CLAUDE.md` doesn't exist, create one. If it does, only ever touch the section wrapped in:

```
<!-- game-dev-docs:start -->
...
<!-- game-dev-docs:end -->
```

Never edit anything outside those markers. Regenerate the whole block in one go rather than patching pieces of it. It should tell a fresh session: which docs exist and in what order to read them, the detected engine, the Unity testing preference (if applicable), the co-author-line preference (if the developer opted out permanently), the `docs-conformant` flag, and the one-line instruction to always check `current-state.md` before starting work and update it after every commit. Template in `references/doc-templates.md`.

## Reference files

- `references/question-bank.md` — the forked, repo-bound question bank used in Path A. Evolves independently from `game-ideation`'s copy from here on.
- `references/doc-templates.md` — the exact structure for every doc this skill creates, plus the CLAUDE.md block template.
- `references/status-and-engine.md` — the full pass-status state list (including the Unity-testing variant), the engine-specific verification notes, and how the Open/Closed backlog tables work.
