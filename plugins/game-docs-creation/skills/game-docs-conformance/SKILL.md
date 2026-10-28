---
name: game-docs-conformance
description: Audits whether a Unity/Unreal project's Docs/ files (design-doc.md, plan.md, progress.md, current-state.md, technical-design-doc.md) actually follow the game-docs-creation skill family's structure, and migrates any that don't — retiring the non-conforming file to Docs/legacy/ and rebuilding it from its own content rather than editing it in place. Use this whenever a developer says their docs don't follow the expected structure, asks to "audit," "check," or "fix" their project docs, mentions a monolithic technical-design-doc.md, an unstructured plan.md, or a progress.md/current-state.md that overlap or got merged. Also invoked automatically as a preflight by game-docs-setup and game-docs-resume, and dispatched here by game-docs-creation's Step 0 routing whenever it detects a conformance failure — if you were dispatched here, report back to the developer once migration finishes and tell them to re-invoke whichever skill they actually wanted.
---

# Game Docs: Conformance & Migration

Checks whether existing docs in `root/Docs/` match the structure the `game-docs-creation` skill family expects, and migrates the ones that don't. Never edits a non-conforming file in place — always retires it to `Docs/legacy/` first and rebuilds fresh, so the developer can diff old against new.

## Checking conformance

Don't read every doc in full on every invocation — that's wasted cost on the common case where everything already matches this skill's structure. Instead:

1. **Cheap structural pass, once per project.** Only for files that are actually present, and only if the CLAUDE.md block doesn't already say `docs-conformant: true`. Grep-level checks, not full reads: does `plan.md` have phase/milestone/pass headers? Does `technical-design-doc.md` read as an index — a `## Systems` section linking out to separate per-system files under `Docs/technical-design/` (per `references/doc-templates.md`) — rather than one undivided dump of prose/code sitting directly in the file? If both `progress.md` and `current-state.md` exist, are they structurally distinct (different header sets) rather than one's content duplicated into the other? If only one of the two exists, don't wave it through by default — check whether it reads as carrying live/active status (language like "currently," "in progress," "next up," a named active task) rather than pure frozen history; if so it's silently playing both roles and fails conformance even though its sibling is merely absent, not overlapping.
2. **Full read only on suspicion.** If the structural pass flags a file, read it in full to confirm, then run migration ([below](#migrating-a-non-conforming-doc)) for that file only — not the whole doc set.
3. **Cache the verdict.** Once every present doc conforms (fresh scaffold, or after migration finishes), write `docs-conformant: true` into the CLAUDE.md block (template in `references/doc-templates.md`). Callers can then skip invoking this skill entirely on future invocations. If the developer mentions hand-editing a doc outside the skill, redo the structural pass for that file only and update the flag accordingly.

`design-doc.md` gets a lighter check: it's non-conforming only if a `game-docs-setup` invocation would find it neither complete nor recognizably incomplete (placeholders) — i.e. structured in a way that doesn't map to the categories of the question bank for the project's type at all (held by `game-docs-setup`; see the design-doc template in `references/doc-templates.md`). A design doc that's simply missing answers (has `*Not yet discussed*` placeholders or similar) is **not** a conformance failure — that's `game-docs-setup`'s normal gap-fill case, not a migration.

## Reporting the verdict

When invoked directly by a developer, or as a preflight by another skill: report which files (if any) failed, then proceed straight into migration for those files without waiting for a separate go-ahead — the developer already asked for this by invoking the skill (directly or by hitting a failed preflight). When dispatched here by `game-docs-creation`'s router, do the same, then tell the developer which skill to re-invoke once migration is done (the router will route them correctly again, or they can name it directly).

## Migrating a non-conforming doc

Reached when a doc exists but doesn't follow this skill family's structure — e.g. a `technical-design-doc.md` that's a monolithic dump instead of split per-system, a `plan.md` that doesn't follow phase → milestone → pass → sub-pass, a `progress.md`/`current-state.md` that are merged or overlapping instead of "frozen history" vs. "live snapshot," or a `design-doc.md` whose sections don't map to this skill's shape at all. This is a one-time migration per file, not an in-place rewrite — **never edit the non-conforming file directly.** Instead:

1. Move the existing file, unchanged, to `Docs/legacy/<original filename>` (create the folder if needed). It stays there as-is — a diffable, referenceable record of what was there before.
2. Create a fresh file at the skill family's canonical path (e.g. `Docs/plan.md`) and populate it by migrating the old content across, mapped onto the target structure in `references/doc-templates.md`.
3. Ask the developer to confirm anywhere the mapping is ambiguous (content that could belong in more than one section, or a boundary — like where one milestone ends and the next begins — that isn't explicit in the source). Never silently drop content that doesn't have an obvious home; ask where it goes, or park it in a clearly-labeled section for the developer to sort later.
4. Add a one-line HTML comment at the top of the new file pointing at its `Docs/legacy/` counterpart, so anyone opening it cold knows a pre-migration version exists to cross-check against.

This lets the developer diff old against new and dig up anything the migration missed, rather than trusting a silent in-place rewrite.

Per doc type:
- **`design-doc.md`** — retire it to `Docs/legacy/`, create a fresh `design-doc.md`, and hand off to `game-docs-setup` to run its gap-fill flow against the old file's content as the source instead of a blank slate.
- **`plan.md`** — extract whatever phase/milestone structure is recognizable from the retired file; ask the developer to confirm boundaries and completeness rather than inventing them; write the new `plan.md` following the phase → milestone → pass template.
- **`technical-design-doc.md`** — propose a per-system split based on whatever structure the retired file has (headers/sections/comments), or, for undivided prose, on the systems/classes/subsystems it actually names; confirm the split with the developer before committing to it; write each system out as its own file under `Docs/technical-design/`, and rewrite `technical-design-doc.md` itself as the index (a `## Systems` section linking to each) per the template — not as sections within one file.
- **`progress.md` + `current-state.md`** — if both exist but overlap, or a single file covers both roles, retire whichever file(s) hold the mixed content; separate live, in-flight status (which pass is active, what's next) into a fresh `current-state.md`'s live-snapshot shape, and completed/frozen milestone history into a fresh `progress.md`'s table; ask the developer to confirm the split wherever it's not obvious which bucket something belongs in.

Once a file's migration is done, re-run its conformance check on the new file (it should now pass) before updating the `docs-conformant` flag.

## Reference files

- `references/doc-templates.md` — the exact target structure for every doc, plus the CLAUDE.md block template (for the `docs-conformant` flag).
