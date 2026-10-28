# Asset Design Question Bank (reusable packages and plugins)

For projects whose `Project type` is `asset`: a reusable package, tool, or plugin that other developers install into their own projects (for example a character controller, a dialogue system, an editor tool). The audience is developers, so these questions replace the game bank's hook, player-fantasy, and genre questions. The game bank (`question-bank.md`) is untouched and still used for `game` projects.

Each question is tagged **[T1]**, **[T2]**, or **[T3]**. SKILL.md's light pass (A2) pulls only T1 from categories 1 to 4; the optional deeper pass (A3) pulls the rest, same covered/not-covered logic as the game bank. Categories marked *(conditional)* only apply under certain conditions, noted below their heading.

## Table of contents

1. Core Concept & Value
2. Target Developer & Market
3. Features, Scope & Non-goals
4. Public API & Extensibility
5. Compatibility
6. Integration & Onboarding
7. Quality, Performance & Support
8. Packaging & Distribution
9. Success Criteria & Definition of Done

---

## 1. Core Concept & Value

- [T1] In one sentence, what problem does this solve for the developer who installs it?
- [T1] What would a developer otherwise do instead (build it themselves, use an existing asset), and why is this worth choosing?
- [T2] If you had to cut everything except one capability, what's the one thing it must do well?
- [T3] What's the most likely reason a developer would try this, then drop it in the first hour?

## 2. Target Developer & Market

- [T1] Who is this for? (Be specific: solo indies, small teams, a particular genre, beginners or experienced programmers.)
- [T2] What's the closest existing asset or built-in Unity/Unreal feature, and what do you do better or differently?
- [T2] How much does the user need to know to get value from it: drop-in and works, or they're expected to read and extend code?
- [T3] Is there a type of developer you're explicitly not designing for, and what are you willing to give up for that?

## 3. Features, Scope & Non-goals

- [T1] What are the headline features for the first release?
- [T2] What is deliberately out of scope, so users don't expect it?
- [T2] Which features are must-have for release and which can wait for a later version?
- [T3] If you had to cut scope by half to ship, what goes first, and what's the untouchable core?

## 4. Public API & Extensibility

- [T1] How does a developer use it: add components in the editor, call a code API, configure ScriptableObjects, or a mix?
- [T2] What can users customize or override, and what is deliberately closed?
- [T2] Which events, callbacks, or extension points do they need to hook into their own game logic?
- [T3] What's your policy on breaking API changes between versions, and how will users be warned?

## 5. Compatibility

- [T1] Which engine versions will you support (minimum, and any maximum or tested range)?
- [T2] *(Unity)* Which render pipelines (Built-in, URP, HDRP)? Old Input Manager, new Input System, or both? *(Unreal)* Which engine versions and platforms?
- [T2] Which platforms and scripting backends must it work on (including IL2CPP or AOT-restricted targets, if relevant)?
- [T2] What does it depend on (other packages, third-party code), and can you avoid dependencies?
- [T3] How will you namespace and isolate the code (assembly definitions, namespaces) so it doesn't collide with the user's project?

## 6. Integration & Onboarding

- [T1] What does the first five minutes look like for a new user: install, then what?
- [T2] Will there be a sample scene, a setup wizard, or both? What should the sample demonstrate?
- [T2] What documentation is needed (quick-start, API reference, troubleshooting), and where does it live?
- [T3] Does any sample content (art, audio, models) carry its own license that affects how you can ship or let users reuse it?

## 7. Quality, Performance & Support

- [T1] How will you test it (automated tests, a clean-project check, manual passes), and across which configurations?
- [T2] What's the performance budget (per-frame cost, allocations, editor impact), and how will you measure it?
- [T2] How will you version releases and record changes (semantic versioning, a changelog)?
- [T3] How will users report problems or ask for help, and what's your plan for updates and bug fixes after release?

## 8. Packaging & Distribution

- [T1] How will this be distributed: a git package (public or private repo), a store listing (Asset Store, Fab), or more than one?
- [T2] *(Git package)* How do users install and pin versions, and how do they update? *(Private repo)* How do consumers get access?
- [T2] What is the package structure (manifest, runtime vs editor code, tests, samples, documentation)?
- [T3] *(Conditional: store channel)* What are the listing materials, pricing, and category? Skip entirely for git-only distribution and say so in the doc rather than asking.
- [T3] What license applies to your code, and does anything third-party constrain it?

## 9. Success Criteria & Definition of Done

- [T1] What does "released" mean for you: tagged in a repo, accepted on a store, used by a particular project, something else?
- [T2] How will you know during development whether the asset is actually useful to someone other than you?
- [T3] What would make you stop or majorly change direction, and what would make you invest in more versions?
