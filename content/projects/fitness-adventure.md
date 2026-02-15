---
title: "Why I Built a Fitness RPG"
date: 2026-02-15
draft: false
tags: ["swiftui", "architecture", "personal-projects", "fitness"]
categories: ["Projects"]
description: "Turning workouts into structured progression."
---

I’ve been building a small iOS app called **Fitness Adventure**.

It started as a simple thought experiment:

What if workouts weren’t just logged — but *structured*?

Not streaks.  
Not badges.  
Not dopamine slot machines.

But progression.

---

## The Idea

I’ve always been drawn to systems that reward consistency:

- leveling curves  
- unlock trees  
- quest progression  
- structured advancement  

Games do this incredibly well.

Fitness, oddly, often doesn’t.

So I started building a SwiftUI app where:

- Workouts generate XP  
- XP increases level  
- Levels unlock quests  
- Quests trigger encounters  
- Encounters reinforce effort  

It’s not meant to be flashy. It’s meant to be coherent.

---

## Why I’m Actually Building It

On the surface, it’s a fitness app.

Underneath, it’s about structure.

I like building systems where effort maps cleanly to outcome.  
Where rules are explicit.  
Where progression is earned.

It’s the same reason I enjoy writing simulation code or designing reliability models:  
well-defined inputs → deterministic rules → observable output.

A fitness RPG is just that idea, translated into a personal domain.

---

## Architecture Matters (Even for a Side Project)

Before expanding features, I paused and cleaned the entire repository:

- Renamed the project properly  
- Normalized folder structure  
- Separated rule logic from UI  
- Added CI build checks  
- Cleaned Git history and branching  

It sounds small. It isn’t.

Entropy compounds.

If structure decays early, everything slows down later.

The game logic now lives separately from SwiftUI views.  
Combat rules don’t know about UI.  
Progression math doesn’t depend on navigation stacks.

That separation makes the system feel stable.

---

## What I’m Learning

- Clean structure reduces mental friction.
- Git discipline matters even for solo work.
- Game systems and engineering systems aren’t that different.
- Progression curves are harder to balance than they look.

Most importantly:

It’s easier to maintain motivation when the tool itself reflects intention.

---

## Try the Beta

If you're curious, the current beta build is available via TestFlight:

👉 **[Join the Fitness Adventure Beta](https://testflight.apple.com/join/sXky1tGj)**

A few notes:

- iOS only
- Expect rough edges
- Progression balance is still evolving
- Feedback is welcome

This is an experimental build — not a polished App Store release.


---

## What’s Next

“Build 3” focuses on:

- Expanding artifact quest logic  
- Refining progression pacing  
- Improving encounter balance  
- Adding lightweight rule tests  

No hype.  
Just steady iteration.

---

This project isn’t commercial (at least not right now).

It’s an experiment in aligning effort with structure.

And honestly, I’m enjoying building it more than I expected.
