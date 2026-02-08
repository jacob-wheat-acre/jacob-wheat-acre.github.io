---
title: "WorkDone (J)"
date: 2026-02-08
draft: false
summary: "A minimalist iOS workout journal that tracks mechanical work using physics."
---

## Overview
Most workout apps focus on calories or abstract scores.  
WorkDone (J) takes a different approach: it treats lifting as **mechanical work**, measured in **Joules**, using a simple model from classical physics.

The goal is not to estimate energy burned, but to provide a **consistent, cumulative measure of physical effort** over time.

The app is intentionally minimal. It remembers what you lifted last time, encourages small increases, and avoids unnecessary gamification.

## How It Works
Each workout day (e.g. Push Day, Leg Day) contains a persistent journal of exercises:
- Exercise name
- Weight
- Reps

When a workout is completed, the current values are timestamped and saved to history. The next session starts from the previous values, making progressive overload the default behavior.

The app also includes a built-in library of ~100 common, Google-friendly exercises grouped by muscle group, with support for custom user-defined exercises.

## The Physics
In physics, work is defined as:

W = ∫F · dx

For lifting, force is approximated as mass times gravity.  
WorkDone (J) uses the following model:

Work (J) = weightKg × 9.81 × 0.5 × reps × 3

Assumptions:
- Gravity g = 9.81 m/s²  
- Average vertical displacement per rep = 0.5 m  
- Three sets assumed for consistency  

This is an approximation, but it is applied consistently, which makes it useful for tracking progress over time.

An in-app **About** screen explains the assumptions and math in plain language.

## Energy Intuition
Because Joules are not an everyday unit, the app translates total work into familiar equivalents:
- Charging a phone (times)
- Powering an LED bulb (hours)
- Running a laptop (hours)
- Boiling water (liters)
- Fractions of a household’s daily electricity
- Seconds of output from a utility-scale solar plant

These comparisons are grounded in real energy values and are meant to build intuition, not hype.

## Tech
- SwiftUI
- SwiftData
- Apple Charts
- Local-only data (no accounts, no tracking)

## Status
The app is currently distributed via **TestFlight**.

## Links
- Beta: TestFlight (link): *requires the TestFlight app*
