# Spec Lenses

A game's systems can't be predicted, so there is no fixed question list here. Instead, these are the **lenses** to apply to any system. For each system being specced, walk the lenses in order and generate questions that are specific to *this* game, using `design-doc.md` and any already-written specs as context. The question shapes below are illustrations of the lens, not a script: rewrite them in the game's own vocabulary.

Rules for using lenses:
- **Skip what's already answered.** If the design doc or an existing spec settles a lens for this system, record it and move on. Never re-ask.
- **A couple of questions at a time**, conversationally. Not a recited list, not all lenses in one message.
- **Never invent answers.** If the developer doesn't know yet, it goes in Open Questions.
- **Not every lens applies to every system.** A scoring system may have no "Feedback & feel" worth speccing yet; say so and skip it rather than padding.

## 1. Inputs & controls

What triggers this system, and how does the player (or another system) drive it? Which inputs, which devices, hold vs tap vs release, buffering, remapping.

- Platformer controller: "Is jump variable-height (hold longer = higher)? Is there input buffering before landing?"
- Deck/inventory UI: "Which inputs select, confirm, and cancel, and do they differ on controller vs mouse?"

## 2. States & transitions

What distinct states can this system be in, and what moves it between them? Include cancels, priorities, and what is interruptible.

- Combat: "Can an attack be cancelled into a dash? Into a jump? What is never cancellable?"
- Enemy AI: "What are the states (idle, alert, attack, stunned), and what interrupts each?"

## 3. Rules & formulas

The actual logic: what happens, in what order, under what conditions. Capture the rule; leave the numbers for the Tunables lens.

- Platformer controller: "When two inputs conflict (left and right), which wins? Does air control differ from ground control?"
- Economy: "When is currency granted, and can it be lost on death?"

## 4. Tunables

Which values will need playtesting? List the parameter names and a placeholder, with a `playtest` flag. Do not lock numbers here.

- "What are the knobs: run speed, jump height, coyote time, dash distance, i-frame length?" (names first; values are placeholders)
- "Which of these should be exposed to the designer in-editor for live tuning?"

## 5. Edge cases & exploits

What breaks, what could be abused, what happens at boundaries.

- Platformer controller: "What happens if the player dashes into a wall in the same frame they jump? Can wall-jumps be chained infinitely?"
- Run generation: "Can a seed produce an unwinnable layout, and what's the fallback?"

## 6. Feedback & feel

What the player sees, hears, and feels when this system acts. Visual, audio, haptic, camera, hit-stop, screen-shake. Only spec what matters for the feel target in the design doc.

- Combat: "What is the hit-confirm feedback: hit-stop, flash, sound, screen-shake?"
- Level-up: "What does the moment of gaining a level look and sound like?"

## 7. Interactions with other systems

What does this system read from or change in others? These are the seams, and the most common source of design bugs. Record each interaction in *both* systems' specs.

- "Does combat lock movement while an attack is winding up?"
- "Does the economy touch level generation (e.g. richer rooms deeper in)?"

## 8. Failure & recovery

What happens when the player fails or the system can't do its job, and how does the player recover?

- "What happens on death: lose everything, keep some currency, restart the room or the run?"
- "If an enemy can't path to the player, what does it do?"
- Reusable character controller: "If the user forgets a required component or assigns the wrong layer, does it log a clear error, auto-fix, or fail silently?"

## 9. Scope tags

Which parts are needed for the current phase, and which are later? This keeps the spec from demanding everything at once and lets `game-docs-resume` scope passes against it.

- "Which of these rules does the Prototype phase need, and which can wait for MVP or later?"

## 10. Public API & configuration

Mainly for `asset` projects (reusable packages), and useful for any system other code or designers will build on. What does this system expose, and what is kept internal? Treat shipped defaults as part of the design.

- Reusable character controller: "Which settings are exposed in the inspector, and what are their shipped defaults? Which are only settable from code?"
- Any reusable system: "Which public methods and events can users depend on, which extension points exist (override, subscribe, replace), and what changes would count as breaking in a later version?"
- "Is configuration a component, a ScriptableObject asset, or code, and can users swap it at runtime?"
