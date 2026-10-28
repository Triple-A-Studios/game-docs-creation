---
name: game-docs-spec
description: Hammers out how each system in a Unity/Unreal game actually works — rules, states and transitions, edge cases, and tunables — by deriving the system list from Docs/design-doc.md and interviewing the developer one system at a time (player controller, combat, enemies, progression, whatever the game or reusable asset has), writing one spec per system under Docs/specs/ plus Docs/spec-index.md. Use after a design doc exists, when the developer says things like "spec out the player controller," "define the combat rules," "hammer out the mechanics," "how should dash work," or wants system behavior pinned down before building it. Specs lock rules and intent, never numbers. Not for concept ideation or project scaffolding (game-docs-setup) and not for ongoing pass work (game-docs-resume).
---

# Game Docs: System Specs

The design doc says what the game is. This skill pins down how each system behaves, so rules are decided once instead of rediscovered mid-pass and lost between sessions. There is no fixed question bank because the systems are game-specific; instead, `references/lenses.md` holds a fixed set of game-agnostic **lenses**, and the questions are generated per system by applying them to this game's design doc.

Specs are **optional** and independent of the plan/progress/current-state/technical-design set: they are never a conformance gate, and their absence never blocks anything. The one hard rule is **rules and intent are locked, numbers are not.** Numeric values go in a Tunables table as placeholders marked for playtesting.

## Preflight

Requires `Docs/design-doc.md`. If it doesn't exist, stop and tell the developer to run `game-docs-setup` first; don't spec a game that has no design doc. Nothing else is required: no conformance check, no scaffolded B7 set.

## Flow

### 1. Find the systems

Read `design-doc.md` (core loop, mechanics, player-experience sections). If `Docs/spec-index.md` already exists, read it and continue from where it leaves off instead of re-deriving the list.

Otherwise propose a candidate system list grounded in the design doc, for example, for a platformer roguelike: player controller, combat, enemies, run progression, level generation, economy. These are suggestions: the developer confirms, edits, adds, or drops. Write the list into `Docs/spec-index.md` with status `Not started` (shape in `references/spec-templates.md`).

Offer to start with the system the next milestone builds (read `plan.md` and `current-state.md` if they exist); otherwise the developer picks.

### 2. Interview one system at a time

Walk the lenses in `references/lenses.md` in order, generating questions specific to this game and system. Ground them in the design doc and in any specs already written, and skip anything already answered. Ask a couple of questions at a time, conversationally, not as a recited list or all lenses at once.

Write answers into `Docs/specs/<system>.md` (shape in `references/spec-templates.md`) as they firm up. Don't wait until the end of the interview to write; the file is the record.

For an `asset` project (read `Project type` from the CLAUDE.md block; a missing line means `game`), also apply lens 10, Public API & configuration, and treat the Tunables table as the settings the package exposes, with their shipped defaults. Write the exposed surface into the spec's optional Public API section. Systems in a `game` project can use lens 10 too when other code or designers will build on them, but don't push it.

### 3. Rules, not numbers

When an answer contains a number ("coyote time about a tenth of a second"), record the rule ("the player may jump briefly after leaving a ledge") and put the value in the Tunables table as a placeholder with the `Playtest` flag. Anything the developer doesn't know yet goes in Open Questions. Never invent an answer to fill a gap.

### 4. Mark status

Per system in `spec-index.md`:
- **Draft**: interview in progress, or Open Questions remain that affect the rules.
- **Hammered**: rules settled; only tunables and playtest items remain.

The developer decides when a spec is Hammered. Update the index when it changes. Never mark a spec Hammered unprompted.

### 5. Cross-system pass

Once two or more systems are Draft or Hammered, ask about the seams between them: does combat lock movement, does the economy touch level generation, what happens to a status effect when the player changes state. Seams are the most common source of design bugs. Record each interaction in **both** systems' specs.

### 6. Offer next

Ask whether to spec another system now or move on. Never push for completeness; a Draft spec for the system the next milestone builds is worth more than a perfect spec for everything.

## Keeping specs honest

Specs are updated by this skill when the developer asks, and by `game-docs-resume` when an implementation deviates from a spec (it adds a Changelog line). Specs never record pass status; `current-state.md` owns that.

## Reference files

- `references/lenses.md`: the fixed lens set and how to turn each into game-specific questions.
- `references/spec-templates.md`: exact shape of `Docs/spec-index.md` and each `Docs/specs/<system>.md`.
