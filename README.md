# game-docs-creation

Claude Code plugin marketplace for the `game-docs-creation` plugin — a family of four skills that manages the full project-documentation and phase/milestone/pass workflow for Unity and Unreal game projects: creating and maintaining `Docs/design-doc.md`, `plan.md`, `progress.md`, `current-state.md`, and a system-organized `technical-design-doc.md`, keeping a managed section of `CLAUDE.md` current so every fresh session knows what to read, and enforcing git hygiene (one branch per milestone, manual verification before any commit, caveman-style commit messages).

- **`game-docs-creation`** — the entry point. Figures out which stage a project is in and dispatches to one of the three skills below. Call this whenever you're not sure which applies.
- **`game-docs-setup`** — turns an idea into `design-doc.md`, then scaffolds the rest of the docs for a new project.
- **`game-docs-resume`** — day-to-day work on an already-scaffolded project: picking up where you left off, scoping the next milestone/phase, and commit hygiene.
- **`game-docs-conformance`** — audits and migrates docs that exist but don't follow this plugin's structure (a monolithic tech-design doc, an unstructured plan, merged progress/current-state), retiring the old file to `Docs/legacy/` rather than editing it in place.

## Install

```
/plugin marketplace add Triple-A-Studios/game-docs-creation
/plugin install game-docs-creation@triple-a-studios
```
