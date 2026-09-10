# Game Design Question Bank (repo-bound fork)

Forked from `game-ideation`'s question bank as a starting point, then adapted for a Claude Code, single-repo context. This copy is independent from here on — improvements to one don't propagate to the other, which is intentional (this skill shouldn't depend on `game-ideation` being installed).

Each question is tagged **[T1]**, **[T2]**, or **[T3]**. SKILL.md's light pass (A2) pulls only T1 from three categories; the optional deeper pass (A3) pulls the rest, same covered/not-covered logic as `game-ideation`. Categories marked *(conditional)* only apply under certain conditions, noted below their heading.

## Table of contents

1. Core Concept & Hook
2. Genre, Platform & Format
3. Core Gameplay Loop & Mechanics
4. Player Experience, Emotion & Fantasy
5. Target Audience & Market Context
6. Narrative, Setting & Tone
7. Progression, Difficulty & Retention
8. Art & Audio Direction
9. Multiplayer & Social Design *(conditional: non-single-player only)*
10. Scope, Technical Constraints & Team
11. Business Model & Monetization *(conditional: skip for personal/hobby/non-commercial projects)*
12. Success Criteria & Definition of Done

---

## 1. Core Concept & Hook

- [T1] In one sentence, what does the player actually *do*, moment to moment?
- [T1] What's the single hook that makes this idea worth building over every other game like it?
- [T2] If you had to cut everything except one mechanic or moment, what's the one thing that must survive?
- [T2] What existing games is this closest to, and what's the one thing you're doing differently?
- [T3] What's the most likely reason someone would bounce off this in the first five minutes — and what's your answer to that?

## 2. Genre, Platform & Format

*(Engine is already known from the repo — don't ask it. Platform/format may still be open.)*

- [T1] ~~Confirm engine~~ — pre-filled from repo detection (Unity/Unreal); confirm platform(s) and player mode instead.
- [T2] Is this a full release, a jam game, a prototype, or something else — and does that change what "done" looks like? (This overlaps with the phase breakdown in Path B — a quick recap here is enough, don't over-invest.)
- [T3] Are there platform-specific constraints (input method, screen size, performance ceiling) that should shape the design from the start?

## 3. Core Gameplay Loop & Mechanics

- [T1] What does the player do in the first 30 seconds, and what do they do minute-to-minute after that?
- [T1] What's the core loop (the thing the player repeats over and over), and why does it stay fun on the 50th repetition?
- [T2] What are the primary verbs/actions available to the player? Which one is the most important?
- [T2] What are the win and lose conditions, at both the moment-to-moment level and the whole-game level?
- [T3] Where are the mechanics in tension with each other, and how do you resolve that tension?
- [T3] What's the most complex interaction between systems, and is a player expected to discover or be taught it?

## 4. Player Experience, Emotion & Fantasy

- [T1] What should the player *feel* while playing — and is that the same as what they feel afterward?
- [T1] What power fantasy, curiosity, or itch is this scratching?
- [T2] What's the emotional arc across a single session? Across the whole game?
- [T3] Where in the experience are you deliberately making the player uncomfortable, frustrated, or tense — and is that intentional friction or a risk to smooth out?

## 5. Target Audience & Market Context

- [T1] Who is this for? (Be specific — not "everyone.")
- [T2] What games does your target player already love, and what do they wish those games did differently?
- [T2] What's your closest comparable/competitor, and why would someone pick your game over it?
- [T3] Is there an audience segment you're explicitly *not* designing for? What are you willing to sacrifice for them to lose?

## 6. Narrative, Setting & Tone

- [T1] Is there a story, or is this systems/mechanics-first? (Both are valid — just be explicit.)
- [T2] What's the tone (funny, grim, whimsical, tense, cozy...) and where might mechanics and tone pull against each other?
- [T2] How is narrative delivered — cutscenes, environmental storytelling, dialogue, none at all?
- [T3] Does the world/setting need to support future content (sequels, DLC, updates), or is it self-contained?

## 7. Progression, Difficulty & Retention

- [T1] How does the player get stronger, unlock new content, or otherwise feel progress?
- [T2] How does difficulty scale, and is there player-facing difficulty choice?
- [T2] What keeps someone playing past the first hour? Past the first week?
- [T3] Is there a risk of the core loop feeling repetitive or grindy by the midpoint — and what's the plan against that?
- [T3] What's the intended length of a full playthrough, and does the content you've planned actually support that?

## 8. Art & Audio Direction

- [T1] What's the visual style, in a few reference points or comparisons?
- [T2] Does the art style serve the gameplay (readability, feedback clarity) or is it purely aesthetic?
- [T2] What role does audio/music play — ambient, reactive, central to the experience?
- [T3] Are there art or audio requirements that are technically or budget-risky (e.g., heavy animation, full voice acting)?

## 9. Multiplayer & Social Design *(conditional)*

*(Include only if the game is co-op, competitive multiplayer, or otherwise social. Skip entirely for confirmed single-player games.)*

- [T1] Co-op, competitive, or both? Symmetric or asymmetric?
- [T1] What's the ideal player count, and what happens outside that range (e.g., solo queue, uneven teams)?
- [T2] How do players interact — direct combat, indirect competition, shared objectives, pure social space?
- [T3] What's your plan for matchmaking, toxicity/moderation, and griefing risks, if relevant?
- [T3] Does the game hold up if the multiplayer population is small (a real risk for most indie multiplayer games)?

## 10. Scope, Technical Constraints & Team

- [T1] Who's building this (solo, small team, size), and roughly how much time/budget is realistic?
- [T2] ~~What engine/tools~~ — pre-filled from repo detection. Ask instead: any major plugins, frameworks, or third-party packages already assumed?
- [T2] What's the single biggest technical risk (a system you're not sure how to build yet)?
- [T3] If you had to cut scope by 50% to ship, what goes first, and what's the untouchable core?
- [T3] What's your plan for the "boring but necessary" parts — save systems, settings menus, accessibility, tutorialization?

## 11. Business Model & Monetization *(conditional)*

*(Skip for personal, hobby, jam, or explicitly non-commercial projects — say so in the doc rather than asking these.)*

- [T1] Premium, free-to-play, subscription, or something else?
- [T2] If free-to-play, what's monetized, and does that create pressure against good design (pay-to-win, artificial friction)?
- [T3] What's the plan for post-launch support — is this a one-and-done release or a live game?

## 12. Success Criteria & Definition of Done

- [T1] What does success look like for *you* — creative satisfaction, commercial performance, portfolio piece, something else?
- [T2] How will you know, during development, if the core idea isn't working?
- [T3] What would make you kill or majorly pivot this project, and what would make you double down?
